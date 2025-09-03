
Theory for the cards: [[Switch]]

FILE TAGS: java_core

Q: Какие типы не могут использоваться в switch?
A: wrapper-классы  Long, Double, Float
<!--ID: 1756744259773-->


Q: Может ли Enum type использоваться в switch?
A: Да
<!--ID: 1756744259785-->


Q: Как в switch выполнять одно и то же действие для разных значений проверяемой переменной?
A: Проваливание в case:
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
<!--ID: 1756744259794-->


Q: Как вернуть значение из switch?
A: Enhanced switch:
```java
public String toString(){
	return switch(this) {
		case SWORD -> "sword";
		case AXE -> "axr";
		default -> "...";
	};
}
```
<!--ID: 1756744259803-->

