
Theory for the cards: [[Enum - Java]]

FILE TAGS: java_core

Q: Что тут выведется в консоль?
```java
enum Status {
	 ACTIVE,
	 INACTIVE;
	 
	Status() { 
	System.out.print([this.name](this.name/)() + " "); 
	} 
} 
	
public class Task170925 { 
	public static void main(String[] args) { 
		System.out.print("Start "); 
		Status status = Status.ACTIVE; 
		System.out.print("End"); 
	} 
}
```
A: Тут будет выведено в консоль: `Start ACTIVE INACTIVE end`. Потому что при инициализации Enum инициализируются сразу все объекты этого Enum.
<!--ID: 1760978176920-->
