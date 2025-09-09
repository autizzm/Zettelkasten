
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


Q: Можно ли так использовать Point в классе PointList? Не вызовет ли это ошибки?
```java
package points;
class Point {
    public int x, y;
    public void move(int dx, int dy) { x += dx; y += dy; }
}
class PointList {
    Point next, prev;
}
```
A: Да, т.к. Point объявлен с no modifier (package private), а они в одном и том же пакете


Q: Можно ли так использовать Point в классе PointList? Не вызовет ли это ошибки?
```java
package points;
class Point {
    public int x, y;
    public void move(int dx, int dy) { x += dx; y += dy; }
}

```java
package collections;
class PointList {
    Point next, prev;
}
```
A: Нет, нельзя, т.к. Point объявлен с no modifier (package private), а они НЕ в одном и том же пакете

Q: Есть ли в классе Point4d поле x?
```java
package points;
class Point{
	int x;
	int y;
	public void move(int dx, int dy){
		this.x += dx;
		this.y += dy;
	}
}

package points;
class Point3d extends Point{
	int z;
	public void move(int dx, int dy, int dz){
		super.move(dx, dy);
		this.z += dz;
	}
}

package different_package;
class Point4d extends Point3d{ 
	int w;
}
```
A: Да, есть, но доступ к нему запрещен, т.к. оно объявлено  с модификатором no modifier (package-private).
>Доступ к полям `x, y, z` из Point4d запрещен, но они в нем есть:
>```
>Point4d [x] - restricted 
>[y] - restricted 
>[z] - restricted 
>[w] - available 
>[move(...)] - available
>```

Q: Как получить доступ к полю x в классе Point4d?
```java
package points;
class Point{
	int x;
	int y;
	public void move(int dx, int dy){
		this.x += dx;
		this.y += dy;
	}
}

package points;
class Point3d extends Point{
	int z;
	public void move(int dx, int dy, int dz){
		super.move(dx, dy);
		this.z += dz;
	}
}

package different_package;
class Point4d extends Point3d{ 
	int w;
}
```
A: Используя метод родительского класса (вызвать его через указатель на суперкласс - super):
```java
package points;
class Point{
	int x;
	int y;
	public void move(int dx, int dy){
		this.x += dx;
		this.y += dy;
	}
}

package points;
class Point3d extends Point{
	int z;
	public void move(int dx, int dy, int dz){
		super.move(dx, dy);
		this.z += dz;
	}
}

package different_package;
class Point4d extends Point3d{ 
	int w;```java
	public void move(int dx, int dy, int dz, int dw){
		super.move(dx, dy, dz);
		this.w += dw;
	}
}
```
>Доступ к полям `x, y, z` из Point4d запрещен, но они в нем есть:
>```
>Point4d [x] - restricted 
>[y] - restricted 
>[z] - restricted 
>[w] - available 
>[move(...)] - available
>```

###not finished:###


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

