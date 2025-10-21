
Theory for the cards: [[Comparable Interface - Java]]

FILE TAGS: java_core

Q: Для чего нужно реализовывать интерфейс Comparable.
A: Чтобы сортировать объекты by ***natural ordering*** . В том числе необходим и для сортировки с помощью `Collections.sort(List<T> list)` и `Arrays.sort(Object[] array)`.
<!--ID: 1756891680591-->


Q: Какой метод содержит интерфейс Comparable?
A: `compareTo(Object obj)`
<!--ID: 1756891680602-->


Q: Что возвращает метод `compareTo(Object obj)`?
A: Отрицательный результат, ноль, положительный результат означают соответственно, что ***this*** меньше, равен, больше чем `obj`
<!--ID: 1756891680609-->


Q: Что выведется на экран `true` или `false`?
```java
Integer a = Integer.valueOf(5);
Integer b = Integer.valueOf(12);
System.out.println(a.compareTo(b) > 0)
```
A: false, `a.compareTo(b)` вернет отрицательный результат.
<!--ID: 1756891680617-->


