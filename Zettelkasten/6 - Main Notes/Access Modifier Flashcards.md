
Theory for the cards: [[Access Modifiers]]

FILE TAGS: java_core

Q: Какой модификатор доступа стоит использовать для того, чтобы методы класса были видны в его наследниках и внутри пакета и снаружи?
A: protected

Q: Какой модификатор доступа стоит использовать для того, чтобы методы класса были видны в его наследниках только внутри пакета?
A: no modifier - (package-private)

Q: Будет ли виден inner класс в подклассах outer класса расположенных вне пакета, если inner класс объявлен с модификатором protected?
A: Да

Q: Можно ли здесь вызвать метод m1? Почему?
```java
package one;

class Parent{
	 protected m1(){...};
}

package two;

class Child extends Parent{
}

public class Runner{
	public static void main(String[] args){
		Child ch1 = new Child();
		ch1.m1();
		}
}
```
A: Да, т.к. protected методы наследуются даже дочерними классами другиз пакетов. и ОНИ ВИДНЫ только через УКАЗАТЕЛЬ на ДОЧЕРНИЙ класс. 

Q: Можно ли здесь вызвать метод m1? Почему?
```java
package one;

class Parent{
	 protected m1(){...};
}

package two;

class Child extends Parent{
}

public class Runner{
	public static void main(String[] args){
		Parent ch1 = new Child();
		ch1.m1();
		}
}
```
A: Нет,  protected методы наследуются даже дочерними классами другиз пакетов, но ОНИ ВИДНЫ только через УКАЗАТЕЛЬ на ДОЧЕРНИЙ класс.

Q: Можно ли здесь вызвать метод m1? Почему?
```java
package one;

class Parent{
	 m1(){...};
}

package two;

class Child extends Parent{
}

public class Runner{
	public static void main(String[] args){
		Child ch1 = new Child();
		ch1.m1();
		}
}
```
A: Нет, нельзя, т.к метод m1() объявлен с no modifier => он package-private и НЕ НАСЛЕДУЕТСЯ дочерними классами В ДРУГИХ ПАКЕТАХ

Q: Можно ли здесь вызвать метод m1? Почему?
```java
package one;

class Parent{
	 m1(){...};
}

class Child extends Parent{
}

public class Runner{
	public static void main(String[] args){
		Child ch1 = new Child();
		ch1.m1();
	}
}
```
A:  Да, можно, т.к. он объявлен с no modifier (package-private) => виден везде внутри пакета

Q: Можно ли здесь вызвать метод m1? Почему?
```java
package one;

class Parent{
	 protected m1(){...};
}

class Child extends Parent{
}

public class Runner{
	public static void main(String[] args){
		Child ch1 = new Child();
		ch1.m1();
	}
}
```
A:  Да, можно, т.к. он объявлен с модификатором protected => виден везде внутри пакета и в наследниках вне пакета.

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
		SomeClass.Inner inner = a.new.Inner();
	}
}
```


Q:  Создастся ли корректно объект inner класса в методе НЕ дочернего класса в том же пакете, если Inner класс объявлен с модификатором protected?
```java
class NonChildSamePackage{
	void test() {
		A a = new A();
		A.Inner inner = a.new.Inner();
	}
}
```
A: Да, т.к protected позволяет "видеть" метод, поле, inner/nested класс везде SAME PACKAGE + наследниках Outer класса даже в Different Package.

Q:  Создастся ли корректно объект inner класса в методе дочернего класса в том же пакете, если Inner класс объявлен с модификатором no modifier?
```java
class ChildDifferentPackage{
	void test() {
		A a = new A();
		ChildDifferentPackage.Inner inner = a.new.Inner();
	}
}
```
A: Нет, т.к. no modifier не позволяет наследовать


Q: Будет ли виден inner класс в подклассах outer класса расположенных вне пакета, если inner класс объявлен с модификатором no modifier?
A: Нет, т.к это Package-private. он будет виден в подклассах outer класса SAME PACKAGE + еще вне класса. (просто package)

