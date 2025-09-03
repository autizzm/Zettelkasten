
Theory for the cards:  [[List Range-View Operations]]

FILE TAGS: java_core

Q: Как проще всего скопировать часть List?
A: С помощью range-view метода `sublist(int fromIndex, int toIndex)`:
```java
List<E> handView = deck.sublist( ..., ...); //handView - range-View list
List<E> hand = new ArrayList(handView); // сделали копию Range-View списка
```
<!--ID: 1756813264808-->


Q: Корректно ли выполнятся здесь все операции?
```java
List<String> list = new ArrayList<>(List.of("a", "b", "c", "d"));
List<String> sub = list.subList(1, 3); // [b, c]

list.add("z");
sub.get(0);  
```
A: Нет, мы получим ConcurrentModificationException при попытке взаимодействия с range-view, источник которого мы изменили

