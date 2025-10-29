
28-10-2025 22:01

Status: #baby 

Tags: [[Hibernate & JPA]] [[Java+]]

---
# HQL - Hibernate

Используется для специфических запросов к БД.


HQL, в отличии от SQL работает с Java сущностями (классами и полями), а не сущностями БД (Таблицами и колонкми).   

~={red}**Конвертируется в SQL**=~

Нужен для того, чтобы не спускаться до SQL при высокоуровневой работе с БД. 

> [!note] 
> Hibernate позволяет писть запросы и на чистом SQL, но это не рекомендуется.
> 
>Можем использовать pure SQL только если HQL - неудобно. (типа каких-то аггрегатных функций)

---

![[Pasted image 20251028222112.png]]

----
### Использование HQL:

Через методы, создающие Query и TypedQuery:

```java
Query q = entityManager.createQuery("FROM Person");
// или
Query q2 = session.createQuery("FROM Person");
```


### Пример:

```java
session.getTransaction().begin();  
session.createQuery("UPDATE Person SET age=18 WHERE name LIKE 'name-%'").executeUpdate();  
List<Person> people = session.createQuery("FROM Person WHERE name LIKE 'name-%'").getResultList();  
session.createQuery("DELETE FROM Person WHERE age<18").executeUpdate();
for(Person pers : people){  
    System.out.println(pers);  
}  
session.getTransaction().commit();
```


----
#### [[HQL - Hibernate - Flashcards|Link to flashcards]]



---
### References:

