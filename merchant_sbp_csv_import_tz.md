# Техническое задание: импорт ЮЛ и ТСП из CSV-файлов с FTP в сервисе merchant-sbp

Версия: v1
Технологии: **Java 17**, **Spring Boot 3.4.2**, **PostgreSQL**, миграции **Flyway**

---

## 1. Цель

Реализовать в сервисе `merchant-sbp` автоматическую загрузку данных по входящим **CSV-файлам**, поступающим на FTP:
- **Юридических лиц** в таблицу `merchant_sbp.legal_entity`
- **ТСП (merchant)** в таблицу `merchant_sbp.merchant`

Импорт должен:
- скачивать файл,
- построчно парсить и валидировать,
- сохранять валидные записи в целевые таблицы,
- дубли по ключам **пропускать**,
- ошибки строк **фиксировать** в отдельной таблице,
- вести **реестр файлов** со статусами и счётчиками,
- по итогам переносить файл в `PROCESSED` или `FAILED`.

---

## 2. Источник данных и FTP-конвенции

### 2.1 Директории
Входящие директории:
- LEGAL: `/home/gate_ftp/MERCHANT_SBP/IN/LEGAL`
- MERCHANT: `/home/gate_ftp/MERCHANT_SBP/IN/MERCHANT`

Выходные директории (общие):
- PROCESSED: `/home/gate_ftp/MERCHANT_SBP/PROCESSED`
- FAILED: `/home/gate_ftp/MERCHANT_SBP/FAILED`

### 2.2 Маски файлов (case-insensitive)
- LEGAL: `legal*.csv`
- MERCHANT: `merchant*.csv`

### 2.3 Поведение при коллизии имени в PROCESSED
Если в `/home/gate_ftp/MERCHANT_SBP/PROCESSED` уже существует файл с таким именем, новый файл должен быть сохранён с суффиксом timestamp:

`<base>_<yyyyMMddHHmmssSSS>.<ext>`

### 2.4 Перемещение после обработки
- При успешной обработке (включая случаи с ошибками строк) файл перемещается в `PROCESSED`.
- При системной ошибке обработки (например, FTP/CSV/DB) файл перемещается в `FAILED`.

---

## 3. Формат CSV

### 3.1 Общие требования
- Кодировка: **UTF-8**
- Разделитель: `;`
- Header: **обязателен**
- Кавычки и экранирование: **поддерживаются**
- Пустые строки: **не допускаются** (фиксируются как ошибка строки)

### 3.2 Правило пустоты
Значение поля считается пустым, если:
- значение отсутствует (`null`) **или**
- `trim()` даёт пустую строку

---

## 4. Шаблоны файлов

Шаблоны фиксированы в коде, порядок и имена колонок должны строго соответствовать header.

### 4.1 Шаблон LEGAL (`legal*.csv`)
Header (строго в этом порядке):
`acquirer_entity_id;ext_entity_id;country_code;account;name`

### 4.2 Шаблон MERCHANT (`merchant*.csv`)
Header (строго в этом порядке):
`legal_entity_id;acquirer_merchant_id;brand_name;mcc;country_code;country_sub_code;city;address;zip;phone;inn;tsp_url;sales_mode`

---

## 5. Валидация

### 5.1 Общие правила
- Любое нарушение валидации строки **не прерывает** обработку файла.
- Для каждой invalid-строки создаётся запись в таблице ошибок строк с `raw_line`.
- В счётчиках файла увеличивается `lines_invalid`.

### 5.2 Обязательность полей
- Для LEGAL: **все поля обязательны** (пустое значение → invalid).
- Для MERCHANT: все поля обязательны, **кроме**:
  - `phone`
  - `tsp_url`
### 5.3 Форматы и дополнительные проверки
#### acquirer_entity_id / acquirer_merchant_id
- Тип: `varchar`
- **Лидирующие нули разрешены**
#### legal_entity_id
- Тип: **UUID**
- Должен успешно парситься как UUID, иначе invalid (`UUID_PARSE_ERROR`).
- Должен существовать в таблице `merchant_sbp.legal_entity`, иначе invalid (`LEGAL_ENTITY_NOT_FOUND`).

---

## 6. Идемпотентность и правила вставки

### 6.1 legal_entity
- Уникальный ключ: `acquirer_entity_id` (уже задан в БД)
- Поведение при дубле: **SKIP**
- UPSERT/UPDATE: **запрещён**

Реализация: `INSERT ... ON CONFLICT (acquirer_entity_id) DO NOTHING`

### 6.2 merchant
- Уникальный ключ: `(acquirer_merchant_id, legal_entity_id)` (уже задан в БД)
- Поведение при дубле: **SKIP**
- UPSERT/UPDATE: **запрещён**

Реализация: `INSERT ... ON CONFLICT (acquirer_merchant_id, legal_entity_id) DO NOTHING`

### 6.3 Повторная загрузка того же файла
Допускается. Записи, уже присутствующие в БД по уникальным ключам, будут попадать в `lines_skipped`.

---

## 7. Таблицы для истории обработки

### 7.1 Таблица реестра файлов
Создать таблицу `merchant_sbp.registry_file`:

- `id` bigserial PK
- `file_name` varchar NOT NULL
- `entity_type` varchar NOT NULL  — значения: `LEGAL`, `MERCHANT`
- `status` varchar NOT NULL
- `upload_date` timestamptz NOT NULL default now()
- `updated_date` timestamptz NOT NULL default now()
- `lines_count` int NOT NULL default 0
- `lines_valid` int NOT NULL default 0
- `lines_invalid` int NOT NULL default 0
- `lines_skipped` int NOT NULL default 0
- `created_by` jsonb NOT NULL default '{"id":"PHUB"}'::jsonb
- `updated_by` jsonb NOT NULL default '{"id":"PHUB"}'::jsonb

#### Статусы обработки
Список статусов (varchar + check constraint):
- `UPLOADED`
- `PARSING`
- `PARSED`
- `VERIFYING`
- `VERIFIED`
- `PROCESSING`
- `COMPLETED`
- `COMPLETED_WITH_ERRORS`
- `FAILED`

Правило финального статуса:
- если файл обработан и `lines_invalid == 0` → `COMPLETED`
- если файл обработан и `lines_invalid > 0` → `COMPLETED_WITH_ERRORS`
- при системной ошибке → `FAILED`

### 7.2 Таблица ошибок строк
Создать таблицу `merchant_sbp.registry_file_error`:

- `id` bigserial PK
- `file_id` bigint NOT NULL FK → `merchant_sbp.registry_file(id)`
- `line_no` int NOT NULL
- `entity_type` varchar NOT NULL  — `LEGAL` / `MERCHANT`
- `error_code` varchar NOT NULL
- `error_message` varchar NOT NULL
- `raw_line` text NOT NULL
- `created_at` timestamptz NOT NULL default now()

Индексы:
- `(file_id, line_no)`

---

## 8. Алгоритм обработки файла (pipeline)

### 8.1 Обнаружение файла
Сервис периодически сканирует входные директории FTP и для каждого подходящего файла запускает обработку (с ограничением параллелизма).

### 8.2 Создание записи в registry
При старте обработки:
1. создать запись в `registry_file` со статусом `UPLOADED`,
2. сохранить `file_name`, `entity_type`.

### 8.3 Парсинг и валидация
1. Статус `PARSING`
2. Считать CSV streaming:
   - валидировать header
   - на каждую строку:
     - увеличить `lines_count`
     - валидировать поля
     - при invalid: записать в `registry_file_error`, увеличить `lines_invalid`
3. Статус `PARSED`

### 8.4 Загрузка валидных строк
1. Статус `PROCESSING`
2. Вставлять валидные строки в целевые таблицы:
   - при успешной вставке → `lines_valid++`
   - при conflict/duplicate → `lines_skipped++`

Транзакционность:
- допустимо выполнять в **одной транзакции на файл**.
- допускается batch insert (200–500 записей), но под общей транзакцией.

### 8.5 Завершение
1. Выставить финальный статус:
   - `COMPLETED` / `COMPLETED_WITH_ERRORS` / `FAILED`
2. Переместить файл в:
   - `PROCESSED` при `COMPLETED*`
   - `FAILED` при `FAILED`

---

## 9. Нефункциональные требования

### 9.1 Параллельность
- Допускается параллельная обработка нескольких файлов.
- Должен быть конфигурируемый лимит параллелизма (`ftp.parallelism`).

### 9.2 Логирование
- На старт и завершение файла логировать:
  - `registry_file.id`, `file_name`, `entity_type`, counters, итоговый статус, длительность
- По ошибочным строкам логировать как минимум:
  - `file_id`, `line_no`, `error_code` (без необходимости выводить весь `raw_line` в лог)
