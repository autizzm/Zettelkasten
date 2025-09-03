
Theory for the cards: [[Deque Interface]]

FILE TAGS: java_core

Q: Как вставить и удалить элемент в Deque?
A:  
```java
Deque<Character> deck = new ArrayDeque<>();
deck.addLast(element);
deck.addFirst(element);
deck.removeFirst(element);
deck.removeLast(element);
```
<!--ID: 1756824473075-->


Q: Как вставить и удалить элемент в Deque без выкидывания исключений в случае неудачи?
A:  
```java
Deque<Character> deck = new ArrayDeque<>();
deck.offerLast(element); //false - if deque - имеет фиксированный размер && размер deque == максимальному
deck.offerFirst(element); //false - if deque - имеет фиксированный размер && размер deque == максимальному
deck.pollFirst(element); //null - if deque is empty
deck.pollLast(element); //null - if deque is empty
```
<!--ID: 1756824537773-->


Q: Как получить указатель на элемент Deque без его удаления из коллекции? 
A:  
```java
Deque<Character> deck = new ArrayDeque<>(List.of('a', 'b'));
deck.getFirst();
deck.getLast();
```
<!--ID: 1756824537782-->


Q: Как получить указатель на элемент Deque без его удаления из коллекции без выкидывания исключения, если deque пуст? 
A:  
```java
Deque<Character> deck = new ArrayDeque<>(List.of('a', 'b'));
deck.peekFirst();
deck.peekLast();
```
<!--ID: 1756828926558-->


Q: Как удалить первый или последний вход элемента в Deque?
A:  
```java
Deque<Character> deck = new ArrayDeque<>(List.of('a', 'b', 'b', 'a'));
deck.removeFirstOccurance('b'); // returns true | теперь deck = {'a', 'b', 'a'}
deck.removeFirstOccurance('c'); // returns false
deck.removeLastOccurance('a'); //// returns true | теперь deck = {'a', 'b'}
```
<!--ID: 1756824473088-->


