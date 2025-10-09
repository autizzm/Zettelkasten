
09-10-2025 20:33

Status: #baby 

Tags: [[Spring]]

---
# Bean Lifecycle - Spring

![[Pasted image 20251009203409.png]]


> [!note] 
> Каждый Bean может иметь user-defined init-метод и destroy-метод

## Указание методов в [[Application Context.xml file - Spring|ApplicationContext.xml]]

```xml
<bean id="musicBean" 
	class="secret.boy.spring.demo.Music"
	init-method="doMyInit"
	destroy-method="doMyDestroy"/>
```

Регистрирует методы:
```java
... doMyInit(){...}
... doMyDestroy(){...}
```


> [!warning]
> Spring вызывает destroy метод толькоу Singleton бинов

---

![[Pasted image 20251009204626.png]]

----
#### [[Bean Lifecycle - Spring - Flashcards|Link to flashcards]]



---
### References:

