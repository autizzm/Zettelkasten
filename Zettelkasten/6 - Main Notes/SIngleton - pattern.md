
04-10-2025 10:05

Status: #baby 

Tags: [[Patterns]]

---
# SIngleton - pattern

Класс, сущность которого может существовать в единственном числе.


# Способы реализации:

### Eager initialization

```java
class DBConnection {
	//инициализируется сразу при загрузке класса
	private static DBConnection connectObj = new DBConnection();
	
	public static DBConnection getInstance(){
		return connectObj;
	}
}
```

### Lazy initialization

```java
class DBConnection {
	private static DBConnection connectObj;
	
	public static DBConnection getInstance(){
	//инициализируется при первом вызове getInstance() метода
		if(connectObj == null){
			connectObj = new DBConnection();
		}
		return connectObj;
	}
}
```

> [!warning] **Threading problem !**
> Если одновременно два потока вызовут `getInstance()` - будет две сущности `DBConnection`
> 
> **Solution:**
> _Synchronization_

### Synchronized method

То же самое, что и Lazy initialization, только лепим `synchronized`:

```java
class DBConnection {
	private static DBConnection connectObj;
	
	synchronized public static DBConnection getInstance(){
	//инициализируется при первом вызове getInstance() метода
		if(connectObj == null){
			connectObj = new DBConnection();
		}
		return connectObj;
	}
}
```

> [!warning] **Very expensive**
> to lock the object every time
> 
> Нам нужно лочить его только для первого вызова - во время создания объекта

### Double locking

~={green}industry standard=~

```java
class DBConnection {
	private static DBConnection connectObj;
	
	public static DBConnection getInstance(){
	//инициализируется при первом вызове getInstance() метода
		if(connectObj == null){
			synchronized(DBConnection.class){
			//проверяем второй раз, т.к. класс, который пытался создать объект, но был вынужден ждать (т.к. другой класс уже начал процесс создания DBConnection, т.е. вошел в syncronized блок) тоже зайдёт в syncronized блок после этого.  И если connectObj уже не равен null -> он не будет создавать новый
				if(connectObj == null){
					connectObj = new DBConnection();
				}
			}
		}
		return connectObj;
	}
}
```



----
#### [[SIngleton - pattern - Flashcards|Link to flashcards]]



---
### References:

