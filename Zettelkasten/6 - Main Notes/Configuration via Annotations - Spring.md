
18-10-2025 15:22

Status: #baby

Tags: [[Spring]]

---
# Configuration via Annotations - Spring


Для использовании аннотаций в ApplicationContext.xml Добавляем единственную строчку:

```xml
<?xml version="1.0" encoding="UTF-8"?>  
<beans xmlns="http://www.springframework.org/schema/beans"  
       xmlns:context="http://www.springframework.org/schema/context"  
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
       xsi:schemaLocation="  
           http://www.springframework.org/schema/beans           http://www.springframework.org/schema/beans/spring-beans.xsd           http://www.springframework.org/schema/context           http://www.springframework.org/schema/context/spring-context.xsd">  
  
    <!-- Автоматическое сканирование компонентов в пакете -->  
    <context:component-scan base-package="secret.boy.spring.demo"/>  
    <!-- Импорт property файла -->
    <context:property-placeholder location="classpath:musicPlayer.properties"/>
      
</beans>
```

---

### @Component

![[Pasted image 20251018152306.png]]


### @Autowired

![[Pasted image 20251018153631.png]]

Аннотация @Autowired подбирает классы по их типу.

Можно использовать на полях, сеттерах и конструкторах.
Можно даже на приватных полях без конструктора и сеттеров. (через Reflection API)

![[Pasted image 20251018160949.png]]

#### **Для уточнения кнкретного bean, который мы хотим заинжектить используем аннотацию @Qualifier:**

![[Pasted image 20251018161100.png]]

При использовании в конструкторе указываем таким образом:

![[Pasted image 20251018161137.png]]


### @Value - внедрение констант

![[Pasted image 20251018165835.png]]


### @Scope

![[Pasted image 20251018170053.png]]


### @PostConstruct - для init методов

### @PreDestroy - для destroy методов


----
#### [[Spring Annotations - Flashcards|Link to flashcards]]



---
### References:

