---
tags:
  - Java
---
**Исключение** - это ситуация когда в процессе выполнения программы виртуальная машина не смогла выполнить инструкции написанные в программе.

Чтобы прервать выполнение программы, нужно использовать оператор throw с передачей этому оператору объекта типа java.util.Exception.

В Java есть встроенные классы, которые описывают исключительные ситуации. Старайтесь не создавать свои исключения, а использовать встроенные. Это позволит другим программистам быстрее понять причину возникновения проблемы.

В конструкторе исключения нужно обязательно указать причину возникновения исключения. Никогда не оставляйте текст пустым.

```
java.lang.IllegalArgumentException - метод вызывается с некорректными параметрами.

java.lang.IllegalStateException - метод вызывается с объекта в не корректном состоянии.

java.lang.NullPointerException - методы вызывается у переменной, которая инициализирована null ссылкой.
```
Все эти классы наследуются от класса java.lang.RuntimeException. Исключения этого типа имеют особенность. Эти исключения не обрабатывают. В литературе можно встретить термин непроверяемые исключения (unchecked exception) или runtime exception.
Все исключения (проверяемые и непроверяемые) в основе иерархии наследуются от класса java.lang.Exception.

<font color="#ff0000">Все остальные исключения должны быть обработаны!!!</font>

Существует два способа обработки исключений:
1. **Объявления исключений в сигнатуре метода.**
Т.е. проброс исключения дальше в вызывающий код
```java
public static void load(String url) throws UserInputException {
    if (url == null) {
        throw new UserInputException("Url could not be null");
    }
    /*load jdbc*/
}
```

2. **Конструкция try-catch.**
Эта конструкция позволяет выполнить методы, которые могут прерваться с исключением и обработать эти исключения.
```java
public static void main(String[] args) {
    try {
        load("jdbc://localhost:8080");
    } catch (UserInputException e) {
        e.printStackTrace();
    }
}
```
Конструкция try-catch может содержать множественный блок catch. Это позволяет для конкретного прерывания произвести свою обработку.
В случае если в блоке могут упасть разные сообщения, их нужно обработать в блоках catch. Каждый catch ловит свое исключение.
<font color="#ff0000">Если эксепшны находятся в одной ветке иерархии, то первым должен обрабатываться самый нижний класс в иерархии.</font>

---

Все исключения в Java можно представить в виде дерева.
![[imageTaskSource 1.png]]

Throwable - базовый класс для всех нестандартных ситуаций при работе приложения.
Error - ошибки связанные с работой виртуальной машины. Такие как: выход за границы памяти, использование одинаковых библиотек с разными версиями.
Exception - ошибки, которые связаны с работой самой программы. Их можно разделить на два типа: проверяемые (handled) и непроверяемые (unhandled) исключения (RuntimeException). 

**Важно!!!** В чем же состоит разница между проверяемыми и не проверяемыми исключениями?

- проверяемыми являются исключения, которые проверяются во время компиляции программы и если такие исключения не обработать, то наша программа попросту не скомпилируется. Для обработки исключения используется блок try-catch или ключевое слово throws после сигнатуры метода. К таким исключениям относятся: готовые исключения подклассов IOException, SQLException, а также все пользовательские исключения, которые мы наследуем от класса Exception;

- соответственно непроверяемыми исключениями являются исключения, которые проверяются во время запуска программы (Runtime), т.е. если их не обработать с помощью конструкции try-catch, то наш код скомпилируется без всяких проблем. К непроверяемым исключениям относятся все наследники класса RuntimeException.

Почему нежелательно отлавливать RuntimeException? Потому, что эти исключения относятся к логическим ошибкам в коде и их нужно устранить за счет поиска ошибки в коде, а не за счет обработки исключения.

---
### Error
Исключения типа Error связаны с работой виртуальной машины.

Самые популярные Error:
**java.lang.StackOverflowError** - ошибка связана с переполнением области памяти stack. В ней хранятся ссылки на объекты, локальные переменные и вызовы функций. Часто можно увидеть при использовании рекурсии.

Решение проблемы. Поиск логической ошибки в коде.

**java.lang.OutOfMemoryError** - переполнение выделенной памяти для виртуальной машины. Каждая программа использует память компьютера. При старте виртуальной машины ей выделяется ограниченный объем памяти. Ваша программа может израсходовать всю выделенную память.

Решение проблемы. Поиск утечки памяти или увеличение памяти виртуальной машины за счет ключей -Xmx (установка максимального размера heap).

**java.lang.LinkageError** - ошибка связана с загрузкой двух одинаковых библиотек. Имена библиотек отличаются, а классы в пространстве имен остаются одинаковые.

Решение проблемы. Построить иерархию зависимостей библиотек и найти дублирующие классы.

**java.lang.UnsupportedClassVersionError** - ошибка связана с загрузкой скомпилированного кода на виртуальной машине с более старой версией. 

Например, код скомпилировали на версии 13, а запустить пробуют на версии 1.8.

Решение проблемы. Использовать версию одинаковую со скомпилированным кодом или более новую.

Как вы видите все ситуации в этом случае правятся с помощью поиска ошибки в программе или с помощью настройки среды.


<font color="#ff0000">Запомните! </font>Если у Вас стоит вопрос что выбрать - организовать проверку с помощью if() или использовать блок catch с исключением - то выбирать стоит второе. Поскольку такой вариант работает быстрее и нам не требуется писать код для дополнительной проверки.

!!!Паразитная конструкция:
```java
package ru.job4j.ex;

public class Config {
    public static void load(String path) {
        if (path != null) {
            System.out.println("load config by " + path);
        } else {
            throw new IllegalStateException("Path could not be null.");
        }
    }

    public static void main(String[] args) {
        load("jdbc://localhost:8080");
    }
}
```

Лучше писать так:
```java
```java
package ru.job4j.ex;

public class Config {
    public static void load(String path) {
        if (path == null) {
	        throw new IllegalStateException("Path could not be null.");
            
        }
        System.out.println("load config by " + path);
    }

    public static void main(String[] args) {
        load("jdbc://localhost:8080");
    }
}
```

Общая схема:
```java
    if (проверка данные на прерывание) {
        throw new генерация исключения;
    }
    /* полезный код. */
```