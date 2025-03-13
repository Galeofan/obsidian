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