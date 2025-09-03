
Theory for the cards:  [[ListIterator]]

FILE TAGS: java_core

Q: Что выведется на экран:
```java
ListIterator<Character> iter = new ArrayList(List.of('a', 'b', 'c'));
iter.next();
System.out.println(iter.set('g'));
```
A: Ничего, метод set(T ...) класса Iterator возвращает void. НЕ ПУТАТЬ с методом класса List. (он возвращает заменённый элемент)
<!--ID: 1756809589739-->



Q: Произойдет ли корректно замена? Как будет выглядеть список в результате?
```java
ListIterator<Character> iter = new ArrayList(List.of('a', 'b', 'c'));
iter.next();
iter.previous();
iter.set('g');
```
A: замена произойдет корректно. 
- `iter.next();`  
    → возвращает `'a'`.  
    → курсор теперь стоит **перед `'b'`**, но «последний возвращённый элемент» = `'a'`.
    
- `iter.previous();`  
    → возвращает `'a'` снова (мы шагнули назад).  
    → курсор теперь стоит **перед `'a'`**, а «последний возвращённый элемент» снова `'a'`.
    
- `iter.set('g');`  
    → заменяет **последний возвращённый элемент**, т.е. `'a'`, на `'g'`.
<!--ID: 1756809589753-->


Q: Как изменится список?
```java
ListIterator<Character> iter = new ArrayList(List.of('a', 'b', 'c'));
iter.next();
iter.previous();
iter.add('g');
```
A: 1. `[^'a', 'b', 'c']`
2.  `['a', ^'b', 'c']` - вызов `next()` => получаем `'a'` 
3.  `[^'a', 'b', 'c']` - вызов `previous()` => получаем `'a'`
4. `['g', 'a', 'b', 'c']`- вызов `add('g') `
<!--ID: 1756809589760-->


Q: Как получить индекс элемента, стоящего после текущего значения итератора ListIterator? (Индекс элеента, который мы получим следующим вызовом next())
A: myIterator.nextIndex()
<!--ID: 1756809589767-->


Q: Как получить индекс элемента, стоящего перед текущим значением итератора ListIterator? (Индекс элеента, который мы получим следующим вызовом previous())
A: myIterator.previousIndex();
<!--ID: 1756809589775-->

