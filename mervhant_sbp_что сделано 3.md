Сделал ImportPersistenceService и всё, что нужно вокруг него для батч-вставок.
Что добавлено:
•
Сервис вставки:
◦
src/main/java/ru/mtsbank/ph/merchantsbp/service/importer/persistence/ImportPersistenceService.java
•
Результат вставки (для счётчиков):
◦
src/main/java/ru/mtsbank/ph/merchantsbp/service/importer/persistence/ImportInsertResult.java
•
Модели строк для персистентности:
◦
src/main/java/ru/mtsbank/ph/merchantsbp/service/importer/persistence/LegalEntityPersistenceRow.java
◦
src/main/java/ru/mtsbank/ph/merchantsbp/service/importer/persistence/MerchantPersistenceRow.java
•
Unit-тесты:
◦
src/test/java/ru/mtsbank/ph/merchantsbp/service/importer/persistence/ImportPersistenceServiceTest.java
Что делает сервис:
•
insertLegalEntities(List<LegalEntityPersistenceRow>)
◦
INSERT INTO merchant_sbp.legal_entity ... ON CONFLICT DO NOTHING
◦
считает attempted/inserted/skipped через batchUpdate result.
•
insertMerchants(List<MerchantPersistenceRow>)
◦
INSERT INTO merchant_sbp.merchant ... ON CONFLICT DO NOTHING
◦
аналогично считает attempted/inserted/skipped.
•
Дефолты в вставке:
◦
LegalStatus.REGISTERED
◦
MerchantStatus.CREATED
◦
retry_count = 0
◦
sales_mode = FACE_TO_FACE, если в строке null
◦
created_by/updated_by = {"id":"PHUB"}
Проверка:
•
Запуск mvn -q -Dtest=ImportPersistenceServiceTest test не прошёл из-за инфраструктуры Maven/Nexus (ошибка сертификата nexus.services.mts.ru, SAN mismatch), а не из-за кода.
Если хотите, следующим шагом сразу подключу этот сервис в ImportPipelineService и заведу пересчёт lines_valid/lines_skipped через ImportInsertResult.
