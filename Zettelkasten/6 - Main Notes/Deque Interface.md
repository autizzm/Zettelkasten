
02-09-2025 17:17

Status: #child 

Tags: [[Java Core]] [[Java Collections]]

---
# Deque Interface

## LIFO или FIFO

>[!note]
>Реализация стека в Java устарела => рекомендуется использовать стек через реализации Deque 

| **Stack Method** | **Equivalent `Deque` Method** |
| ---------------- | ----------------------------- |
| `push(e)`        | `addFirst(e)`                 |
| `pop()`          | `removeFirst()`               |
| `peek()`         | `peekFirst()`                 |
### Реализации

- ArrayDeque
- LinkedList
- ConcurrentLinkedDeque
- LinkedBlockingDeque


###  Methods:

Deque помимо методов из Collection предлагает по два метода на операции вставки, чтения и удаления: один выкидывает исключение, а другой - нет.

| Type of operation             | Throws exception                          | Returns special value                      | value |
| ----------------------------- | ----------------------------------------- | ------------------------------------------ | ----- |
| вставка                       | addFirst(e)                   addLast(e)  | offerFirst(e)                 offerLast(e) | false |
| удаление и получение значения | removeFirst(e)          removeLast(e)     | pollFirst()                  pollLast()    | null  |
| только просмотр элемента      | getFirst()                      getLast() | peekFirst()           peekLast()           | null  |

- *boolean*  removeFirstOccurance(`Object o`)
- *boolean*  removeLastOccurance(`Object o`)
	Возвратят true - если элемент успешно удален;
		false - если такого элемента нет.

----
#### [[Deque Interface Flashcards|Link to flashcards]]



---
### References:

