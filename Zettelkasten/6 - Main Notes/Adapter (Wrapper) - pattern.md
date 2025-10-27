
27-10-2025 20:03

Status: #baby 

Tags: [[Patterns]]

---
# Adapter (Wrapper) - pattern

Facilitates communication between incompatible interfaces.

```java 
public class AdappterLogger implements LoggerStandard{
	private final ThirdPartyLogger thirdPartyLogger;
	
	//конструктор
	
	@Override
	public void logInfo(String message){
		thirdPartyLogger.info(message);
	}
}
```




### Common use cases:

1. Third-party APIs integration
2. Database abstraction (JDBC)
3. Legacy system integration

Из реальных примеров:

Класс [[EntityManager - JPA|EntityManager]] (из JPA) является адаптером Session (из Hibernate).

----
#### [[Adapter (Wrapper) - pattern - Flashcards|Link to flashcards]]



---
### References:

