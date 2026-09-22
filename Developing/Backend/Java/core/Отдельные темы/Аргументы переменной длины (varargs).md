---
tags:
  - Java
---
В Java 5 появился удобный механизм **varargs**, позволяющий быстро и просто обрабатывать аргументы произвольной длины. Аргументы переменной длины объявляются в коде в виде троеточия "..." между типом данных и именем.

```java
public static String text(String... strings) {
```

Данная запись подразумевает 0 и более аргументов. Аргумент strings неявно будет преобразован в массив строк. Указать varargs можно _только_ как параметр какого-либо метода.
Пример:
```java
package ru.job4j.varargs;

public class VarArgs {

    public static String text(String... strings) {
        StringBuilder builder = new StringBuilder();
        for (String string : strings) {
            builder.append(string);
        }
        return builder.toString();
    }

    public static void main(String[] args) {
        System.out.println(text());
        System.out.println(text("aaa"));
        System.out.println(text("aaa", "bbb", "ccc"));
        System.out.println(text("aaa", "bbb", "ccc", "ddd", "eee"));
    }

}
```

В методе с аргументами произвольной длины можно задавать и обычные параметры помимо произвольных. Например:
```java
public static String text(int x, int y, String... strings) {
```

Если нужно принимать параметры разных типов, то типом параметра varargs нужно сделать Object:
```java
public static String text(int x, int y, Object... objects) {
```

**Важно!** Параметр varargs всегда должен быть объявлен последним среди всех параметров метода, а также он должен быть единственным. Нельзя объявить 2 параметра varargs в сигнатуре одного и того же метода