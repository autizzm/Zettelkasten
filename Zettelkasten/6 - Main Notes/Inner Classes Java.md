
09-09-2025 10:31

Status: #baby 

Tags: [[Java Classes]] [[Java Core]]

---
# Inner Classes Java

1. Имеют доступ к полям Enclosing класса:
```java
class WithDeepNesting {  
    boolean toBe;  
    WithDeepNesting(boolean b) { toBe = b; }  
  
    class Inner {  
        boolean theQuestion;  
        class DeepInner {  
            DeepInner(){  
                theQuestion = toBe || !toBe;  
            }  
        }  
    }  
}
```
>[!note]
>Дважды вложенный класс имеет доступ к переменным Обоих Enclosing классов!




2. ~={orange}Принадлежат объекту=~ (==instance==) Enclosing класса.

> [!warning]
> Inner классы должны создаваться через объект Parent или Child класса. Однако такая запись в Child классах тоже возможна:
> ```java
> class InPackageChild extends A{
> 	void test() {
> 		InPackageChild.Inner inner = new A.Inner(); // ✅ компилируется
> 	}
> }
> ```
>  Однако "под капотом" это преобразовывается в:
>  ```java
>  InPackageChild.Inner inner = this.new Inner();
>  ```



3. Inner класс требует экземпляр внешнего класса для создания:
```java
public class Enclosing {  
    private int x = 9;  
      
    protected class Inner{  
        public void print(){  
            System.out.println("x of the Enclosing class = " + x);  
        }  
    }  
    
    public static void main(String[] args){
	    Enclosing.Inner inner = new Enclosing.Inner(); // ❌ ошибка, т.к.это не static метод => не можем переделать в this.new Inner();
	    Enclosing.Inner inner = ???.new Inner(); -> нужен объект Enclosing класса:
	    Enclosing.Inner inner = new Enclosing().new Inner(); // ✅
    }
}
```



4. Может ~={cyan}реализовывать=~ ~={green}интерфейсы=~, расширять~={green} top-level =~и ~={green}member classes=~, расширять ~={green}abstract classes=~ 


### Наследование Inner классов

1. Можно наследовать ~={orange}внутри=~ других классов ~={orange}наследников Enclosing class=~, где позволяет видимость

2. Можно наследовать ~={orange}внутри=~ других классов ~={orange}НЕ наследников Enclosing=~, но придется тоже передавать сущность Enclosing класса в конструктор:
```java 
package package_one;  
  
public class A {  
    public int x;  
    protected int y;  
    int z;
}

package package_one;  
  
public class SomeClass {  
    private class SomeInner extends A.Inner{  
        SomeInner(A a){  
            a.super();  
        }  
    }  
}
```


3. Можно наследовать top-level классом, но создавать все равно придется с помощью сущности Enclosing класса:
```java
package p1;

public class Outer {
    public class Inner {
        public void hello() {
            System.out.println("Hello from Inner");
        }
    }
}


package p2;

import p1.Outer;

public class SubInner extends Outer.Inner {
    public SubInner(Outer outer) {
        // нужно явно передать внешний объект
        outer.super(); 
    }

    @Override
    public void hello() {
        System.out.println("Hello from SubInner");
    }
}
   ```

----
#### [[Inner Classes Java - Flashcards|Link to flashcards]]



---
### References:

- [[Member Classes Java]]
- [[Nested Classes Java]]