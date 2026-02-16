Что сделано
•
Добавил сервис реестра: src/main/java/ru/mtsbank/ph/merchantsbp/service/importer/RegistryFileService.java
•
Расширил DAO чтением по id: src/main/java/ru/mtsbank/ph/merchantsbp/dao/RegistryFileDao.java
•
Добавил CSV-парсер со strict header и UTF-8 BOM:
◦
src/main/java/ru/mtsbank/ph/merchantsbp/service/importer/csv/CsvParser.java
◦
src/main/java/ru/mtsbank/ph/merchantsbp/service/importer/csv/ParsedCsv.java
•
Добавил модели строк импорта:
◦
src/main/java/ru/mtsbank/ph/merchantsbp/service/importer/model/LegalEntityImportRow.java
◦
src/main/java/ru/mtsbank/ph/merchantsbp/service/importer/model/MerchantImportRow.java
•
Добавил слой валидации:
◦
src/main/java/ru/mtsbank/ph/merchantsbp/service/importer/validation/ValidationResult.java
◦
src/main/java/ru/mtsbank/ph/merchantsbp/service/importer/validation/ValidationError.java
◦
src/main/java/ru/mtsbank/ph/merchantsbp/service/importer/validation/LegalEntityRowValidator.java
◦
src/main/java/ru/mtsbank/ph/merchantsbp/service/importer/validation/MerchantRowValidator.java
По правилам ошибок
•
HEADER_MISMATCH теперь возвращается как код ошибки через ValidationResult в CsvParser.validateHeader(...).
•
EMPTY_LINE возвращается валидаторами для полностью пустой строки.
•
UUID_PARSE_ERROR, EMPTY_FIELD, LEGAL_ENTITY_NOT_FOUND покрыты в валидаторах.
Тесты добавлены
•
src/test/java/ru/mtsbank/ph/merchantsbp/service/importer/RegistryFileServiceTest.java
•
src/test/java/ru/mtsbank/ph/merchantsbp/service/importer/csv/CsvParserTest.java
•
src/test/java/ru/mtsbank/ph/merchantsbp/service/importer/validation/LegalEntityRowValidatorTest.java
•
src/test/java/ru/mtsbank/ph/merchantsbp/service/importer/validation/MerchantRowValidatorTest.java
Проверку mvn test запустить не смог: выполнение команды в текущей среде было отклонено политикой прав.
1.
Если хотите, следующим шагом сделаю п.2 (интерфейс FtpFileClient + изоляция FTP-реализации).
2.
Или сразу п.6 (транзакционные вставки ON CONFLICT DO NOTHING и корректные lines_valid/lines_skipped).