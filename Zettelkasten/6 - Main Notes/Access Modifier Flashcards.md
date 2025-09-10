
Theory for the cards: [[Access Modifiers]]

FILE TAGS: java_core

Q: Какой модификатор доступа стоит использовать для того, чтобы методы класса были видны в его наследниках и внутри пакета и снаружи?
A: protected
<!--ID: 1757496290025-->


Q: Какой модификатор доступа стоит использовать для того, чтобы методы класса были видны в его наследниках только внутри пакета (и в самом пакете)?
A: no modifier - (package-private)
<!--ID: 1757496290029-->


Q: Будет ли виден inner класс в подклассах outer класса расположенных вне пакета, если inner класс объявлен с модификатором protected?
A: Да, но конструктор Inner класса будет недоступен (если используется default конструктор, который наследует модификатор доступа класса)
<!--ID: 1757496290032-->


Q: Можно ли здесь вызвать метод m1? Почему?
```java
	package one;
	
	class Parent{
		 protected void m1(){...};
	}
	
	package two;
	class Child extends Parent{
	
		public static void main(String[] args){
			Child ch1 = new Child();
			ch1.m1();
			}
	}
```
A: Да, т.к. protected методы наследуются даже дочерними классами другиз пакетов. и ОНИ ВИДНЫ только через УКАЗАТЕЛЬ на ДОЧЕРНИЙ класс. 
<!--ID: 1757496356379-->


Q: Можно ли здесь вызвать метод m1? Почему?
```java
	package one;
	class Parent{
		 protected void m1(){...};
	}
	
	package two;
	class Child extends Parent{
		public static void main(String[] args){
			Parent ch1 = new Child();
			ch1.m1();
			}
	}
```
A: Нет,  protected методы наследуются даже дочерними классами другиз пакетов, но ОНИ ВИДНЫ только через УКАЗАТЕЛЬ на ДОЧЕРНИЙ класс.
<!--ID: 1757496374912-->


Q: Можно ли здесь вызвать метод m1? Почему?
```java
	package one;
	class Parent{
		 protected void m1(){...};
	}
	
	package two;
	class Child extends Parent{
	}
	
	package two;
	public class Runner{
		public static void main(String[] args){
			Сршдв ch1 = new Child();
			ch1.m1();
		}
	}
```
A: Нет,  protected методы наследуются даже дочерними классами другиз пакетов, но ОНИ ВИДНЫ только в этих классах, здесь же вызов метода происходит не в классе-наследнике Parent, а в другом не связанном классе.
<!--ID: 1757496401496-->


Q: Можно ли здесь вызвать метод m1? Почему?
```java
	package one;
	class Parent{
		 void m1(){...};
	}
	
	package two;
	class Child extends Parent{
		public static void main(String[] args){
			Child ch1 = new Child();
			ch1.m1();
		}
	}
```
A: Нет, нельзя, т.к метод m1() объявлен с no modifier => он package-private и НЕ НАСЛЕДУЕТСЯ дочерними классами В ДРУГИХ ПАКЕТАХ
<!--ID: 1757496425781-->


Q: Можно ли здесь вызвать метод m1? Почему?
```java
	package one;
	class Parent{
		 void m1(){...};
	}
	
	package one;
	class Child extends Parent{
		public static void main(String[] args){
			Child ch1 = new Child();
			ch1.m1();
		}
	}
```
A:  Да, можно, т.к. он объявлен с no modifier (package-private) => виден везде внутри пакета
<!--ID: 1757496469910-->


Q: Можно ли здесь вызвать метод m1? Почему?
```java
	package one;
	class Parent{
		 protected void m1(){...};
	}
	
	package one;
	class Child extends Parent{
	}
	
	package one;
	public class Runner{
		public static void main(String[] args){
			Child ch1 = new Child();
			ch1.m1();
		}
	}
```
A:  Да, можно, т.к. он объявлен с модификатором protected => виден везде внутри пакета и в наследниках вне пакета.
<!--ID: 1757496489750-->



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
<!--ID: 1757496290035-->



Q: Можно ли так использовать Point в классе PointList? Не вызовет ли это ошибки?
```java
	package points;
	class Point {
	    public int x, y;
	    public void move(int dx, int dy) { x += dx; y += dy; }
	}
	
	package collections;
	class PointList {
	    Point next, prev;
	}
```
A: Нет, нельзя, т.к. Point объявлен с no modifier (package private), а они НЕ в одном и том же пакете
<!--ID: 1757496504158-->


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
<!--ID: 1757496519962-->


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
		int w;
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
<!--ID: 1757496559863-->



Q:  Создастся ли корректно объект inner класса в методе дочернего класса в том же пакете, если Inner класс объявлен с модификатором no modifier и конструктор не задан (используется default)?
```java
class ChildSamePackage extends A{
	void test() {
		A a = new A();
		ChildDifferentPackage.Inner inner = a.new Inner();
	}
}
```
A: Да, т.к. этот класс находится в том же пакете.
<!--ID: 1757496290039-->


Q:  Создастся ли корректно объект inner класса в методе дочернего класса (дочерний класс Enclosing класса A) в другом пакете, если Inner класс объявлен с модификатором protected и конструктор не задан (используется default)?
```java
package different_package;
class ChildSamePackage extends A{
	void test() {
		A a = new A();
		ChildDifferentPackage.Inner inner = a.new Inner();
	}
}
```
A: класс Inner - виден, т.к. ChildSamePackage наследует protected members у класса A.  А вот конструктор класса Inner в ChildSamePackage не виден, т.к. класс ChildSamePackage не является наследником Inner. => конструктор Inner() виден НЕ будет.
<!--ID: 1757496290043-->



Q: Будет ли виден inner класс в подклассах outer класса расположенных вне пакета, если inner класс объявлен с модификатором no modifier?
A: Нет, т.к это Package-private. он будет виден в подклассах outer класса SAME PACKAGE + еще вне класса. (просто package)
<!--ID: 1757496290046-->


Q: Будет ли виден inner класс в подклассах outer класса расположенных вне пакета, если inner класс объявлен с модификатором protected?
A: Да, Inner класс будет виден, но его конструктор (если используется default конструктор) будет protected относительно Inner класса -> конструктор вызвать будет нельзя.
<!--ID: 1757496609908-->

