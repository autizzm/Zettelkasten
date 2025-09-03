
02-09-2025 14:13

Status: #child 

Tags: [[Java Core]] [[Java Collections]]

---
# List Range-View Operations

>[!note]
>Range-view содержит ссылки на элементы исходного списка, НЕ УКАЗАТЕЛИ. Ranage-View - это часть списка.

### Range-View методы класса List

- *`List<E>`* ~={green}sublist(`int fromIndex, int toIndex`)=~ - `['a', ('b', 'c' ), 'd']` в скобках - полученный range-view. 

~={yellow}Use only as a  =~ ~={orange}TRANSIT OBJECT=~:
```java
List<E> handView = deck.sublist( ..., ...); //handView - range-View list
List<E> hand = new ArrayList(handView); // сделали копию Range-View списка
handView.clear(); //приведет к удалению этих элементов из deck
```

Сделали Range-View => сделали его копию => забыли про список range-View

> [!warning]
> Удалив начальный список (тут deck) до удаления Range-View (тут handView), при попытке взаимодействия с этим range-view => получим ConcurrentModificationException

```java
List<String> list = new ArrayList<>(List.of("a", "b", "c", "d"));
List<String> sub = list.subList(1, 3); // [b, c]

list.add("z");        // изменяем исходный список напрямую
sub.get(0);           // 💥 ConcurrentModificationException
```


> [!warning]
> Если добавить или удалить элемент из оригинального списка, то при попытке взаимодействия с range-view списком => получим ConcurrentModificationException


----
#### [[List Range-View Operations Flashcards|Link to flashcards]]



---
### References:

