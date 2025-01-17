---
tags:
  - ООП
---
### ENUM
**Enum** - это класс c ограниченным набором объектов. Также можно встретить термин "класс-перечислений".

Перечисления используется для объявления набора фиксированных объектов. Например, дни недели, времена года, карточные масти или даже записи полей шахматной доски.

Элемент перечисления имеет аналогичную структуру класса, только вместо class используется ключевое слово enum.

```java
package ru.job4j.enumeration;

public enum Status {
    ACCEPTED,
    IN_WORK,
    WAITING,
    FINISHED
}
```

В теле класса мы объявили имена **<font color="#ff0000">ОБЪЕКТОВ</font>** этого перечисления: ACCEPTED, IN_WORD, WAITING, FINISHED.
```java
package ru.job4j.enumeration;

public class CarService {

    public static void main(String[] args) {
        Status toyota = Status.FINISHED;
        Status volvo = Status.WAITING;
        System.out.println("Статус Toyota: " + toyota);
        System.out.println("Статус Volvo: " + volvo);
    }

}
```

**Конструкторы, поля и методы**
Так как enum - это обычный Java класс, то в нем можно объявить: конструкторы, поля и методы.
```java
public enum G {  
    ACCEPTED("Принят"),  
    IN_WORK("В работе"),  
    WAITING("Ожидает клиента"),  
    FINISHED("Завершен");  
  
    private final String text;  
  
    G(String text) {  
        this.text = text;  
    }  
  
    public String info() {  
        return text;  
    }  
  
    public static void main(String[] args) {  
        G toyota = G.FINISHED;  
        G volvo = G.WAITING;  
        System.out.println("Статус Toyota: " + toyota.info());  
        System.out.println("Статус Volvo: " + volvo.info());  
    }  
}
```
Вывод:
Статус Toyota: Завершен
Статус Volvo: Ожидает клиента

Перечисление может реализовывать интерфейсы

**Абстрактные классы и методы**
Enum нельзя объявить абстрактным, потому что у нас нет возможности его наследовать.
Однако, мы можем объявить абстрактные методы.

В нашем коде конструктор в перечислении неявно имеет модификатор private. Других модификаторов конструктор перечисления иметь не может, соответственно, создать объект перечисления мы можем только внутри него самого. Каждый из экземпляров перечисления создаётся только при первом обращении к нему.

Каждое значение (константа) нашего перечисления - это объект анонимного класса. Создав конструктор с полем info, мы сразу должны передать этот параметр в каждое значение нашего перечисления, то есть ранее у нас был просто ACCEPTED, а стал ACCEPTED("Принят")

Данная строка под капотом выглядит так:
```java
public static final Status ACCEPTED = new Status("Принят") { };
```
Отсюда следует, что перечисление - это список неизменяемых готовых объектов.

Так как каждое значение перечисления - это объект анонимного класса, то в нём можно объявлять свои методы:
```java
package ru.job4j.enumeration;

public enum Status {
    ACCEPTED("Принят") {
        private String message = "Автомобиль принят на СТО";

        @Override
        public String getMessage() {
            return message;
        }

    },
    IN_WORK("В работе") {
        private String message = "Автомобиль в работе";

        @Override
        public String getMessage() {
            return message;
        }
    },
    WAITING("Ожидание") {
        private String message = "Автомобиль ожидает запчасти";

        @Override
        public String getMessage() {
            return message;
        }
    },
    FINISHED("Работы завершены") {
        private String message = "Все работы завершены";

        @Override
        public String getMessage() {
            return message;
        }
    };

    private String info;

    Status(String info) {
        this.info = info;
    }

    public String getInfo() {
        return info;
    }

    public abstract String getMessage();

}
```
Сначала в перечисление Status мы добавили абстрактный метод getMessage(), который обязывает каждое значение перечисления переопределить этот метод:

Далее в тело каждого из значений мы добавили по переменной message и по методу getMessage(), который переопределяет абстрактный метод из Status, возвращая переменную message. Таким образом, каждое значение - это отдельный объект со своим поведением.

**Switch**
Также, так как значения enum не меняются, то при работе с ними можно использовать оператор switch. Добавим в метод main():
```java
        switch (status) {
            case ACCEPTED:
                System.out.println("Статус: Автомобиль принят на СТО");
                break;
            case IN_WORK:
                System.out.println("Статус: Автомобиль в работе");
                break;
            case WAITING:
                System.out.println("Статус: Автомобиль ожидает запчасти");
                break;
            case FINISHED:
                System.out.println("Статус: Все работы завершены");
                break;
            default:
```