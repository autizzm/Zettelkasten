
Theory for the cards:  [[Functional Interfaces - Java]]

FILE TAGS: java_core

Q: Что здесь не так?
```java
@FunctionalInterface  
public interface TripleFunction<T, U, N, R> {  
    R apply(T t, U u, N n);  
    
    void close();
}
```
A: Здесь два abstract метода, в функциональном интерфейсе. Это запрещено.
(Но можно любое количество static и default методов).
<!--ID: 1758637316219-->


Q: Что здесь не так?
```java
@FunctionalInterface  
public interface TripleFunction<T, U, N, R> {  
    R apply(T t, U u, N n);  
    
    default void close(){
	    System.out.println("closing");
    }
    
    static int simpleAdd(int a, int b){
	    return a+b;
    }
}
```
A: Здесь всё ок. Функциональный интерфейс должен содержать ТОЛЬКО ОДИН абстрактный метод.
Но default и static методов можно сколько угодно.
<!--ID: 1758637316240-->


Q: Объясни, как это работает:
```java
@FunctionalInterface  
public interface TripleFunction<T, U, N, R> {  
    R apply(T t, U u, N n);  
}
...
	
TripleFunction<Integer, Integer, Integer, Integer> sum = (a, b, c) -> a + b + c;
System.out.println(sum.apply(1, 2, 3)); // 6
```
A: Когда компилятор встречает лямбду (или ссылку на метод), он создает `invokedynamic` инструкцию:
- создаёт одноразовый класс (просто байткод)
- вместо реализации единственного абстрактного метода подставляет код этой лямбды (или метода, если используем ссылку на метод)
- возвращает invokedynamic конструкцию -> sum теперь указывает на созданную `invokedynamic` конструкцию (аналог класса, реализующего интерфейс `TripleFunction<T, U, N, R>`)
<!--ID: 1758637316247-->
