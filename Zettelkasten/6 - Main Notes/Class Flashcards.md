
Theory for the cards: [[Class]]

FILE TAGS: java_core

Q: Все ли классы в Java наследуют Object?
A: Да
<!--ID: 1756906250736-->


Q: Наследуют ли Abstract классы класс Object?
A: Да
<!--ID: 1756906250756-->


Q: Что здесь выведется в консоль ?
```java
class Parent{
		Parent(){
			System.out.println("Parent is being created");
		}
	}
	
	class Child extends Parent{
		Child(){
			System.out.println("Child is being created");
		}
		
		public static void main(String[] args){
			Child child = new Child(); 
		} 
}
```

A: Выведет "Parent is being created\Child is being created\n" т.к. При вызове конструктора Дочернего класса перед выполнением кода конструктора вызывается конструктор Родительского класса.
<!--ID: 1756906695366-->




Q: Что здесь выведется в консоль?
```java
class Parent{
		Parent(int i){
			System.out.println("Parent is being created");
		}
	}
	
	class Child extends Parent{
		Child(){
			System.out.println("Child is being created");
		}
		
		public static void main(String[] args){
			Child child = new Child();
		} 
}
```
A: Возникнет исключение, т.к. у родительского класса нет Default конструктора (т.к. мы прописали свой) и, при неявном вызове super() в конструкторе Child, он не находит его.
Способы решения:
1. Прописать default конструктор в Parent
2. Явно вызвать уже прописанный конструктор с параметрами в конструкторе Child:
```java
	Child(){
		super(1);
		System.out.println("Child is being created");
	}
```
<!--ID: 1756906706520-->





Q: Можно ли вызвать private конструктор родительского класса в дочернем?
A: нет
<!--ID: 1756906250765-->


Q: Будет ли вызван конструктер суперкласса в первой строке конструктора `Circle()`?
```java
class Circle{
	...
	
	Circle(int x, int y){
		this.x = x;
		this.y = y;
	}
	
	Circle(){
		this(0, 0);
	}
}
```

A:  Нет
```java
class Circle{
	...
	
	Circle(int x, int y){
		// super() неявно вызывается только здесь
		this.x = x;
		this.y = y;
	}
	
	Circle(){
		this(0, 0); // а здесь - нет
	}
}
```
<!--ID: 1756906654844-->



Q: Где можно вызывать super(...) и this(...)?
A: Только в первой строке конструктора и одновременно вызывать их нельзя.
<!--ID: 1756906250774-->
