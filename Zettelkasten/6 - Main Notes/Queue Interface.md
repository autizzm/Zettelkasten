
02-09-2025 16:43

Status: #child 

Tags: [[Java Core]] [[Java Collections]]

---
# Queue Interface

## FIFO 

#### Implementations:
- LinkedList
- PriorityQueue
- ArrayDeque
- ConcurrentLinkedList

#### + еще поддерживающие blocking operations:
- ArrayBlockingQueue
- LinkedBlockingQueue
- PriorityBlockingQueue
Методы для блокировки очереди -> java.util.concurrent.BlockingQueue

> [!note]
> Только blocking-очереди могут иметь фиксированный размер



### Operations:

Queue помимо методов из Collection предлагает по два метода на операции вставки, чтения и удаления: один выкидывает исключение, а другой - нет.

| Type of operation                           | Throws exception | Returns special value | The value |
| ------------------------------------------- | ---------------- | --------------------- | --------- |
| вставка                                     | .add(e)          | offer(e)              | false     |
| remove from the head (и получение значения) | remove()         | poll()                | null      |
| return the head but don't remove            | element()        | peek()                | null      |

>[!note]
.offer(e) возвратит null, если Размер очереди уже равен максимальной (только в блокируемых очередях) и он не сможет вставить новое значение.



----
#### [[Queue Interface Flashcards|Link to flashcards]]



---
### References:

