
02-09-2025 13:55

Status: #child

Tags: [[Java Core]] [[Java Collections]]

---
# Collections class

Содержит статические методы для которые используют или возвращают коллекции.
(Аналог Arrays класса для массивов)

#### Useful Methods:
- ~={green}Collections.~={yellow}swap=~( `List<?> list, int i, int j`)=~ - переставляет элементы по индексам i и j

- ~={green}Collections.~={yellow}shuffle=~( =~`List<?> list, Random rnd`~={green})=~ - перемешает элементы списка

- ~={green}Collections.~={yellow}min=~(`Collection<E> collection` )=~ 
- ~={green}Collections.~={yellow}min=~(`Collection<E> collection, Comparator<E> comp` )=~ - возвратит минимальный элемент коллекции, сравнивая компараторои
(для max то же самое)

- ~={green}Collections.~={yellow}reverse=~(`List<E> list`)=~ - разворачивает список

- *int*  ~={green}Collections.~={yellow}binarySearch=~(`List<T extends Comparable> list, T key`)=~ - бинарный поиск в списке; возвращает индекс искомого ключа; **если ключа нет в списке => выведет отрицательное значение**
 
- ~={green}Collections.~={yellow}copy=~(`List<E> dest, List<E> source`)=~ - копирует все элементы из source в dest

- ~={green}Collections.~={yellow}fill=~(`List<E> list, E obj`)=~ - заменит все элементы списка на obj

----
#### [[Collections class Flashcards|Link to flashcards]]



---
### References:

