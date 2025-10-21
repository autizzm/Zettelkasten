
03-09-2025 12:06

Status: 

Tags: [[Java Core]] 

---
# Comparable Interface

Чтобы сортировать объекты by ~={red}***natural ordering***=~ они должны реализовывать Comparable.

В том числе необходим и для сортировки с помощью `Collections.sort(List<T> list)` и `Arrays.sort(Object[] array)`.

>[!note]
>Результат e1.compareTo(e2) == 0 должен быть равен e1.equals(e2), иначе это может вызвать неопределенной поведение в сортированных коллекциях.


### Method:

- ~={green}compareTo(`Object obj`)=~ - отрицательный результат, ноль, положительный результат означают соответственно, что ***this*** меньше, равен, больше чем `obj`

----
#### [[Comparable Interface Flashcards|Link to flashcards]]



---
### References:

- [[SortedMap Interface]]
- [[SortedSet Interface]]
- [[Comparator Interface]]
