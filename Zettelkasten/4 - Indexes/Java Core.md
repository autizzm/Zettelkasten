


### Data Types:
1. [[Primitive Data Types]]
2. [[Ссылочные типы данных - Java Core]]
3. [[String Java]]
4. [[StringBuilder Java]]
5. [[Number classes Java]]
	- [[Autoboxing & Autounboxing - Java Core]]
6. [[Boolean wrapper class Java]]

#### Other topics:
1. [[Math Java]]
2. [[Switch]]
3. [[Cycles]]
4. [[Array]]
5. [[Arrays class]]
6. [[Enum - Java]]

#### Collections:
1. [[Collection Interface]]
2. [[Iterator]]
3. [[Set Interface]]
4. [[SortedSet Interface]]
5. [[List Interface]]
	- [[List Range-View Operations]]
	- [[Collections class]]
	- [[ListIterator]]
6. [[Queue Interface]]
	- [[Deque Interface]]
7. [[Map Interface]]
8. [[SortedMap Interface]]


### Collection implementations
1. [[LinkedList implementation - Java Core]]
2. [[HashMap implementation - Java Core]]
	 - [[ConcurrentHashMap - Java Core]]

##### Collections Time Complexity
1. [[List implementations- Time Complexity]]
2. [[Set Implementations - Time Complexity]]
3. LinkedHashMap implementation

### Java Classes:
[[Java Classes|Link to the tag]]

1. [[Class]]
2. [[Access Modifiers]]
3. [[Member Classes Java]]
	-  [[Inner Classes Java]]
	-  [[Static Nested Classes Java]]
4. [[Local classes Java]]
5. [[Anonymous classes Java]]
6. [[Record - Java]]

> [!tip] Note
> Начиная с Java 16, **все виды inner‑классов** (inner, static nested, локальные и анонимные) получили право объявлять обычные `static` members (fields, methods, static initializer blocks);  
> 
> **static nested классы** и до этого имели полные права на `static`, и продолжают их иметь.


## Interfaces:
[[Interfaces Java|Link to the tag]]

1. [[Interface Java]]
	- [[Наследование интерфейсов Java]]
2. [[Lambda Expressions - Java]]
3. [[Functional Interfaces - Java]]
	- [[Standard Functional Interfaces - Java]]


### Annotations

1. [[Что такое аннотации]]


### Under the hood
[[Under the hood - Java|Link to the tag]]

1. [[Detailed Initialization Procedure - Java]]
    - [[clinit method - Java]]
2. [[Алгоритм динамического связывания, vtable класса - Java Core]]
3. [[Этапы выполнения Java кода - Java Core]]
##### [[JMM]]


### Small important moments
[[Small Important Moments Java|Link to the tag]]

1. [[Неизменяемость наследников Number]]
2. [[Передача по значению в Java]]
3. [[Редкие модификаторы Java]]
4. [[Effectively final - Java]]
5. [[Fully qualified vs Simple name - Java]]
6. [[Initializer; constants & just static finals - Java]]
7. [[assert Java]]
8. [[Classpath - Java]]
9. [[Сравнение в Java]]
10. [[Method parameters & arguments - Java]]
11. [[Модификатор метода final - Java]]
12. [[Порядок вызова конструкторов и initializer blocks - Java Core]]
13. [[static import - Java Core]]
14. [[WeakReference, SoftReference, PhantomReference - Java Core]]


### Java Important Classes
[[Java Important Classes|Link to the tag]]
#### classes:

1. [[Object class - Java Core]]
2. [[Optional class - Java]]

##### interfaces

1. [[AutoClosable Interface - Java]]
2. [[Comparable Interface - Java]]


#### IO/NIO

- [[IO - Java Core]]
- [[File class - Java Core]]
- NIO
- [[Path class - Java Core]]
- Files class 


### [[JMM]]


#### Конченые вопросы:

1. [Что такое autoboxing («автоупаковка») в Java и каковы правила упаковки примитивных типов в классы-обертки?](https://github.com/enhorse/java-interview/blob/master/core.md#%D1%87%D1%82%D0%BE-%D1%82%D0%B0%D0%BA%D0%BE%D0%B5-autoboxing-%D0%B0%D0%B2%D1%82%D0%BE%D1%83%D0%BF%D0%B0%D0%BA%D0%BE%D0%B2%D0%BA%D0%B0-%D0%B2-java-%D0%B8-%D0%BA%D0%B0%D0%BA%D0%BE%D0%B2%D1%8B-%D0%BF%D1%80%D0%B0%D0%B2%D0%B8%D0%BB%D0%B0-%D1%83%D0%BF%D0%B0%D0%BA%D0%BE%D0%B2%D0%BA%D0%B8-%D0%BF%D1%80%D0%B8%D0%BC%D0%B8%D1%82%D0%B8%D0%B2%D0%BD%D1%8B%D1%85-%D1%82%D0%B8%D0%BF%D0%BE%D0%B2-%D0%B2-%D0%BA%D0%BB%D0%B0%D1%81%D1%81%D1%8B-%D0%BE%D0%B1%D0%B5%D1%80%D1%82%D0%BA%D0%B8)
2. [Про клонирование объектов](https://github.com/enhorse/java-interview/blob/master/core.md#%D1%80%D0%B0%D1%81%D1%81%D0%BA%D0%B0%D0%B6%D0%B8%D1%82%D0%B5-%D0%BF%D1%80%D0%BE-%D0%BA%D0%BB%D0%BE%D0%BD%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5-%D0%BE%D0%B1%D1%8A%D0%B5%D0%BA%D1%82%D0%BE%D0%B2)