
Theory for the cards: [[Class]]

FILE TAGS: java_core

Q: Все ли классы в Java наследуют Object?
A: Да

Q: Наследуют ли Abstract классы класс Object?
A: Да

Q:  Что здесь выведется в консоль?
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
A: Выведет: "Parent is being created\Child is being created\n" т.к. При вызове конструктора Дочернего класса перед выполнением кода конструктора вызывается конструктор Родительского класса

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

Q: Можно ли вызвать private конструктор родительского класса в дочернем?
A: нет

