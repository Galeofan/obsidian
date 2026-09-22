runBlocking позволяет запускать асинхронный код в одном потоке, например для кафки т.к. кафка всегда синхронно ждёт ответа. Столкнулся при интеграции с graphql т.к. там функция вызова запроса обязательно должна быть suspend

```
package ru.mts.multitransfer.gr.adapter.controller.kafka

import kotlinx.coroutines.runBlocking
import org.springframework.boot.autoconfigure.condition.ConditionalOnBean
import org.springframework.stereotype.Component
import ru.mts.multitransfer.api.configuration.kafka.auto.AutoConfigurableKafkaListener
import ru.mts.multitransfer.api.converter.toProto
import ru.mts.multitransfer.api.kafka.KafkaConfig
import ru.mts.multitransfer.api.protobuf.gradapter.get_golden_record_client_by_document_id.GetGoldenRecordClientByDocumentId
import ru.mts.multitransfer.gr.adapter.service.ConnectorFactory


@Component
@ConditionalOnBean(KafkaConfig::class)
class GetGoldenRecordClientByDocumentIdController(
    connectorFactory: ConnectorFactory
) {

    companion object {
        const val METHOD_NAME = "get-golden-record-client-by-document-id"
    }

    private var goldenRecordsSearchConnector = connectorFactory.getSearchConnector()

    @AutoConfigurableKafkaListener(methodName = METHOD_NAME)
    fun listen(request: GetGoldenRecordClientByDocumentId.Request): GetGoldenRecordClientByDocumentId.Response {
        return runBlocking {
            val person = goldenRecordsSearchConnector.search(
                request.documentId,
                request.documentSeries,
                request.documentNumber
            )

            GetGoldenRecordClientByDocumentId.Response.newBuilder()
                .setClient(person.toProto())
                .build()
        }
    }
}
```