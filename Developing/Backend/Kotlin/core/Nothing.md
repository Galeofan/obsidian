`Nothing` в Kotlin означает: “метод **никогда не возвращает значение**” (обычно потому что **всегда кидает исключение**). Тогда Elvis сохранит тип `D`.
```kotlin
private fun callErrorModule(httpStatus: Int, logMsg: String): Nothing {
    errorClient.processError(httpStatus, null, logMsg, null)
    throw GoldenRecordsBadResponseException(logMessage = logMsg)
}
```