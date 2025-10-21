
20-10-2025 16:32
 
Status: #baby 

Tags: [[Java Core]]

---
# Enum - Java

В Java это класс.

У него могут быть методы, поля и конструкторы.

#### Стандартные методы:


**static:**
- `.values()` - возвращает массив всех значений этого Enum

- `.valueOf()` - создание объекта из текста:
```java
String s = "active";
Status status = Status.valueOf(s.toUpperCase());
```

**instance methods:**
- `.name()` - возвращает String ровно в том виде, в котором задана константа:

```java
Planet p = Planet.EARTH;

String name = p.name() // returns "EARTH"
```



### Конструктор и поля в enum

```java
enum Planet{
MERCURY(3.3e23, 2.439e6),
VENUS(4.89e24, 6.0518e6),
EARTH(5.976e24, 6.37814e6)

private final double mass; //fields
private final double radius;
public static final G = 6.67e-11;  //константа

Planet(double mass, double radius){ //constructor
	this.mass = mass;
	this.radius = radius;
}
}
```

> [!note]
> При инициализации Enum инициализируются сразу все объекты этого Enum:
> ```java
> enum Status {
>  ACTIVE,
>  INACTIVE;
>  
>	Status() { 
>	System.out.print([this.name](this.name() + " "); 
>	} 
>} 
>	
>public class Task170925 { 
>	public static void main(String[] args) { 
>		System.out.print("Start "); 
>		Status status = Status.ACTIVE; 
>		System.out.print("End"); 
>	} 
>}
> ``` 
> Тут будет выведено в консоль: `Start ACTIVE INACTIVE end`


----
#### [[Enum - Java - Flashcards|Link to flashcards]]



---
### References:

