
09-10-2025 20:16

Status: #baby 

Tags: [[Spring]]

---
# Bean scope

Можно явно указать в [[Application Context.xml file - Spring|ApplicationContext.xml]].

```xml
	<bean id="musicPlayer" scope="prototype" class="secret.boy.spring.demo.MusicPlayer">  
    <property name="music">  
        <list>            
	        <ref bean="classicalMusic"/>  
            <ref bean="rockMusic"/>  
            <ref bean="grungeMusic"/>  
        </list>    
	</property>
</bean>
```

## Singleton scope

~={orange}Используется по умолчанию=~

Создаётся единственный объект класса. 
При каждом вызове getBean() возвращается ссылка на один и тот же объект

![[Pasted image 20251009201910.png]]

(Про "стлкнёмся с проблемой" - очевидная хуйня)


## Prototype scope

![[Pasted image 20251009202330.png]]


## Request scope

~={pink}(no info yet)=~
## Session scope

~={pink}(no info yet)=~

## Global-session scope

~={pink}(no info yet)=~

----
#### [[Bean scope - Flashcards|Link to flashcards]]



---
### References:

