
Theory for the cards: [[Number classes Java]]

FILE TAGS: java_core

Q: Что выведет следующий код:
```java
Integer int1 = Integer.valueOf(5);
System.out.println(int1.compareTo(7););
```
A: -2
<!--ID: 1756740789905-->


Q: Как получить примитивное значение из оберточного типа (наследника Number)? Например, как получить int из Integer?
A: методом .xxxValue. `int a = myInteger.intValue();`
<!--ID: 1756740789918-->


Q: Как получить оберточное значение (наследник Number) из примитивного?
A: static методом .valueOf( String/ int ): `Integer a = Intteger.valueOf(5);`
<!--ID: 1756891680628-->


Q: Каким образом лучше всего получить ПРИМИТИВНОЕ числовое значение из строки? (например, из строки  "5")
A: static методом .parseXXXX( String ). Пример: `int a = Integer.parseInt("5");`
<!--ID: 1756740789926-->


Q: Изменятся ли значения a и b после вызова modify()?
```java
Integer a = 1;
Integer b = 2;
void modify(Integer x, Integer y){
	x = 5;
	y = 3;
}
modify(a, b);
```
A: Нет, не изменятся. Т.к. наследники Number - неизменяемы, а в функцию передается копии на указатели a и b.
<!--ID: 1756740928977-->
