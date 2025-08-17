
# 📚 Lombok

## 📝 Описание
Lombok — библиотека для сокращения шаблонного кода в Java.  
Автоматически генерирует геттеры, сеттеры, конструкторы, методы `equals`, `hashCode`, `toString` и многое другое через аннотации.

## 🚀 Основные аннотации
- `@Getter` / `@Setter` — автоматически создают геттеры и сеттеры
- `@Data` — сочетает в себе `@Getter`, `@Setter`, `@RequiredArgsConstructor`, `@ToString`, `@EqualsAndHashCode`
- `@Builder` — паттерн Builder для удобного создания объектов
- `@Slf4j` — автоматически добавляет логгер
- `@NoArgsConstructor`, `@AllArgsConstructor`, `@RequiredArgsConstructor` — генерация конструкторов

## 🔑 Ключевые примеры
```java
import lombok.Data;
import lombok.Builder;

@Data
@Builder
public class User {
    private int id;
    private String name;
}
```

Использование:
```java
User user = User.builder()
        .id(1)
        .name("Andrey")
        .build();

System.out.println(user.getName()); // Andrey
```

## ⚙️ Настройки / расширения
- Подключается через Gradle:
  ```gradle
  dependencies {
      compileOnly 'org.projectlombok:lombok:1.18.30'
      annotationProcessor 'org.projectlombok:lombok:1.18.30'
  }
  ```
- Для IDE нужно включить поддержку аннотаций (в IntelliJ IDEA — включить Lombok plugin).

## ❗ Подводные камни / особенности
- Код генерируется на этапе компиляции → в исходниках не видно методов
- Иногда ломается автогенерация при обновлении IDE/Gradle
- Не рекомендуется злоупотреблять `@Data` для сложных сущностей (лучше явно определять нужные методы)
- Может мешать отладке, если IDE не корректно отображает сгенерированные методы

## 🔗 Ссылки
- [Официальный сайт](https://projectlombok.org/)
- [Аннотации Lombok](https://projectlombok.org/features/all)
