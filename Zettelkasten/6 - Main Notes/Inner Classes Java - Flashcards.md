
Theory for the cards: [[Inner Classes Java]]

FILE TAGS: java_core

Q: Может ли Inner класс реализовывать интерфейс?
A: Да, может.

Q: Может ли Inner класс наследовать top-level class?
A: Да, может.

Q: Может ли Inner класс наследовать другой inner class?
A: Да, может.

Q: Может ли Inner класс реализовывать абстрактный класс?
A: Да, может.

Q: Имеет ли Inner класс доступ к private переменным Enclosing класса?
A: Да, имеет.

Q: Можно ли создать объект класса A.Inner в DiffrPackageChild? Почему?
```java
package package_one;  
  
public class A {  
    public int x;  
    protected int y;  
    int z;  
  
    public void print(){  
        System.out.println("x:" + this.x);  
        System.out.println("y:" + this.y);  
        System.out.println("z:" + this.z);  
    }  
  
    protected class Inner{  
        int field;  
        public void print(){  
            System.out.println("durka");  
        }  
    }  
}

package package_two;  
  
import package_one.A;  
  
public class DiffrPackageChild extends A {  
	public void someMethod(){
		DiffrPackageChild.Inner inner = this.new Inner();
	}
}
```
A: Нет, нельзя. Т.к. Inner объявлен с модификатором protected, его default конструктор унаследовал этот модификатор (он теперь `protected Inner()`) и доступен только в пакете `package_one` или в наследниках самого Inner класса.
```
DiffrPackageChild
[x] - available
[y] - available, т.к. он наследует класс A
[z] - restricted, т.к.package-private
[class Inner] - available, но конструктор - restricted
```
На конструктор правила доступа накладываются относительно Inner класса, (а не Enclosing класса).
Пример:
```java
package package_two;  
import package_one.A;  
public class DiffrPackageChild extends A {  
    private class SubInner extends A.Inner{  
    }  // наследовали, чтобы получить возможность вызвать protected Inner()
    
    public void print(){  
        System.out.println("public x = " + x);  
        System.out.println("protected y = " + y);  
        DiffrPackageChild.Inner inner2 = this.new SubInner();  
        //DiffrPackageChild.Inner - виден.
    }  
}
```

Q: Какой модификатор доступа имеет default конструктор Inner класса?
A: Тако же как и сам Inner класс, только он считается относительно Inner класса:
Если Inner класс объявлен как protected, то его конструктор тоже будет protected => создать Inner класс (вызвать его default конструктор) мы сможем только в том же пакете или В НАСЛЕДНИКАХ именно Inner класса.

Q: Создастся ли корректно inner класс при таком вызове в классе SamePackageNonChild того же пакета:
```java
package package_one;  
  
public class A {  
    public int x;  
    protected int y;  
    int z;  
  
    public void print(){  
        System.out.println("x:" + this.x);  
        System.out.println("y:" + this.y);  
        System.out.println("z:" + this.z);  
    }  
  
    class Inner{  
        int field;  
        public void print(){  
            System.out.println("durka");  
        }  
    }  
} 
  
public class SamePackageNonChild {  
	public void someMethod(){
		A.Inner inner = new A().new Inner();
	}
}
```
A: Да, `A.Inner` виден, т.к. у класса Inner модификатор no modifier (package-private), конструктор Inner тоже будет виден, т.к. он наследует package-private modifier класса. 
(Но он считается относительно Inner класса, здесь их область видимости совпала, т.к. оба в одном пакете (а как иначе?:)) и обоих (и у member класса, и у конструктора member класса) package-private modifier)  


Q: Создастся ли корректно inner класс при таком вызове в наследнике A того же пакета:
```java
class InPackageChild extends A{
	void test() {
		InPackageChild.Inner inner = new A.Inner();
	}
}
```
A: Да, такая запись не используется при работе с inner классами, но это будет работать: "под капотом" это преобразуется в `InPackageChildInner inner = this.new Inner();` Inner классы обычно создаются через объект Child или Parent класса:
```java
class SomeClass{
	void test() {
		A a = new A();
		SomeClass.Inner inner = a.new Inner();
	}
}
```


Q:  Создастся ли корректно объект inner класса в методе НЕ дочернего класса в том же пакете, если Inner класс объявлен с модификатором protected?
```java
class NonChildSamePackage{
	void test() {
		A.Inner inner = new A.Inner();
	}
}
```
A: Нет, т.к. inner клласс не может быть создан без Enclosing класса. Здесь компилятор попробует преобразовать в `InPackageChildInner inner = this.new Inner();`, но т.к. это не дочерний класс => получим ошибку.

Q:  Создастся ли корректно объект inner класса в методе НЕ дочернего класса в том же пакете, если Inner класс объявлен с модификатором protected?
```java
class NonChildSamePackage{
	void test() {
		A a = new A();
		A.Inner inner = a.new Inner();
	}
}
```
A: Да, т.к protected позволяет "видеть" метод, поле, inner/nested класс везде SAME PACKAGE + наследниках Outer класса даже в Different Package.

Q: Возникнут ли ошибки при создании экземпляров наследников inner класса?
```java
package package_one;

public class Outer{
	...
	protected class Inner{
	}
}

package package_two;
class SomeClass extends Outer.Inner{
	class Child extends Outer.Inner{}
}

package package_two;
class AnotherClass{
	public void createInner(){
		SomeClass inner1 = new SomeClass();
		Outer.Inner inner2 = new SomeClass();
		Outer.Inner inner3 = new Child();
	}
}
```
A: Да, возникнут, т.к. мы не сможем унаследовать Outer.Inner в классе SomeClass, т.к. он в другом пакете. (Область видимости member класса Inner - только в пакете `package_one` и наследниках `Outer` => мы не видим класс Outer.Inner => не можем его унаследовать)

Q: Возникнут ли ошибки при создании экземпляров наследников inner класса?
```java
package package_one;

public class Outer{
	...
	protected class Inner{
	}
}

package package_two;
class SomeClass extends Outer{
	class Child extends Outer.Inner{}
}

package package_two;
class AnotherClass{
	public void createInner(){
		SomeClass.Inner inner1 = new SomeClass().new Inner();
		SomeClass.Inner inner2 = new Child();
		Outer.Inner inner3 = new Child();
	}
}
```
A: Возникнет ошибка в строке `SomeClass.Inner inner1 = new SomeClass().new Inner();` т.к. в классе SomeClass не доступен конструктор Inner() (т.к. он protected относительно класса Inner, а SomeClass - не подкласс Inner). Однако сам класс Inner он видит и в строчке `SomeClass.Inner inner2 = new SomeClass();` ошибки не возникнет.
>Доступ к полям `x, y, z` из Point4d запрещен, но они в нем есть:
>```
>SomeClass
>[Inner] - available 
>[Child] - available
>
> SomeClass.Inner
> [Inner()] - restricted, т.к. он не наследует Inner, а конструктор вне пакета виден только наследникам
>
>SomeClass.Child
>[Child()] - available (has package-private modifier) 
>```

Q: Какие классы могут унаследовать Inner класс?
A: 1. Можно наследовать внутри других классов наследников Enclosing class, где позволяет видимость
2. Можно наследовать внутри других классов НЕ наследников Enclosing, но придется тоже передавать сущность Enclosing класса в конструктор:
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

Q: Можно ли объявлять константы в inner классе?
A: Да, в inner классе можно объявлять константы (в любой версии Java)

Q: Может ли Inner класс иметь static переменные и static initializer block?
A: Да, может, но только начиная с версии Java 16 SE.