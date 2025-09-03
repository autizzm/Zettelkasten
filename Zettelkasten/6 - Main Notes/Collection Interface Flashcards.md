
Theory for the cards: [[Collection Interface]]

FILE TAGS: java_core

Q: Does Map implement Collction interface?
A: No, it doesn't.
<!--ID: 1756798010037-->


Q: Как проще всего превратить одну реализацию Collection в другую?
A: С помощью конструктора, принимающего Collection как аргумент:
```java
Collection<String> c = new SortedSet(...);
List<String> list = new ArrayList<>(c);
```
<!--ID: 1756798010042-->


Q: Что здесь произойдет нехорошего при превращении списка в Set:
```java
List<Integer> list = List.of(1, 1, 8, 0, 8);
Collection<String> c = new SortedSet(list);
```
A: Дублированные элементы будут пропущены, т.к. Set не может содержать дублирующих элементов.
<!--ID: 1756798010047-->


Q: Как получить Stream из объекта Collection? (методом интерфейма Collection)
A: myCollection.stream();
<!--ID: 1756798010053-->


Q: Как получить размер коллекции (`<? extends Collection>`) в Java?
A: myCollection.size();
<!--ID: 1756798010058-->


Q: Как проверить наличие элемента в коллекции (`<? extends Collection>`) в Java?
A: myCollection.contains( element ); //returns boolean => true if the element is present
<!--ID: 1756798010063-->


Q; Как получить массив из коллекции  (`<? extends Collection>`)?
A: myCollection.toArray();

Q: Каким способом лучше всего проходить коллекцию, если в процессе этого нужно добавлять и удалять элементы?
A: с помощью итератора
<!--ID: 1756798010068-->


Q: Что произойдет, если в процессе прохождения коллекции в Java с помощью for-each удалить или добавить элемент с помощью методов add() remove()?
A: Если вызвать `collection.add(...)` или `collection.remove(...)` прямо внутри цикла `for-each` → будет выброшено `ConcurrentModificationException`.
<!--ID: 1756798010073-->


Q: Как быстро удалить все элементы в коллекции  (`<? extends Collection>`)?
A: myCollection.clear();
<!--ID: 1756798010078-->

Q: Как в коллекцию добавить все элементы другой коллекции?
A:  `boolean addAll(Collection<E> coll)` - добавит все элементы из коллекции `coll`; returns true -  if this collection changed as a result of the call
<!--ID: 1756832154496-->


Q: Как проверить, имеются ли в list1 элементы `'a', 'b', 'c'` одним методом?
A: `list1.containsAll(Collection.of(`'a', 'b', 'c')); 
<!--ID: 1756832154505-->


Q: Как удалить из коллекции 1 все элементы, которых нет в коллекции 2?
A: `boolean retainAll(Collection<E> coll` - returns true -  if this collection changed as a result of the call
<!--ID: 1756832154512-->


Q: Как удалить из коллекции 1 все элементы, которые есть в коллекции 2?
A: `boolean removeAll(Collection<E> coll` - returns true -  if this collection changed as a result of the call
<!--ID: 1756832154521-->
