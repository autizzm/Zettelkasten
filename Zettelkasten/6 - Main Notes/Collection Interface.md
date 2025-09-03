
02-09-2025 09:37

Status: #child 

Tags: [[Java Core]] [[Java Collections]]

---
# Collection Interface

> [!warning]
> Map не является реализацией Collection

### SubInterfaces:
- List
- Queue
- Deque
- Set -> SortedSet

#### Из любой реализации Collection можно сделать любую другую реализацию Collection:

> [!note]
> Каждая реализация Collection имеет конструктор, принимающий Collection

~={red}WARNING: могут терятся элементы при конвертации из допускающих дублирование коллекций в недопускающие.=~
```java
Collection<String> c = new SortedSet(...);
List<String> list = new ArrayList<>(c);
```


### Useful Methods:
- ~={green}size()=~
- ~={green}isEmpty()=~
- *boolean* ~={green}contains( Object element )=~
- ~={green}add( E element )=~
- *boolean*~={green} remove( Object o )=~ - returns `true` if element was deleted successfully, если такого элемента нет - просто возвращает `false`
- *`Iterator<E>`*~={green} iterator()=~ - возвращает итератор по этой коллекции
- *`Object[]`* ~={green}toArray()=~ - превращает коллекцию в массив
- *`Stream<E>`* ~={green}stream()=~ - создает Stream из элементов коллекции
- ~={green}clear()=~ - очищает коллекцию

##### Bulk operations:

- *boolean*  ~={green}addAll(`Collection<E> coll`)=~ - добавит все элементы из коллекции `coll`; ~={orange}returns true -  if this collection changed as a result of the call=~
- *boolean*  ~={green}containsAll(`Collection<E> coll`) =~- вернет true, если в коллекции есть все элементы коллекции coll
- *boolean* ~={green} removeAll(`Collection<E> coll`) =~- ~={orange}returns true  - if this collection changed as a result of the call=~
- *boolean* ~={green} retainAll(`Collection<E> coll`) =~- ~={orange}returns true  - if this collection changed as a result of the call

### Способы прохождения коллекций

1. Aggregate operations => Stream API
2.  for-each  ~={red} WARNING: не позволяет удалять элементы=~
3.  iterator

> [!note]
> Iterator - лучший способ прохождения коллекций с возможностью удаления элементов


> [!warning]
> **Если вызвать `collection.add(...)` или `collection.remove(...)` прямо внутри цикла** `for-each`  → будет выброшено `ConcurrentModificationException`.


----
#### [[Collection Interface Flashcards|Link to flashcards]]



---
### References:

-  [[Set Interface]]
-  [[List Interface]]
