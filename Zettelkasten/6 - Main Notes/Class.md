
03-09-2025 13:33

Status:

Tags: [[Java Classes]] [[Java Core]]

---
# Class

В Java ~={red}***все*** классы=~ наследуют Object. (~={green}Даже Abstract классы=~)

>[!note]
>В Java класс может наследовать ==только один класс==

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



----
#### [[Class Flashcards|Link to flashcards]]



---
### References:

