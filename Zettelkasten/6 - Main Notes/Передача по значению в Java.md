
03-09-2025 14:08

Status:

Tags:

---
# Передача по значению в Java

```java
class Circle{
	public int x;
	public int y;
	
	public Circle(int x, int y){
		this.x = x;
		this.y = y;
	}
	
	public void move(Circle circle){
		circle.x++; // изменит значение x у myCircle
		circle = new Circle(0,0); //указатель circle теперь указывает на созданный объект
	}
	
	
	public static void main(String[] args){
		Circle myCircle = new Circle(5, 5);
		move(myCircle);
		System.out.println(myCircle.x); // выведет 6
	}
}
```
> [!warning]
> В функцию передается копия указателя ~={purple}(адрес объекта Circle но в другой ячейке памяти)=~ и, если создать новый объект, то изменится лишь значение в новой ячейке памяти, а адрес хранимый оригинальным указателем -  не изменится.

