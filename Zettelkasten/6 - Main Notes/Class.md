
03-09-2025 13:33

Status:

Tags: [[Java Classes]] [[Java Core]]

---
# Class

В Java ~={red}***все*** классы=~ наследуют Object. (~={green}Даже Abstract классы=~)

>[!note]
>В Java класс может наследовать ==только один класс==

### Вызов конструктора Parent класса

>[!warning]
>При вызове конструктора Дочернего класса перед выполнением кода конструктора вызывается конструктор Родительского класса

```java
class Parent{
	Parent(){
		System.out.println("Parent is being created");
	}
}

class Child extends Parent{
	Child(){
		//неявный вызов super();
		System.out.println("Child is being created");
	}

	public static void main(String[] args){
		Child child = new Child(); // Выведет: Parent is being created\Child is being created\n
	} 
}
```

Конструктор класса может быть ~={purple}private=~ => объект класса можно будет создать ~={orange}только внутри класса (его методах).=~
- Fabrique method
- immutable objects


### Вызов другого конструктора этого же класса

> [!warning]
> В Java вызов другого конструктора **через `this(...)`** (или конструктора родителя через `super(...)`) должен быть **самой первой строкой** конструктора.  

Поэтому такой код:

```java
Circle() 
{
     System.out.println("Child is created");
     this(0,0); // вызовет ошибку
     }
```


### Неявный вызов `super()`

>[!note]
 super() вызывается только если первой строкой явно не указан super(...) или this(...). 

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


## Инициализация класса:

[[Detailed Initialization Procedure - Java|Порядок инициализации]]

> [!warning] **Инициализация зависимостей**
> Первым делом рекурсивно инициализируется суперкласс и все суперинтерфейсы с default-методами


> [!note] **Константы инициализируются первыми**
> Константы инициализируются перед простыми static final полями интерфейса.
> ([[Initializer; constants & just static finals - Java|Разница между static final и константами]])


> [!warning] **Клас инициализируется ==lazily==**
> [[Initializer; constants & just static finals - Java|Инициализаторы]] выполняются только при первом вызове static переменной класса.

----
#### [[Class Flashcards|Link to flashcards]]



---
### References:

- [[Access Modifiers]]
- [[Жизненный цикл класса Java]]
