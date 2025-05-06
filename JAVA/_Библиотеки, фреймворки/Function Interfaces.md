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
