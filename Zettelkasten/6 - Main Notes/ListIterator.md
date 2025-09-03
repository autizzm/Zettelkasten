
02-09-2025 12:37

Status: #baby

Tags: [[Java Core]] [[Java Collections]]

---
# `ListIterator<E> extends Iterator<E>


### Эксклюзивные методы ListIterator:

-  ~={green}hasPrevious()=~
-  ~={green}previous()=~
-  *void* ~={green}set()=~ - Переписывает последний элемент, возвращенный next() *~={green}или previous. =~*~={red}НЕ ВОЗРАЩАЕТ=~  предыдущее значение. ~={yellow}НЕ ПУТАТЬ с методом List=~.
- *void* ~={green}add( E e )=~ - вставляет элемент перед позицией итератора

- *int* ~={green}nextIndex()=~ - возвратит индекс элемента после текущего. (элемента, который будет возвращен следующим вызовом  next())
- *int* ~={green}previousIndex() =~- возвратит индекс элемента, предшествующего текущему. (элемента, который будет возвращен следующим вызовом previous())




----
#### [[ListIterator Flashcards|Link to flashcards]]



---
### References:

- [[Iterator]]
- [[List Interface]]