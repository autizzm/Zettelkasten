
03-09-2025 16:40

Status: #child 

Tags: [[Java Core]] [[Java Classes]]

---
# Access Modifiers


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


| modifier    | ~={orange}Enclosing Class=~ | В том же пакете НЕ ПОДКЛАССЫ ~={orange}Package=~ | У подклассов внешнего класса (same package) ~={orange}Subclasses of Enclosing class=~ | У подклассов внешнего класса (different package) ~={orange}Subclasses of Enclosing class=~ | ~={orange}World=~ |
| ----------- | --------------------------- | ------------------------------------------------ | ------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------ | ----------------- |
| public      | ✅                           | ✅                                                | ✅                                                                                     | ✅                                                                                          | ✅                 |
| protected   | ✅                           | ✅                                                | ✅                                                                                     | ✅                                                                                          | ❌                 |
| no modifier | ✅                           | ✅                                                | ✅                                                                                     | ❌                                                                                          | ❌                 |
| private     | ✅                           | ❌                                                | ❌                                                                                     | ❌                                                                                          | ❌                 |

#### ~={yellow}По сути - то же самое=~

> [!warning]
> ~={cyan}Inner=~ классы ~={green}должны создаваться через=~ ~={cyan}объект=~ ~={cyan}Parent или Child=~ класса. Однако такая запись в Child классах тоже возможна:
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

> [!note]
> Через имя класса обычно создаются только nested классы:
> ```java
> class Outer{
> 	//...
> 	protected Inner{
> 		int y;
> 	}
> }
> class SomeClass{
> 	public m1(){
> 		Outer.Inner inner = new Outer.Inner();
> 	}
> }
> ```

----
#### [[Access Modifier Flashcards|Link to flashcards]]



---
### References:

