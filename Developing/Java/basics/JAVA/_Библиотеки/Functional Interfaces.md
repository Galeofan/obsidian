##### 1. Supplier
Supplier (поставщик) используется для создания какого-либо объекта и при этом ему не требуется входной параметр. В стандартной библиотеке Java интерфейс представлен в следующем виде:
```java
@FunctionalInterface
public interface Supplier<T> {
    T get();
}
```
Основное применение этого интерфейса – создание новых объектов. Давайте попробуем создать HashSet строк из списка с помощью этого интерфейса:
```java
List<String> list = List.of("one", "two", "three", "one", "two", "three");
Supplier<Set<String>> supplier = () -> new HashSet<>(list);
Set<String> strings = supplier.get();
for (String string : strings) {
    System.out.println(string);
}
```
##### 2. Consumer и BiConsumer
Consumer (потребитель) используется в том случае, если нам нужно применить какое-то действие или операцию к параметру (для BiConsumer два параметра) и при этом у метода нет возвращаемого значения. Объявляются эти интерфейсы следующим образом:
```java
@FunctionalInterface
public interface Consumer<T> {
    void accept(T t);
}
@FunctionalInterface
public interface BiConsumer<T, U> {
    void accept(T t, U u);
}
```
Пример:
```java
List<String> list = List.of("one", "two", "three", "one", "two", "three");
Supplier<Set<String>> supplier = () -> new HashSet<>(list);
BiConsumer<Integer, String> consumer = (first, second) -> System.out.println(first + second);
Set<String> strings = supplier.get();
int i = 1;
for (String string : strings) {
    consumer.accept(i++, " is " + string);
}
Вывод: 
1 is one
2 is two
3 is three
```

##### 3. Predicate (BiPredicate).
Predicate (утверждение) наиболее часто применяется в фильтрах и сравнении и объявляются они следующим образом:
```java
@FunctionalInterface
public interface Predicate<T> {

    boolean test(T t);
}
@FunctionalInterface
public interface BiPredicate<T, U> {

    boolean test(T t, U u);
}
```
Т.е. в метод test() передается один или два параметра, в зависимости от функционального интерфейса и возвращает логическое значение true или false.
Пример:
```java
Predicate<String> predicate = string -> string.isEmpty();
System.out.println("Строка пустая: " + predicate.test(""));
System.out.println("Строка пустая: " + predicate.test("test"));

Вывод:
Строка пустая: true
Строка пустая: false
```

##### 4. Function (BiFunction)
Function используется для преобразования входного параметра или двух параметров (для Bi-формы этого функционального интерфейса) в какое-либо значение, тип возвращаемого значения может не совпадать с типом входных параметров. Объявляются интерфейсы следующим образом:
```java
@FunctionalInterface
public interface Function<T, R> {

    R apply(T t);
}
@FunctionalInterface
public interface BiFunction<T, U, R> {

    R apply(T t, U u);
}
```
Попробуем прочитать символ с определенным индексом в строке с помощью функционального интерфейса:
```java
Function<String, Character> function = string -> string.charAt(2);
System.out.println("Третий символ в строке: " + function.apply("first"));
System.out.println("Третий символ в строке: " + function.apply("second"));
```
##### 5. UnaryOperator и BinaryOperator.
UnaryOperator и BinaryOperator – это разновидность Function, в которых входные и выходные обобщенные параметры должны совпадать:
```java
@FunctionalInterface
public interface UnaryOperator<T> extends Function<T, T> {}
@FunctionalInterface
public interface BinaryOperator<T> extends BiFunction<T,T,T> {}
```
Используем функциональный интерфейс UnaryOperator для того, чтобы перевернуть строку:
```java
UnaryOperator<StringBuilder> builder = b -> b.reverse();
System.out.println("Строка после реверса: " + builder.apply(new StringBuilder("String for test")));
System.out.println("Строка после реверса: " + builder.apply(new StringBuilder("tset rof gnirtS")));
```
Применение BinaryOperator рассмотрим на примере объединения двух строк:
```java
BinaryOperator<StringBuilder> builder = (first, second) -> first.append(" ").append(second);
System.out.println(
        "Строка после объединения: " + builder.apply(
                new StringBuilder("First string"),
                new StringBuilder("Second string")
        )
);
```
Обратите внимание, что, несмотря на три параметра (два входных и один для возвращаемого значения) в обобщении мы указываем только один тип – StringBuilder. Все потому, что, как говорилось ранее, для функциональных интерфейсов UnaryOperator и BinaryOperator обобщенные типы должны совпадать и указывать три одинаковых типа обобщения не имеет смысла.