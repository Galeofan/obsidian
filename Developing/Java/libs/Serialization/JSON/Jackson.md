# 📚 Jackson

## 📝 Описание
Библиотека для сериализации и десериализации JSON в Java.  
Основной класс — `ObjectMapper`.

## 🚀 Основные классы / интерфейсы
- `ObjectMapper` — основной API для чтения/записи JSON
- `JsonNode` — дерево JSON-узлов
- `Module` — механизм расширения (например, для работы с датами)

## 🔑 Ключевые методы
- `readValue(String, Class<T>)` — JSON → объект
- `writeValueAsString(Object)` — объект → JSON
- `registerModule(Module)` — добавить расширение (например, `JavaTimeModule`)

## 💡 Примеры использования
```java
ObjectMapper mapper = new ObjectMapper();

// JSON → объект
String json = "{\"id\":1,\"name\":\"Andrey\"}";
User user = mapper.readValue(json, User.class);

// объект → JSON
String jsonOut = mapper.writeValueAsString(user);
System.out.println(jsonOut);

// работа с деревом
JsonNode node = mapper.readTree(json);
System.out.println(node.get("name").asText());
```

## ⚙️ Настройки / расширения
- `mapper.enable(SerializationFeature.INDENT_OUTPUT)` — pretty-print
- `mapper.disable(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES)` — игнор неизвестных полей при десериализации
- `mapper.setSerializationInclusion(JsonInclude.Include.NON_NULL)` — обязательно сериализовать только nonnull поля, остальные опционально 
- `mapper.registerModule(new JavaTimeModule())` — поддержка `LocalDateTime`

## ❗ Подводные камни / особенности
- По умолчанию упадёт на неизвестных полях → нужно отключать FAIL_ON_UNKNOWN_PROPERTIES
- Для `LocalDate/LocalDateTime` обязателен `JavaTimeModule`

## 🔗 Ссылки
- [Документация](https://github.com/FasterXML/jackson)
- [Wiki: Jackson Annotations](https://github.com/FasterXML/jackson-annotations)
