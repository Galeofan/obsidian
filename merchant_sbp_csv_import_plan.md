# План реализации поэтапно: импорт ЮЛ и ТСП из CSV через FTP в merchant-sbp

Контекст:
- Входящие CSV-файлы приходят на FTP в разные директории: `/home/gate_ftp/MERCHANT_SBP/IN/LEGAL` и `/home/gate_ftp/MERCHANT_SBP/IN/MERCHANT`
- Маски (без учёта регистра): `legal.csv`, `merchant.csv`
- CSV: UTF-8, `;`, header обязателен, кавычки/экранирование разрешены, пустых строк нет
- Валидация: **все поля обязательны**, пустота определяется как `value.trim().isEmpty() == true`
- Ключи:
  - `legal_entity` уникален по `acquirer_entity_id` (varchar, **без лидирующих нулей**)
  - `merchant` уникален по `(acquirer_merchant_id, legal_entity_id)` (оба varchar/uuid в терминах CSV; `legal_entity_id` — UUID)
- Поведение на дубль по ключу: **SKIP**, без upsert
- Если `merchant.legal_entity_id` не найден → **ошибка строки**
- Контент файла в БД хранить не нужно: после обработки файл переносится в `/home/gate_ftp/MERCHANT_SBP/PROCESSED` или `/home/gate_ftp/MERCHANT_SBP/FAILED`
- Нужны таблицы:
  - реестр файлов (статус/метаданные/счётчики)
  - ошибки строк (с `raw_line`)
- Статусы должны быть расширены: добавить `COMPLETED_WITH_ERRORS`
- Проект использует **Flyway**
- Работает один разработчик, задача в Jira одна — ниже план реализации внутри неё.

---

## Этап 0. Подготовка (1 раз, до кода)
**Результат:** фиксируем контракт и точки интеграции, чтобы не переделывать.

1. Подтвердить список колонок для 2 шаблонов: +
   - `legal.csv` (включая `acquirer_entity_id`) - acquirer_entity_id;ext_entity_id;country_code;account;name
   - `merchant.csv` (включая `acquirer_merchant_id`, `legal_entity_id`) - legal_entity_id;acquirer_merchant_id;brand_name;mcc;country_code;country_sub_code;city;address;zip;phone;inn;tsp_url;sales_mode
2. Согласовать целевые имена директорий на FTP: +
   - вход: `/home/gate_ftp/MERCHANT_SBP/IN/LEGAL`, `/home/gate_ftp/MERCHANT_SBP/IN/MERCHANT`
   - выход для перемещения: `/home/gate_ftp/MERCHANT_SBP/PROCESSED`, `/home/gate_ftp/MERCHANT_SBP/FAILED` (общие)   
3. Зафиксировать правила переименования при коллизии в `/home/gate_ftp/MERCHANT_SBP/PROCESSED`: +
   - добавление timestamp к имени

---

## Этап 1. База данных (Flyway миграции)
**Цель:** создать таблицы для истории файла и ошибок строк. Без этого нельзя корректно вести процессинг.

### 1.1 Таблица реестра файлов
Создать таблицу (например `merchant_sbp.registry_file`) с полями:
- `id` serial/bigserial PK
- `file_name` varchar NOT NULL
- `entity_type` varchar NOT NULL (LEGAL / MERCHANT)
- `status` varchar NOT NULL (со значениями ниже)
- `upload_date` timestamptz NOT NULL default now()
- `updated_date` timestamptz NOT NULL default now()
- `lines_count` int NOT NULL default 0
- `lines_valid` int NOT NULL default 0
- `lines_invalid` int NOT NULL default 0
- `lines_skipped` int NOT NULL default 0
- `created_by` varchar NOT NULL default 'PHUB' (или заполнять из кода)
- `updated_by` varchar NOT NULL default 'PHUB'

Статусы:
- `UPLOADED`, `PARSING`, `PARSED`, `VERIFYING`, `VERIFIED`, `PROCESSING`,
- `COMPLETED`, `COMPLETED_WITH_ERRORS`, `FAILED`

### 1.2 Таблица ошибок строк
Создать таблицу (например `merchant_sbp.registry_file_error`) с полями:
- `id` bigserial PK (или составной ключ)
- `file_id` bigint NOT NULL FK → registry_file(id)
- `line_no` int NOT NULL
- `entity_type` varchar NOT NULL
- `error_code` varchar NOT NULL
- `error_message` varchar NOT NULL
- `raw_line` text NOT NULL
- `created_at` timestamptz NOT NULL default now()

Индексы:
- `(file_id, line_no)`
- при необходимости `(file_id)` отдельно

**DoD:**
- Flyway миграции применяются на чистой БД.
- Таблицы доступны репозиториям, FK работает.

---

## Этап 2. Конфигурация FTP и расширение клиента (download/list/move)
**Цель:** научиться читать входящие папки и перемещать файлы по результату.

1. Переиспользовать существующие настройки/клиент (который умеет upload отчётов) и добавить операции:
   - `listFiles(remoteDir, maskCaseInsensitive)`
   - `download(remotePath)` (stream → временный файл)
   - `move(remotePath, targetDir, collisionStrategy=timestampSuffix)`
2. Реализовать стратегию коллизии имени в `processed/`:
   - если файл существует, новое имя = `<base>_<yyyyMMddHHmmssSSS>.<ext>`
3. Настроить properties:
   - `ftp.in.legalEntityDir`
   - `ftp.in.merchantDir`
   - `ftp.processedDir` / `ftp.failedDir` (или по сущностям)
   - `ftp.mask.legalEntity`, `ftp.mask.merchant`
   - `ftp.parallelism` (кол-во параллельно обрабатываемых файлов)
4. Убедиться, что операции `rename/move` доступны на сервере FTP (обычно да).

**DoD:**
- Можно вручную (локальным тестом) скачать файл с FTP и переместить его в `processed/` с timestamp при коллизии.

---

## Этап 3. Poller входящих директорий + анти-дубли на уровне файла
**Цель:** автоматически запускать импорт при появлении файла. Никаких REST/ручных запусков.

1. Реализовать `FtpInboundPoller` (scheduled/loop):
   - отдельно для `/in/legal_entity/` и `/in/merchant/`
   - фильтрация по маске
2. Параллельная обработка файлов:
   - ExecutorService с фиксированным пулом (`ftp.parallelism`)
3. Анти-дубли:
   - при старте обработки создаём запись в registry (status `UPLOADED`)
   - если такая запись уже существует в non-terminal статусе (или по уникальному ключу на `file_name + entity_type + upload_date?`) — не стартовать повторно
   - рекомендуется: перед началом переместить файл во временную папку `processing/` (если возможно) или “захватить” записью в БД

**DoD:**
- Новый файл в FTP входной папке приводит к созданию записи в registry и запуску обработки.
- Один и тот же файл не обрабатывается одновременно двумя потоками.

---

## Этап 4. CSV parsing (streaming) + валидация header
**Цель:** корректно читать CSV и детектить несоответствие шаблону.

1. Выбрать библиотеку CSV (типично `commons-csv`):
   - delimiter `;`
   - header обязателен
   - quotes/escape включены
2. Реализовать схемы:
   - `LegalEntityCsvSchema`
   - `MerchantCsvSchema`
   которые содержат список колонок и маппинг.
3. Валидация header:
   - все колонки присутствуют
   - (рекомендуется) порядок строго совпадает со схемой
4. Пустые строки запрещены:
   - если встретили пустую строку → записать ошибку строки (`EMPTY_LINE`) и продолжить

**DoD:**
- Несоответствие header переводит файл в `FAILED` с понятным логом/ошибкой файла.
- Парсинг корректно работает с кавычками и `;`.

---

## Этап 5. Построчная валидация + запись ошибок
**Цель:** на каждую строку определить VALID/INVALID (и причину), не останавливая весь файл.

1. Правило пустоты:
   - `value == null || value.trim().isEmpty()` → invalid
2. Все поля из схемы обязательны:
   - любое пустое поле → invalid (`REQUIRED_FIELD_EMPTY`)
3. Проверка лидирующих нулей для `acquirer_entity_id` и `acquirer_merchant_id`:
   - если строка начинается с `'0'` → invalid (`LEADING_ZERO_NOT_ALLOWED`)
4. Для merchant:
   - `legal_entity_id` парсится как UUID
   - проверка существования ЮЛ:
     - если не найден → invalid (`LEGAL_ENTITY_NOT_FOUND`)
5. На каждую invalid-строку вставлять запись в `merchant_sbp_registry_file_error`:
   - `file_id`, `line_no`, `entity_type`, `error_code`, `error_message`, `raw_line`, `created_at`
6. Счётчики:
   - `lines_count` увеличивать на каждую data-строку
   - `lines_invalid` при любой invalid
   - `lines_valid/lines_skipped` — на этапе вставки в target-таблицы

**DoD:**
- Ошибки по строкам сохраняются с `raw_line`.
- Падение на одной строке не останавливает обработку файла.

---

## Этап 6. Загрузка в target-таблицы (insert + skip)
**Цель:** записать валидные строки, дубли пропускать.

### 6.1 legal_entity
1. Подготовить insert (JDBC / jOOQ / Spring Data — что используется в проекте)
2. Вставка:
   - `INSERT ... ON CONFLICT (acquirer_entity_id) DO NOTHING`
3. По результату:
   - если вставилась строка → `lines_valid++`
   - если не вставилась (conflict) → `lines_skipped++`
4. Проставить аудит:
   - `created_by/updated_by = 'PHUB'`

### 6.2 merchant
1. Проверку существования `legal_entity_id` желательно делать заранее:
   - либо запросом `select exists` на каждую строку (для тысяч строк допустимо)
   - либо одним запросом для множества UUID (опционально оптимизация)
2. Вставка:
   - `INSERT ... ON CONFLICT (acquirer_merchant_id, legal_entity_id) DO NOTHING`
3. Аналогично считаем valid/skipped
4. Аудит: `PHUB`

Транзакции:
- Рекомендуемое: **одна транзакция на файл**
- Вставка может быть батчами (например 200–500), но под общей транзакцией.

**DoD:**
- Повторная загрузка файла приводит к росту `lines_skipped`, а не к ошибкам.
- Для merchant при неизвестном `legal_entity_id` запись не создаётся, ошибка фиксируется.

---

## Этап 7. Машина статусов файла + финализация
**Цель:** корректно проставлять статусы, счётчики и завершать обработку.

1. Обновления статусов:
   - `UPLOADED` → `PARSING` → `PARSED` → `PROCESSING`
2. Финальный статус:
   - если процесс завершён и `lines_invalid == 0` → `COMPLETED`
   - если процесс завершён и `lines_invalid > 0` → `COMPLETED_WITH_ERRORS`
   - при исключении/системной ошибке → `FAILED`
3. `updated_date` обновлять на каждом переходе
4. Перемещение файла:
   - `COMPLETED` / `COMPLETED_WITH_ERRORS` → `processed/`
   - `FAILED` → `failed/`
   - при коллизии имени → timestamp suffix

**DoD:**
- Статус в registry всегда соответствует реальному исходу.
- Файл всегда уходит из входной директории.

---

## Этап 8. Интеграционные тесты и регресс
**Цель:** минимальный набор тестов, гарантирующий корректность.

1. Тесты парсинга:
   - delimiter `;`, quotes, header
2. Тесты валидации:
   - пустые значения после trim → invalid
   - лидирующий ноль → invalid
   - UUID parse для `legal_entity_id`
3. Тесты загрузки:
   - `ON CONFLICT DO NOTHING` → skipped
   - merchant с неизвестным `legal_entity_id` → invalid + error row
4. Тест “повторная загрузка файла”:
   - первый раз valid, второй раз skipped

**DoD:**
- Тесты выполняются в CI.
- Основные бизнес-правила подтверждены тестами.

---

## Этап 9. Эксплуатационные детали (логи/наблюдаемость)
**Цель:** чтобы поддержка могла быстро разбирать инциденты.

1. Логи на файл:
   - `fileId`, `entityType`, `fileName`, итоговые counters, финальный статус, время обработки
2. Логи на ошибку строки:
   - `fileId`, `line_no`, `error_code` (без лишних данных)
3. (Опционально) метрики:
   - количество файлов по статусам
   - длительность обработки

**DoD:**
- По логам понятно, что именно пошло не так и где смотреть ошибки (таблица ошибок).

---

## Рекомендуемый порядок выполнения (для одного разработчика)
1. Этап 1 (Flyway миграции)
2. Этап 2 (FTP client list/download/move)
3. Этап 4 (CSV parsing + header)
4. Этап 5 (валидация + error-table)
5. Этап 6 (insert+skip в legal_entity/merchant)
6. Этап 7 (статусы + перемещение файлов)
7. Этап 3 (poller + параллельность) — можно раньше, но удобнее после готовности процессора файла
8. Этап 8–9 (тесты, логи)

---

## Примечания по рискам
- **UUID** для `legal_entity_id` в merchant CSV: при неверном формате будет много invalid — обязательно лог/код ошибки `UUID_PARSE_ERROR`.
- “Все поля обязательны” + “частично пустые колонки допустимы” противоречат друг другу. В реализации принято правило: **пустые значения = invalid**, файл обрабатывается дальше.
- Параллельные файлы: важно ограничить пул и корректно “захватывать” файл, чтобы не стартовать дважды.

