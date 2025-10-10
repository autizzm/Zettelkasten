
09-10-2025 18:38

Status: #baby 

Tags: [[Spring]]

---
# Application Context.xml file - Spring

храним в resources



---
### Пример ApplicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="
		http://www.springframework.org/schema/beans
		http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context
		http://www.springframework.org/schema/context/spring-context.xsd">
	
	<!-- Автоматическое сканирование компонентов в пакете -->
	<context:component-scan base-package="com.example.demo"/>
	
	<bean id="classicalMusic" class="secret.boy.spring.demo.ClassicalMusic" scope="singleton" />
	<bean id="rockMusic" class="secret.boy.spring.demo.RockMusic"/>
	
	<bean id="musicPlayer" scope="prototype" class="secret.boy.spring.demo.MusicPlayer">  
	    <property name="music">  
	        <list>            
		        <ref bean="classicalMusic"/>  
	            <ref bean="rockMusic"/>  
	            <ref bean="grungeMusic"/>  
	        </list>    
		</property>    
		<property name="name" value="${musicPlayer.name}"/>  
	    <property name="volume" value="${musicPlayer.volume}"/>  
	</bean>
	
</beans>
```

> [!note]
Для инъекции констант удобно использовать [[property file - Spring|property file]]
>
> В ApplicationContext он указывается так:
>```xml
>
>```
>


---

## Способы внедрения зависимостей

#### Через конструктор

```java
public MusicPlayer(Music music) {
	this.music = music;
}
```
	
```xml
<bean id="musicBean" class="secret.boy.spring.demo.ClassicalMusic"/>
<bean id="MusicPlayer" class="secret.boy.spring.demo.MusicPlayer">
	<constructor-arg ref="musicBean">
</bean>
```


#### Через setter

```java
public class MusicPlayer {  
    private Music music;  
    private String name;  
    private int volume;  
	  
    public MusicPlayer(Music music) {  
       this.music = music;  
    }  
	  
    public void setName(String name){  
       this.name = name;  
    }  
	  
    public void setVolume(int volume){  
       this.volume = volume;  
    }  
	  
    public void play() {  
       System.out.println(this.music.getSong());  
    }  
      
}
```
	
```xml
<bean id="musicBean" class="secret.boy.spring.demo.ClassicalMusic"/>
<bean id="musicPlayer" class="secret.boy.spring.demo.MusicPlayer">  
    <constructor-arg ref="classicalMusic"/>  
    <property name="name" value="Some name"/>  
    <property name="volume" value="50"/>  
</bean>
```

> [!warning]
> ```xml
>     <property name="volume" value="50"/>  
> ```
> Тут name сопоставляется с именем сеттера, а не поля:
> ```java
> public void setVolume(int volume){
> 	this.someField = volume
> } 
> ```



## Подключение property файла

```xml
<context:property-placeholder location="classpath:musicPlayer.properties"/>
```

[[Classpath - Java|Что такое classpath]]

Использование значений из property файла для инъекции:
```xml
<property name="name" value="${musicPlayer.name}"/> 
<property name="volume" value="${musicPlayer.volume}"/> 
<!-- в musicPlayer.properties эти переменные так и названы:
 musicPlayer.name=Some name
 musicPlayer.volume=70
 -->
```


# Типы задаваемых значений

- **value**
```xml
    <property name="name" value="Some name"/>  
```

- **ref** - сссылка
```xml
    <property name="music" ref="classicalMusic"/>  
```

- **list**
```java
public void setServers(List<String> servers) {
	this.servers = servers;
}
```

```xml
        <property name="servers">
            <list>
                <value>https://server1.com</value>
                <value>https://server2.com</value>
                <value>https://server3.com</value>
            </list>
        </property>
    </bean>
```
	в list могут быть и value и ref:
	
```java
    public void setSenders(List<EmailSender> senders) {
        this.senders = senders;
    }
```
	
```xml
<bean id="mailService" class="com.example.MailService">
    <property name="senders">
        <list>
            <ref bean="sender1"/>
            <ref bean="sender2"/>
        </list>
    </property>
</bean>
```


- **set** - аналогично list

- **map** 
- **props**


----
#### [[Application Context.xml file - Spring - Flashcards|Link to flashcards]]



---
### References:

- [[property file - Spring]]
- [[yaml file - Spring]]