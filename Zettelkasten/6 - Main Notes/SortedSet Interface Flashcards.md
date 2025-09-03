
Theory for the cards: [[SortedSet Interface]]

FILE TAGS: java_core

Q: Какие есть реализации SortedSet?
A: TreeSet
<!--ID: 1756832154452-->


Q: Как получить range-view TreeSet?
A:  
``` java
SortedSet<Character> mySet = new TreeSet(List.of('a', 'b', 'c', 'd', 'e'))
mySet.subSet('a', 'c'); // ['a', 'b']
mySet.tailSet('b'); // ['b', 'c', 'd', 'e']
mySet.headSet('d'); // ['a', 'b', 'c']
```
<!--ID: 1756832154463-->


Q: Как создать копию SortedSet, которая унаследует его компаратор, но при этом избежать пересортировки при копировании?
A: Просто использовать конструктор, принимающий SortedSet:
```java
TreeSet<Character> mySourceSet = new TreeSet(myVerySpecialComparer);
mySourceSet.addAll(List.of('a', 'b', 'c'));
TreeSet<Character> myNewSet = new TreeSet(mySourceSet);
//myNewSet - код внутри конструктора скопирует все элементы в том же порядке без новой сортировки и скопирует себе указатель на myVerySpecialComparer
```
<!--ID: 1756832154471-->


Q: Как получить первый элемент SortedSet?
A: `E first()`
<!--ID: 1756832154479-->


Q: Как получить последний элемент SortedSet?
A: `E last()`
<!--ID: 1756832154486-->

Q: Как получить компаратор коллекции, реализующей SortedSet interface?
A:  метод comparator()
<!--ID: 1756883364762-->


Q: Что вернет вызов .comparator()?
```java
SortedSet<Character> mySet = new TreeSet<>(List.of('a', 'b', 'c'));
Comparator<Character> myComparator = mySet.comparator();
```
A: `myComparator` будет равен `null`, т.к при создании mySet не был явно задан компаратор => используется natural ordering
<!--ID: 1756883364770-->

Q: В каком порядке итератор проходит элементы SortedSet? 
A: По возрастанию (если natural ordering).
<!--ID: 1756885018691-->
