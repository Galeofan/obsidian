Сделал следующий шаг: собрал рабочий ImportPipelineService и синхронизировал модель/валидацию под реальную вставку в БД.
Что добавлено и изменено:
•
Оркестратор импорта:
src/main/java/ru/mtsbank/ph/merchantsbp/service/importer/ImportPipelineService.java
◦
статусы: UPLOADED -> PARSING -> PARSED -> PROCESSING -> COMPLETED*
◦
при ошибке: FAILED
◦
strict header для LEGAL и MERCHANT
◦
построчная валидация + addError(...)
◦
счётчики:
▪
lines_count = все data-строки
▪
lines_invalid = невалидные/ошибки парсинга
▪
lines_valid = ImportInsertResult.inserted
▪
lines_skipped = ImportInsertResult.skipped
•
Persistence слой (из прошлого шага, теперь используется pipeline):
src/main/java/ru/mtsbank/ph/merchantsbp/service/importer/persistence/ImportPersistenceService.java
src/main/java/ru/mtsbank/ph/merchantsbp/service/importer/persistence/ImportInsertResult.java
src/main/java/ru/mtsbank/ph/merchantsbp/service/importer/persistence/LegalEntityPersistenceRow.java
src/main/java/ru/mtsbank/ph/merchantsbp/service/importer/persistence/MerchantPersistenceRow.java
•
Расширены import row модели под обязательные поля таблиц:
src/main/java/ru/mtsbank/ph/merchantsbp/service/importer/model/LegalEntityImportRow.java
src/main/java/ru/mtsbank/ph/merchantsbp/service/importer/model/MerchantImportRow.java
•
Обновлены валидаторы под новые поля:
src/main/java/ru/mtsbank/ph/merchantsbp/service/importer/validation/LegalEntityRowValidator.java
src/main/java/ru/mtsbank/ph/merchantsbp/service/importer/validation/MerchantRowValidator.java
◦
EMPTY_LINE, EMPTY_FIELD, UUID_PARSE_ERROR, LEGAL_ENTITY_NOT_FOUND
◦
для некорректного sales_mode в pipeline теперь пишется CSV_PARSE_ERROR на строку, а не падает весь файл
Тесты:
•
Добавил тесты pipeline:
src/test/java/ru/mtsbank/ph/merchantsbp/service/importer/ImportPipelineServiceTest.java
•
Добавил тесты persistence:
src/test/java/ru/mtsbank/ph/merchantsbp/service/importer/persistence/ImportPersistenceServiceTest.java
•
Актуализировал тесты валидаторов под новые модели:
src/test/java/ru/mtsbank/ph/merchantsbp/service/importer/validation/LegalEntityRowValidatorTest.java
src/test/java/ru/mtsbank/ph/merchantsbp/service/importer/validation/MerchantRowValidatorTest.java
Проверка mvn test по-прежнему блокируется инфраструктурой (nexus.services.mts.ru certificate SAN mismatch), а не кодом.
1.
Дальше логично сделать адаптер уровня “file handler” (который вызовет processFile(...) для конкретного FTP/локального файла).
2.
Затем подключить FTP-слой (FtpFileClient) и сканер/параллелизм.