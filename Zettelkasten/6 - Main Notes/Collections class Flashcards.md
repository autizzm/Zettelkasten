
Theory for the cards: [[Collections class]]

FILE TAGS: java_core

Q: Как проще всего поменять местами два элемента в списке List?
A: static методом класса Collections: `Collections.swap( myList, index1, index2);`
<!--ID: 1756811522618-->


Q: Как проще всего перемешать элементы в списке?
A: static методом класса Collections: `Collections.shuffle( myList, new Random())`
<!--ID: 1756811522627-->


Q: Как проще всего найти минимальный(максимальный) элемент коллекции?
A:  static методом класса Collections:
```java
Collections.min(myList);
Collections.max(myList, myComparator);
```
<!--ID: 1756811522633-->

Q: Как одним методом развернуть список?
A: static методом класса Collections: `Collections.reverse(List<E> list)`
<!--ID: 1756820382034-->


Q: Как найти элемент в списке List методом бинарного поиска (вызовом единственного метода)?
A: `Collections.binarySearch( myList, myKey );
<!--ID: 1756820382047-->


Q: Что вернет функция `Collections.binarySearch( myKey );` , если в списке `myList` нет элемента `myKey`?
A: отрицательное значение
<!--ID: 1756820382056-->


Q: Как скопировать элементы из одного списка в другой (существующий)?
A: `Collections(List<E> dest, List<E> source)`
<!--ID: 1756820382064-->


Q: Как заменить все элементы списка на определенное значение?
A: `Collections.fill(List<E> list, E obj)`
<!--ID: 1756820382071-->


Q: Что делает метод `Collections.fill(List<E> list, E obj)`?
A: Заменяет только существующие элементы списка на obj
<!--ID: 1756820382078-->
