# 📚 Lombok

## 📝 Описание
Lombok — библиотека для сокращения шаблонного кода в Java.  
Автоматически генерирует геттеры, сеттеры, конструкторы, методы `equals`, `hashCode`, `toString` и многое другое через аннотации.

## 🚀 Основные аннотации
- `@Getter` / `@Setter` — автоматически создают геттеры и сеттеры
- `@Data` — сочетает в себе `@Getter`, `@Setter`, `@RequiredArgsConstructor`, `@ToString`, `@EqualsAndHashCode`
- `@Builder` — паттерн Builder для удобного создания объектов
- `@Builder (toBuilder = true)` - параметр для удобного создания копии иммутабельного объекта с некоторыми изменениями. Вызывается на объекте 
- `@Slf4j` — автоматически добавляет логгер
- 
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
- Также обязательно скачать плагин

## ❗ Подводные камни / особенности
- Код генерируется на этапе компиляции → в исходниках не видно методов
- Иногда ломается автогенерация при обновлении IDE/Gradle
- Не рекомендуется злоупотреблять `@Data` для сложных сущностей (лучше явно определять нужные методы)
- Может мешать отладке, если IDE не корректно отображает сгенерированные методы

## 🔗 Ссылки
- [Официальный сайт](https://projectlombok.org/)
- [Аннотации Lombok](https://projectlombok.org/features/all)

<font color="#ffff00">@NonNull</font> - используется для автоматической проверки, что переданный параметр не является `null`.  
Если в метод передаётся `null`, будет выброшено `NullPointerException`.
✅ <font color="#00b050">Лучшее использование:</font>
- В конструкторах с `@RequiredArgsConstructor`
- В параметрах методов

⚠ <font color="#ff0000">НЕ ставьте `@NonNull` на поля, если нет конструктора – проверка не сработает!</font>

---

## Аннотации подробно
<font color="#ffff00">@Getter</font> и <font color="#ffff00">@Setter</font> - Автоматическое создание геттеров и сеттеров.
📌 **Дополнительно:**
- Можно аннотировать только поле, если не нужны геттеры/сеттеры для всех полей.
- Можно задать `@Getter(AccessLevel.NONE)` или `@Setter(AccessLevel.PRIVATE)`, чтобы скрыть методы.

---

<font color="#ffff00">@ToString</font> — Автоматический `toString()`, включает все поля
📌 **Можно исключить поля:**
```java
@ToString(exclude = "password")
```

---

<font color="#ffff00">@EqualsAndHashCode</font> — `equals()` и `hashCode()`, основываясь на всех полях.
📌 **Можно исключить поля:**
```java
@EqualsAndHashCode(exclude = "id")
```

---

<font color="#ffff00">@NoArgsConstructor</font>, <font color="#ffff00">@AllArgsConstructor</font>, <font color="#ffff00">@RequiredArgsConstructor</font> — Конструкторы

| Аннотация                  | Что делает?                                              |
| -------------------------- | -------------------------------------------------------- |
| `@NoArgsConstructor`       | Создаёт пустой конструктор                               |
| `@AllArgsConstructor`      | Создаёт конструктор со всеми аргументами                 |
| `@RequiredArgsConstructor` | Создаёт конструктор только с `final` и `@NonNull` полями |

---

<font color="#ffff00">@Data</font> — Всё сразу!
📌 Эквивалентно: `@Getter + @Setter + @ToString + @EqualsAndHashCode + @RequiredArgsConstructor`

---

<font color="#ffff00">@Value</font> — Immutable класс (аналог `@Data`, но с `final`)
📌 Все поля `private final`, без сеттеров.
✔ Генерирует только геттеры, `toString()`, `equals()`, `hashCode()`, и конструктор.  
❌ Нельзя изменить поля после создания объекта.

---

<font color="#ffff00">@Builder</font> — Шаблон Builder (удобное создание объектов)
📌 Позволяет гибко создавать объекты, особенно если много параметров.
```java
User user = User.builder()
        .name("Alice")
        .age(25)
        .build();
```

---

<font color="#ffff00">@Slf4j</font> - это интерфейс (абстракция) для работы с логированием в Java. Он позволяет использовать разные системы логирования (например, Logback, Log4j, java.util.logging) без привязки к конкретной библиотеке.
📌 Добавляет `private static final Logger log = LoggerFactory.getLogger(Класс.class);`
Lombok упрощает работу с логированием, автоматически создавая `Logger` через аннотацию <font color="#ffff00">@Slf4j</font>. Это избавляет от необходимости вручную объявлять логгер.
Lombok автоматически создаёт статический `Logger`, доступный через `log`.
```java
import lombok.extern.slf4j.Slf4j;

@Slf4j
public class LombokSlf4jExample {
    public static void main(String[] args) {
        log.info("Приложение запущено!");
        log.warn("Предупреждение!");
        log.error("Ошибка произошла!");

        String user = "Alex";
        log.debug("Пользователь {} вошёл в систему", user);
    }
}
```

---

<font color="#ffff00">@SneakyThrows</font> — Упрощает обработку исключений
📌 Позволяет не писать `try-catch` или `throws` в методе.
```java
import lombok.SneakyThrows;

public class Example {
    @SneakyThrows
    public void riskyMethod() {
        throw new Exception("Ошибка!");
    }
}
```
✔ Spring сам обернёт в `try-catch` и пробросит исключение.

---
