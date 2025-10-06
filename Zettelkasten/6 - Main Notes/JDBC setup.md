
06-10-2025 12:01

Status: #baby

Tags: [[Java+]]

---
# JDBC setup

### Dependency of the driver

1. Подрубаем драйвер в Maven:
```xml
<dependency>
    <groupId>org.postgresql</groupId>
    <artifactId>postgresql</artifactId>
    <version>42.7.8</version>
</dependency>
```

2. Загружаем класс драйвера:

```java
Class.forName("org.postgresql.postgresql.Driver")
```

3. Устанавливаем связь с БД:
```java
Connection connection = DriverManager.getConnection("jdbc:postgresql://127.0.0.1:5432/identity"); //your DB url

//Если нужно указывать пароль и username:
Connection connection = DriverManagergetConnection(url, user, password)
```

> [!warning] **Connection - НЕ singleton**
>Каждый вызов 
>```java
> Connection conn = DriverManager.getConnection(url, user, password);
>```
создаёт **новое физическое соединение** с базой данных (или логическое, если используется пул соединений).  
То есть можно получить **несколько независимых соединений** одновременно.


~={red}!!!=~ Connection - НЕ ПОТОКОБЕЗОПАСЕН ~={red}!!!=~


> [!note] **Протоколы связи с БД**
>```java
>"jdbc:postgresql://127.0.0.1:5432/identity"
> <protocol>:<subprotocol>:<DB_url>
>```


4. выполняем запросы

5. ~={orange}ЗАКРЫВАЕМ=~ Connection:
```java
connection.close();
```

----
#### [[JDBC setup - Flashcards|Link to flashcards]]



---
### References:

- [[Запросы к БД - JDBC]]
