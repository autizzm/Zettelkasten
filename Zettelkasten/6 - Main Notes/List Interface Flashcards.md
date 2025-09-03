
Theory for the cards: [[List Interface]]

FILE TAGS: java_core

Q: Какая реализация List более предпочтительна, если нужно часто вставлять по индексу (`get(int index)`)?
A: ArrayList
<!--ID: 1756809589799-->


Q: Какая реализация List более предпочтительна, если нужно использовать random access?
A: ArrayList
<!--ID: 1756809589807-->


Q: Какая реализация List более предпочтительна, если часто нужно добавлять/удалять элементы из концов списка?
A: LinkedList
<!--ID: 1756809589813-->


Q:Что здесь не так?
```java
ListIterator<Character> iter = List.of('a', 'b', 'c').listIterator();
```
A: Список, полученный методом List.of( массив ), является UNMODIFIABLE => получить итератор по нему нельзя => UnsupportedOperationException
