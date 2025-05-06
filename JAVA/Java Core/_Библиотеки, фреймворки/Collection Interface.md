---
tags:
  - collections
---
**1. Методы для добавления элементов:**
 (Вместо заглавной E может быть использован любой ссылочный тип данных)
 
- <font color="#31859b">boolean add(E e)</font> – метод добавляет элемент в коллекцию и возвращает true если добавление выполнено успешно.
- <font color="#31859b">boolean addAll</font>(< Collection? extends E> c) – метод добавляет все элементы переданной в метод коллекции в коллекцию, у которой был вызван этот метод. Метод возвращает true если коллекция, у которой был вызван метод, изменилась.
Пример добавления элементов в ArrayList:
```java
public class CollectionUsage {
    public static void main(String[] args) {
        Collection<String> collection = new ArrayList<>();
        collection.add("one");
        collection.add("two");
        collection.add("three");
        for (String string : collection) {
            System.out.println(string);
        }
    }
}
```

Пример добавления в коллекцию другой коллекции:
```java
Collection<String> collection = new ArrayList<>();
collection.add("one");
collection.add("two"); 
collection.add("three");
Collection<String> list = new ArrayList<>();
list.addAll(collection);
for (String string : list) {
    System.out.println(string);
}
```
---
**2. Чтение элементов из коллекции.**
Единственный способ, который определен на уровне Collection для чтения элементов из коллекции – это использовать итератор, на основе которого работает улучшенный цикл for или цикл for-each.
- <font color="#31859b">Iterator</font>< E> iterator()– метод возвращает объект Iterator, который позволяет проходить по элементам нашей коллекции.
---
**3. Удаление элементов.**
<font color="#31859b">boolean remove</font>(Object o) – метод удаляет объект, который мы передали в метод, и возвращает true, если удаление успешно, т.е. коллекция изменилась.
---
```java
Collection<String> collection = new ArrayList<>();
collection.add("one");
collection.add("two");
collection.add("three");
for (String string : collection) {
    System.out.println(string);
}
collection.remove("two");
System.out.println("Вывод содержимого коллекции после удаления");
for (String string : collection) {
    System.out.println(string);
}

//В последнем выводе получится только two
```

<font color="#31859b">boolean removeAll</font>(Collection< ?> c) – метод удаляет все элементы из коллекции, у которой был вызван этот метод, и которые также содержатся в переданной в метод коллекции. Таким образом после вызова этого метода в коллекции не будет тех элементов, которые содержатся в переданной в метод коллекции.

<font color="#31859b">boolean retainAll</font>(Collection< ?> c) – метод, который позволяет сохранить в коллекции только те элементы, которые содержатся в коллекции, которая была передана в метод. Таким образом в результате в коллекции останутся только те элементы, которые представлены в обоих коллекциях.

Пример вызова retainAll:
```java
Collection<String> collection = new ArrayList<>();
collection.add("one");
collection.add("two");
collection.add("three");
Collection<String> list = new ArrayList<>();
list.add("two");
for (String string : collection) {
    System.out.println(string);
}
collection.retainAll(list);
System.out.println("Вывод содержимого коллекции после вызова retainAll");
for (String string : collection) {
    System.out.println(string);
}
```

**4. Вспомогательные методы.**
- <font color="#31859b">int size() </font>– метод возвращает размер коллекции. Т.е. фактическое количество добавленных в коллекцию элементов.
- <font color="#31859b">boolean isEmpty()</font> – метод который вернет true, если коллекция пока еще не содержит элементов.
- <font color="#31859b">boolean contains(Object o)</font> – метод возвращает true, если переданный в метод объект содержится в коллекции.
- <font color="#31859b">void clear()</font> – удаляет все элементы из коллекции. После применения этого метода коллекция будет пустой.
- <font color="#31859b">Object[] toArray()</font> – метод возвращает массив, который содержит все элементы, содержащиеся в коллекции у которой был вызван этот метод.
