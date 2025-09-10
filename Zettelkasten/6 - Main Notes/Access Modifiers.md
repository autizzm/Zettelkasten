
03-09-2025 16:40

Status: #child 

Tags: [[Java Core]] [[Java Classes]]

---
# Access Modifiers

>[!warning]
>Acess modifiers контролируют доступ к полю. Они не убирают поле при наследовании, просто запрещают к нему доступ.
>```java
>package points;
>class Point{
>	int x;
>	int y;
>	public void move(int dx, int dy){
>		this.x += dx;
>		this.y += dy;
>	}
>}
>
>package points;
>class Point3d extends Point{
>	int z;
>	public void move(int dx, int dy, int dz){
>		super.move(dx, dy);
>		this.z += dz;
>	}
>}
>
>package different_package;
>class Point4d extends Point3d{ 
>	int w;
>	public void move(int dx, int dy, int dz, int dw){
>		super.move(dx, dy, dz);
>		this.w += dw;
>	}
>}
>```
>Доступ к полям `x, y, z` из Point4d запрещен, но они в нем есть:
>```
>Point4d [x] - restricted 
>[y] - restricted 
>[z] - restricted 
>[w] - available 
>[move(...)] - available
>```





> [!note]
> Acessibility - static property => determined at *~={green}compile-time=~*
## For methods
#### Top-level classes' methods:

| modifier    | Внутри самого класса ~={orange}Class=~ | В том же пакете ПРИ ВЫЗОВЕ ДРУГИМ КЛАССОМ  ~={orange}Package=~ | У подклассов (same package) ~={orange}Subclasses=~ | У подклассов (different package) ~={orange}Subclass=~ | ~={orange}World=~ |
| ----------- | -------------------------------------- | -------------------------------------------------------------- | -------------------------------------------------- | ----------------------------------------------------- | ----------------- |
| public      | ✅                                      | ✅                                                              | ✅                                                  | ✅                                                     | ✅                 |
| protected   | ✅                                      | ✅                                                              | ✅                                                  | ✅                                                     | ❌                 |
| no modifier | ✅                                      | ✅                                                              | ✅                                                  | ❌                                                     | ❌                 |
| private     | ✅                                      | ❌                                                              | ❌                                                  | ❌                                                     | ❌                 |

~={green}No modifier=~ ещё называют ***~={green}package-private=~*** -> виден только в package, зато во всём.


> [!warning]
> ~={green}***protected***=~ и ~={green}***no modifier***=~ имеют различия по видимости только в ~={red}ОБЪЕКТАХ НАСЛЕДНИКОВ=~


```java
┌───────────────────────────────────────────┐
│                  public                   │
│  Доступен везде, наследуется              │
│  (классы, наследники, пакеты, внешние)    │
└───────────────────────────────────────────┘
                 ▲
┌───────────────────────────────────────────┐
│                protected                  │
│  Доступен:                                │
│   • внутри класса                         │
│   • внутри пакета                         │
│   • y наследников (даже из других пакетов)│
│  Наследуется                              │
└───────────────────────────────────────────┘
                 ▲
┌───────────────────────────────────────────┐
│           package-private ('no modifier') │
│  Доступен:                                │
│   • внутри класса                         │
│   • в том же пакете                       │
│  Не доступен у наследников из других      │
│  пакетов                                  │
│  Наследуется (только в ТОМ ЖЕ пакете)     │
└───────────────────────────────────────────┘
                 ▲
┌───────────────────────────────────────────┐
│                 private                   │
│  Доступен только внутри класса            │
│  Не наследуется (но может быть "затенён") │
└───────────────────────────────────────────┘

```


```java
package p1;

public class Parent {
    private void m1() {}
            void m2() {}
    protected void m3() {}
    public void m4() {}
}

package p1;

class SamePackage {
    void test() {
        Parent p = new Parent();
        // p.m1(); ❌ private
        p.m2();   // ✅ package-private
        p.m3();   // ✅ protected (в том же пакете)
        p.m4();   // ✅ public
    }
}

package p2;

class Child extends Parent {
    void test() {
		// ВЫЗОВ У СЕБЯ. т.е. у объекта наследника, а не самого Parent
        // m1(); ❌ private (недоступен вообще)
        // m2(); ❌ package-private (другой пакет)
        m3();     // ✅ protected (наследник может вызвать)
        m4();     // ✅ public
    }
}

package p2;

class Other {
    void test() {
        Parent p = new Parent();
        // p.m1(); ❌ private
        // p.m2(); ❌ package-private
        // p.m3(); ❌ protected (не наследник, другой пакет)
        p.m4();   // ✅ public
    }
}

```


>[!warning]
>  Если мы наследуем protected поле Parent класса, то ~={red}вызвать=~ его мы можем только ~={red}через указатель на Child класс.=~:
>  ```java
>  package one;
>  
>  class Parent{
> 	 protected m1(){...};
>  }
>  
>  package two;
>  
>class Child extends Parent{
>}
>
>public class Runner{
>public static void main(String[] args){
>	Child ch1 = new Child();
>	ch1.m1(); //можно
>	Parent ch2 = new Child();
>	ch2.m1(); // нельзя
>	}
>}
>  ```

## For classes

#### Top-level classes' methods:

~={green}**public**=~ - виден везде

~={green}**no modifier**=~ - виден только внутри пакета

#### Nested/inner classes:

Модификаторы доступа работают точно также, как и у методов.
>[!warning]
>Но, default конструктор member классов наследует модификатор доступа самого member класса, и вот уже модификатор доступа member класса определяет доступ относительно member класса, а не Enclosing.
>
>Например:
>```java
>package package_one;
>
>class Outer{
>	...
>	protected class Inner{
>		//компилятор сам создает protected Inner();
>		//и создание Inner класса (вызов этого конструктора) будет доступно только
>		//в пакете package_one и наследнике Inner класса другого пакета
>	}
>}
>
>//эти классы могут выщвать конструктор Outer.Inner
>package package_two;
>class SomeClass extends Outer.Inner{
>	class Child extends Outer.Inner{}
>}
>
>package package_two;
>class AnotherClass{
>	public void createInner(){
>		SomeClass inner1 = new SomeClass();
>		Outer.Inner inner2 = new SomeClass();
>		Outer.Inner inner3 = new Child();
>	}
>}
>```





#### ~={yellow}По сути - то же самое=~ но относительно Member классов, а не Enclosing

----
#### [[Access Modifier Flashcards|Link to flashcards]]



---
### References:

- [[Inner Classes Java]]
- [[Static Nested Classes Java]]