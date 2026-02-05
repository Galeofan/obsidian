# Dummy API

**Dummy API** — это Spring Boot (Java 11) сервис-заглушка, предназначенный для подмены внешних SOAP/REST-интеграций.  
Сервис принимает **любые входящие HTTP(S)-запросы** и возвращает **заранее подготовленные ответы из файлов** на основе правил, описанных в JSON-конфигах.

---

## Общая схема работы

1. **Перехват запроса**
   - `DummyController` перехватывает все пути `/**` и почти все HTTP-методы.
   - Логирует:
     - URL
     - query-параметры
     - headers
     - body
   - Передаёт управление в `DummyService`.

   **Код:**  
   `src/main/java/ru/mtsbank/soapdummy/mock/DummyController.java`

2. **Загрузка правил моков**
   - `DummyCacheStorage`:
     - читает все `*.json` из директорий, указанных в `setting.mock.file.dirs.path`
     - по умолчанию: `./config/mocks_conf/`
     - кладёт правила в Guava-кэш
     - периодически перечитывает конфиги по cron-выражению `dummy.load.cache.cron`

   **Код и настройки:**  
   - `src/main/java/ru/mtsbank/soapdummy/mock/setting/DummyCacheStorage.java`  
   - `config/application.properties`

3. **Выбор ответа**
   - Сначала выполняется **строгий матч по `requestURI`**  
     (`requestURI` ⇔ `endpointUrl`)
   - Далее перебираются `specificMockSettings`:
     - **без условий** — используется как *общая* настройка для эндпоинта
     - **с условиями** (`requestMockConditions`):
       - точное совпадение тела запроса с файлом (`requestBodyPath`)
       - `containsValue` — поиск подстроки или regex в:
         - query
         - body

   **Код:**  
   - `src/main/java/ru/mtsbank/soapdummy/mock/DummyService.java`  
   - `src/main/java/ru/mtsbank/soapdummy/mock/RequestMockConditions.java`

---

## Возможности ответа

Dummy API поддерживает следующие сценарии:

### 1. Статический ответ
- HTTP-статус
- Тело ответа из файла (`responseBodyPath`)

**Конфигурация:** `ResponseSetting`

---

### 2. Искусственная задержка
- Задержка ответа по правилу `sleepRule`
- Используется для эмуляции медленных внешних сервисов

---

### 3. Динамические плейсхолдеры
- Включается через `enableDynamicResponse`
- Поддерживаются:
  - генерация UUID
  - генерация строк
  - генерация чисел
- Значения подставляются в тело ответа

**Настройка:** `dynamicResponses`

---

### 4. Доменные вычисления TIV
- Включается флагом `isTiv`
- Выполняются вычисления и подстановки полей:
  - на основе входящего запроса
  - на основе курса из ответа
- Используется для специфичной бизнес-логики

**Код:**  
`src/main/java/ru/mtsbank/soapdummy/utils/ReplaceUtils.java`

---

## Сетап сервера

- Основной HTTPS-сервер:
  - порт: `server.port`
- Дополнительный HTTP-коннектор:
  - порт: `server.http.port`
  - реализован через `Tomcat additional connector`
- Фильтр запросов:
  - добавляет `sessionId` в MDC
  - используется для корреляции логов

**Код и конфигурация:**  
- `src/main/java/ru/mtsbank/soapdummy/config/HttpConnectorConfig.java`  
- `src/main/java/ru/mtsbank/soapdummy/config/HttpServletFilter.java`  
- `config/log4j.properties`

---

## Структура конфигов и примеров

- Примеры конфигураций:
  - `src/main/resources/config/*.json`
- Примеры тел ответов:
  - `src/main/resources/response/*`
- Рабочие моки (по умолчанию):
  - `config/mocks_conf/*.json`

В репозитории присутствует пример: 
`config/mocks_conf/universalbank-sbp.json`