
27-10-2025 20:10

Status: #baby 

Tags: [[Hibernate & JPA]]

---
# Transaction - JPA

Инициируется обхектом связи с БД, т.е. или [[Session - Hibernate|Session]] или [[EntityManager - JPA|EntityManager]].


### ЖЦ транзакции

```java
//создание
session.beginTransaction();
entityManager.getTransaction().begin();

//работа с БД
session.save(person); // (через instance of Session или EntityManager)

//Завершение
session.getTransaction().commit();
entityManager.getTransaction().commit();


// отмена транзакции (откат)
session.getTransaction().rollback();
entityManager.getTransaction().rollback();
```




----
#### [[Transaction - JPA - Flashcards|Link to flashcards]]



---
### References:

