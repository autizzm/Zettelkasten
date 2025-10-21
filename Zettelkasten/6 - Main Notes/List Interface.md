
02-09-2025 12:07

Status: #child

Tags: [[Java Core]] [[Java Collections]]

---
# List Interface


### Реализации

|Операция|ArrayList|LinkedList|
|---|---|---|
|`get(i)` / `set(i,v)`|O(1)|O(n)|
|Добавление в конец|Амортиз. O(1)|O(1)|
|Вставка/удаление в сер.|O(n) (сдвиги)|O(n) (поиск) + O(1)|
|Итерация по списку|Быстро (кэш)|Медленнее (разброс узлов в памяти)|


### Useful Methods:

#### ~={orange}Static=~

- ~={green}List.of(=~ *массив* ~={green})=~ - возвращает ***~={red}unmodifiable=~*** list

>[!warning]
>у списка, полученного методом List.of(...) нельзя получить итератор. При попытке вызова метода .listIterator() => UnsupportedOperationException
>


#### ~={orange}Non Static=~

- ~={green}get()=~
- *T* ~={green}set()=~ - возвращает предыдущее значение (которое перезаписывается)
- *T* ~={green}remove()=~  - возвращает предыдущее значение (которое перезаписывается)
- *ListIterator* ~={green}listIterator()=~ - возвращает [[ListIterator]] (extends [[Iterator]]) 

- `boolean` ~={green}**removeIf(`Predicate<? super T> predicate`)**=~  - true - if any elements were removed
### [[List Range-View Operations]]



----
#### [[List Interface Flashcards|Link to flashcards]]



---
### References:

- [[Iterator]]
- [[ListIterator]]
- [[Collections class]]
- [[List Range-View Operations]]