
09-10-2025 21:09

Status: #baby 

Tags: [[Spring]]

---
# Bean factory method - Spring




## Указание фабричного метода в [[Application Context.xml file - Spring|ApplicationContext.xml]]

```xml
<bean id="classicalMusic" class="secret.boy.spring.demo.ClassicalMusic"   
		 init-method="init"  
	     destroy-method="destroy"  
         factory-method="createClassicalMusic"/>
```

> [!note] 
> init-method - не то же самое, что и метод создания - factory-method:
> 1. сначала создаём bean вызывая **factory-method**
> 2. потом инициализируем вызовом **init-method**

----
#### [[Bean factory method - Spring - Flashcards|Link to flashcards]]



---
### References:

- [[Factory method - pattern]]