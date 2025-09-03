
Theory for the cards: [[Iterator]]

FILE TAGS: java_core

Q: Можно ли удалить элемент методом итератора remove() до первого вызова next()?
A: Нет, нельзя, возникнет IllegalStateException.
<!--ID: 1756809589783-->


Q: Какой элемент удалится при вызове remove():
```java
Iterator<Charracter> iter = List.of('a', 'b', 'c').iterator();
iter.next();
iter.remove();
```
A: Удалится `'a'`, т.к. это последний элемент, который был возвращен методом next() (элемент, предшествующий текущему положению итератора. Текущее положение - на `'b'`)
<!--ID: 1756809589791-->



