#### java.lang.String
Текстовые данные в Java помещаются в двойные кавычки.
Конкатенация с любым типом данных через оператор <font color="#00b050">+</font>
- <font color="#31859b">equals</font> - метод для сравнения ссылочных типов
- <font color="#31859b">startsWith</font> - метод проверяет что слово начинается c определенной последовательности
- <font color="#31859b">endsWith</font> - метод проверяет что слово заканчивается на определенную последовательность
- <font color="#31859b">String.concat()</font> - Удобно пользоваться, если у нас уже есть готовые строки, которые мы просто хотим объединить и получит результат. Метод concat() класса String возвращает объект String. Метод concat() можно объединять в цепочку, в результате работы которой будет возвращена новая строка без накладных расходов в виде создания промежуточных строк.
```java
package ru.job4j.concat;

public class ConcatExample {
    public static void main(String[] args) {
        String string = "abc".concat("def").concat("ghi").concat("jkl");
        System.out.println(string);
    }
}
```
- <font color="#31859b">String.join()</font> - когда нам нужно объединить 2 и более строки с каким-либо разделителем, например, пробелом, то можно использовать метод [join()](https://docs.oracle.com/en/java/javase/16/docs/api/java.base/java/lang/String.html#join(java.lang.CharSequence,java.lang.CharSequence...)) класса String.
```java
package ru.job4j.concat;

public class JoinExample {
    public static void main(String[] args) { 
        String string = String.join(" ", "abc", "def", "ghi"); 
        System.out.println(string); 
    }
}
```
