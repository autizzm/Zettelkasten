
09-09-2025 12:47

Status: #baby

Tags: [[Java Classes]] [[Java Core]]

---
# Member Classes Java

Это классы, объявленные внутри других классов.


>[!note]
>static modifier может быть применен ИСКЛЮЧИТЕЛЬНО к Member классам => превращает Inner в Nested

>[!note]
>Member класс может быть определен в интерфейсе


>[!warning]
>Неявный конструктор member класса наследует модификатор доступа этого класса:
>```java
>class Outer{
>	...
>	protected class Inner{
>		//компилятор сам создает protected Inner();
>	}
>}
>```


----
#### [[Member Classes Java Flashcards|Link to flashcards]]



---
### References:

- [[Inner Classes Java]]
- [[Nested Classes Java]]
