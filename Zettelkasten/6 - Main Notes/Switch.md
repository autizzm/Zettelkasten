
01-09-2025 19:16

Status: #child 

Tags: [[Java Core]]

---
# Switch

#### Работает только с:
- примитивными типами
- String
- Enum types
- Некоторые wrapper-классы (Character, Byte, Short, Integer)

> [!warning]
> В switch не могут использоваться wrapper-классы Long, Double, Float


#### Несколько case для одного действия:
```java
switch(month){
	case 1: case 2: case 12:
	System.out.println("winter");
	break;
	
	case 3: case 4: case 5:
	System.out.println("spring")
	break;
}
```

#### Enhanced switch:
```java
public String toString(){
	return switch(this) {
		case SWORD -> "sword";
		case AXE -> "axr";
		default -> "...";
	};
}
```

----
#### [[Switch Flashcards|Link to flashcards]]



---
### References:

