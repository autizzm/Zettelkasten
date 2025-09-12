
Theory for the cards: [[Наследование интерфейсов Java]]

FILE TAGS: java_core

Q: Что произойдёт, если два суперинтерфейса содержат поля с одинаковыми именами?  
```java
interface I1 { int X = 1; }
interface I2 { int X = 2; }
class C implements I1, I2 {
    void test() {
        System.out.println(X);
    }
}
```
A: Это не ошибка компиляции, но имя становится неоднозначным. Нужно указывать квалификатор (Имя интерфейса):
```java
interface I1 { int X = 1; }
interface I2 { int X = 2; }
class C implements I1, I2 {
    void test() {
        // System.out.println(X); // ❌ ошибка: ambiguous
        System.out.println(I1.X); // ✅
        System.out.println(I2.X); // ✅
    }
}
```
<!--ID: 1757700639213-->


---

Q: Что будет, если два интерфейса объявляют метод с одинаковой сигнатурой и одинаковым возвращаемым типом?  
A: Конфликта нет, метод просто наследуется.
<!--ID: 1757700639221-->


---

Q:  Как здесь будет решен конфликт сигнатур методов ?
```java
interface I1 { Number m(); }
interface I2 { String m(); }  
interface Bad extends I1, I2 {
	public static void main(String[] args){
		Bar bar = new Bar();
		bar.m();
	}
} 
```
A: Это ошибка компиляции
<!--ID: 1757700715034-->


---
Q:  Как здесь будет решен конфликт сигнатур методов?
```java
interface I1 { Number m(); }
class cl2 { String m(); }  
class Bad extends cl2 implements I2 {
	public static void main(String[] args){
		Bar bar = new Bar();
		bar.m();
	}
} 

```
A: Это ошибка компиляции

---

Q: Что произойдёт, если два интерфейса содержат одинаковые `default` методы?  
A: Наследующий класс обязан переопределить метод и разрешить конфликт
<!--ID: 1757700639226-->


---

Q: Как здесь будет решен конфликт default методов? Какой методунаследует C?
```java
interface I1 {
     default void hello() { System.out.println("I1"); } 
} 
	
interface I2 {
     default void hello() { System.out.println("I2"); } 
	}
class C implements I1, I2 {
	...
}
```
A: Тут будет ошибка. Конфликт default методов нужно решать переопределением этого метода в классе:
```java
interface I1 {
     default void hello() { System.out.println("I1"); } 
} 
	
interface I2 {
     default void hello() { System.out.println("I2"); } 
}
	
class C implements I1, I2 {
     @Override     
     public void hello() {
	    System.out.println("Resolved"); 
	} // ✅ }```
```
<!--ID: 1757700701344-->


---

Q: Что здесь произойдет?
```java
class A {
    void hello() { System.out.println("A"); }
}
interface I {
    void hello();
}
class C extends A implements I {} // использует метод из A
```
A: Если в суперинтерфейсе определен абстрактный метод, а в суперклассе - метод с той же сигнатурой -> Метод суперкласса считается реализацией интерфейсного метода.
<!--ID: 1757700639232-->

---

Q: Что произойдёт? Как будет решен конфликт имен полей?
```java
class A { static int X = 1; }
interface I { int X = 2; }
class C extends A implements I {
    void test() {
        System.out.println(X);
    }
}
```
A: При обращении без квалификатора используется поле суперкласса. К полю интерфейса можно обратиться через `I.X`:
```java
class A { static int X = 1; }
interface I { int X = 2; }
class C extends A implements I {
    void test() {
        System.out.println(X);   // берётся A.X
        System.out.println(I.X); // обращение к интерфейсу
    }
}
```
<!--ID: 1757700639236-->

---

Q: Что произойдёт, если суперкласс содержит метод, а интерфейс — `default` метод с той же сигнатурой?  
A: Выбирается метод суперкласса (правило _"class wins"_):
```java
class A {
    void hello() { System.out.println("A"); }
}
interface I {
    default void hello() { System.out.println("I"); }
}
class C extends A implements I {} // использует метод из A
```
<!--ID: 1757700639240-->


---

Q: Что будет, если суперкласс и интерфейс объявляют методы с одинаковой сигнатурой, но несовместимыми возвращаемыми типами?  
A: Это ошибка компиляции.
<!--ID: 1757700639245-->

---

Q: Наследуются ли `static` методы интерфейсов ?  
A: Нет. Они доступны только через квалификатор (имя интерфейса):
`I1.utilMethod();`
<!--ID: 1757700835929-->

---

Q: Что произойдёт, если суперкласс содержит метод и интерфейс объявляет такой же абстрактный метод ?  
A:  Метод суперкласса считается реализацией интерфейсного метода.
<!--ID: 1757700835936-->



