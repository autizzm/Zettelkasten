
02-09-2025 19:15

Status: #child

Tags: [[Java Core]] [[Java Collections]]

---
# `SortedSet<E> (extends Set<E>)` Interface

### Реализации

- TreeSet


>[!note]
>Итератор проходит SortedSet по возрастанию




>[!note]
>У SortedSet есть специальный конструктор, принимающий SortedSet. Он создает копию, сохраняя порядок и компаратор, если он установлен в копируемом Set. При этом НЕ совершает ПЕРЕСОРТИРОВКИ значений.

Code sample:
```java
TreeSet<Character> mySourceSet = new TreeSet(myVerySpecialComparer);
mySourceSet.addAll(List.of('a', 'b', 'c'));
TreeSet<Character> myNewSet = new TreeSet(mySourceSet);
//myNewSet - под внутри конструктора скопирует все элементы в том же порядке без новойй сортировки и скопирует себе указатель на myVerySpecialComparer
```

### Useful Methods:

- *E* ~={green}first() =~- возвращает первый элемент
- *E*~={green} last()=~ - возвращает последний элемент
- *`Comparator<E>`* ~={green}comparator()=~ - возвращает компаратор этого SortedSet ~={red}или `null,` если используется=~ [[Comparable Interface|natural ordering]]
~={cyan} `+` наследует методы Set=~

#### Range-Veiw methods:

- *`SortedSet<E>`* ~={green}subSet(`E fromEl, E toEl`)=~
- *`SortedSet<E>`* ~={green}headSet(`E toEl`)=~
- *`SortedSet<E>`* ~={green}tailSet(`E fromEl`)=~

----
#### [[SortedSet Interface Flashcards|Link to flashcards]]



---
### References:

- [[SortedMap Interface]]
- [[Comparable Interface]]