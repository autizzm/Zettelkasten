
Theory for the cards: [[SortedMap Interface]]

FILE TAGS: java_core


Q: Какие есть реализации SortedMap?
A: TreeMap
<!--ID: 1756885018661-->


Q: Можно ли создать SortedMap на основе другой коллекции так, как это можно сделать у наследников Collection?
A: Да, у SortedMap (т.к. он расширяет Map,  он наследует его рекомендации по реализации конструкторов) есть конструктор, принимающий исключительно Map:
```java
SortedMap(Map<K,V> map);
```
<!--ID: 1756885018674-->


Q: Как получить первый ключ SortedMap?
A:  
```java
TreeMap<Character, Integer> myMap = new TreeMap(Map.of('a', 1, 'b', 2, 'c', 3));
Character key = myMap.firstKey(); // key = 'a'
```
<!--ID: 1756886989735-->


Q: Как получить последний ключ SortedMap?
A:  
```java
TreeMap<Character, Integer> myMap = new TreeMap(Map.of('a', 1, 'b', 2, 'c', 3));
Character key = myMap.lastKey(); // key = 'c'
```
<!--ID: 1756887036935-->



Q: Как получить компаратор TreeMap?
A:  метод `comparator()` (наследован от Map):
``` java
TreeMap<Character, Integer> mySourceMap = new TreeMap(myVerySpecialComparer);
Comparator<Character> theComparator = mySourceMap.comparator();
```
<!--ID: 1756887036943-->


Q: Есть ли у Map операции типа range-view ( не collection-view)?
A: Нет, в этом нет смысла, т.к. она не отсортирована.
<!--ID: 1756886989755-->



Q: Как получить range-view TreeMap?
A:  
``` java
TreeMap<Character, Integer> myMap = new TreeMap(myVerySpecialComparer);
myMap.putAll(Map.of('a', 1, 'b', 2, 'c', 3, 'd', 4, 'e', 5));
myMap.subMap('a', 'c'); // ['a' - 1, 'b' - 2]
myMap.tailSet('b'); // ['b' - 2 , 'c' - 3, 'd' - 4, 'e' - 5]
myMap.headSet('d'); // ['a' - 1, 'b' - 2, 'c' - 3]
```
<!--ID: 1756886989763-->

Q: Как создать копию SortedMap, которая унаследует его компаратор, но при этом избежать пересортировки при копировании?
A: Просто использовать конструктор, принимающий SortedSet:
```java
TreeMap<Character, Integer> mySourceMap = new TreeMap(myVerySpecialComparer);
mySourceMap.putAll(Map.of('a', 1, 'b', 2, 'c', 3));
TreeMap<Character, Integer> myNewMap = new TreeSet(mySourceSet);
```
myNewSet - код внутри конструктора скопирует все элементы в том же порядке без новой сортировки и скопирует себе указатель на myVerySpecialComparer
<!--ID: 1756887339508-->


Q: Какие range-view операции поддерживает SortedMap ?
A:  
``` java
//Операции типа collection-view:
myMap.keySet();
myMap.values();
myMap.entrySet();
//Операции типа range-view
myMap.subMap('a', 'c'); // ['a' - 1, 'b' - 2]
myMap.tailSet('b'); // ['b' - 2 , 'c' - 3, 'd' - 4, 'e' - 5]
myMap.headSet('d'); // ['a' - 1, 'b' - 2, 'c' - 3]
```
<!--ID: 1756887500612-->

