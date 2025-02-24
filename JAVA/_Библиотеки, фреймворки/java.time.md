#### java.time
<font color="#ff0000">Классы</font>
1. <font color="#e36c09">LocalDate</font> - предназначен для отображения даты (год, месяц, день (yyyy-MM-dd))
2. <font color="#e36c09">LocalTime</font> - предназначен для отображения времени (час, минуты, секунды и наносекунды (HH-mm-ss-ns))
3. <font color="#e36c09">LocalDateTime</font> - предназначен для отображения даты и времени (yyyy-MM-dd-HH-mm-ss-ns)
4. <font color="#e36c09">DateTimeFormatter</font> - предназначен для форматирования даты согласно образцу

<font color="#ff0000">Методы</font>
<font color="#31859b">now() </font>-  статический метод, позволяющий получить текущие дату, время или и дату и время, в первых трех классах выше
Пример:
```java
package ru.job4j.tracker;

import java.time.LocalDateTime;

public class UsagePackageDate {
    public static void main(String[] args) {
        LocalDateTime currentDateTime = LocalDateTime.now();
        System.out.println("Текущие дата и время: " + currentDateTime);
    }
}
```
<font color="#31859b">DateTimeFormatter.ofPattern()</font> - метод форматирования даты
Пример:
```java
package ru.job4j.tracker;

import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;

public class UsagePackageDate {
    public static void main(String[] args) {
        LocalDateTime currentDateTime = LocalDateTime.now();
        System.out.println("Текущие дата и время до форматирования: " + currentDateTime);

        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd-MM-yyyy HH:mm:ss");
        String currentDateTimeFormat = currentDateTime.format(formatter);
        System.out.println("Текущие дата и время после форматирования: " + currentDateTimeFormat);
    }
}
```