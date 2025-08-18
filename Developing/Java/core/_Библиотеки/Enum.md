#### Enum
- name() - возвращает имя константы (значения) в том же виде, в каком оно объявлено.
- ordinal() - возвращает порядковый номер константы (значения), по которому её экземпляр находится в перечислении (нумерация с нуля).
- values() - возвращает массив всех элементов перечисления.

```java
Status[] statuses = Status.values();
        for (Status s : statuses) {
            System.out.println("Название статуса: " + s.name() + ", Порядковый номер статуса: " + s.ordinal());
        }
```

- valueOf() - получает объект типа класса перечисления по его строковому представлению.
```java
String accepted = "ACCEPTED";
Status status = Status.valueOf(accepted);
System.out.println(status);
```
В данном коде мы получаем объект типа Status из одноимённой строки.