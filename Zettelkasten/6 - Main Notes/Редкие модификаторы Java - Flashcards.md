
Theory for the cards: [[Редкие модификаторы Java]]

FILE TAGS: java_core

Q: Что делает модификатор `strictfp`?
A: Ничего, это устаревший модификатор.
<!--ID: 1757496289930-->


Q: Что необычного будет в поведении такого класса?
```java
public strictfp class Foo1 {  
	int x;
    public Foo1() { }  
    public void setX(int x){
	    this.x = x;
    }
	public void print(){
		System.out.println("x = " + x);
	}
}
```
A: Ничего, модификатор `strictfp` - устаревший и никак не влияет на поведение класса ни при компиляции, ни в рантайме.
<!--ID: 1757496289935-->
