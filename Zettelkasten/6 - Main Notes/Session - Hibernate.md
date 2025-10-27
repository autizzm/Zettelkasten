
27-10-2025 19:47

Status: #baby 

Tags: [[Hibernate & JPA]]

---
# Session - Hibernate

Объект для взаимодействия с Hibernate.

Все основные действия с БД проиводятся именно через Session.

Является реализуемым классом Hibernate (класс, который, согласно стандарту JPA, должен быть реализован ORM фреймворком). 

В JPA - хранится в [[EntityManager - JPA|EntityManager]].

---
### Создание (получение) Session

```java
SessionFactory sessionFactory = configuration.buildSessionFactory();

Session session = sessionFactory.getCurrentSession();
Session session = sessionFactory.openSession(); // управление сессией вручную - он не закрывается апосле коммита транзакции

session.close();//обязательно только при ручном управлении сессией
```

> [!warning]
> Session закрывается после коммита транзакции:
> ```java
> session.getTransaction().commit(); //закроет эту session
> ```

---
### Методы Session

- save
- update
- get
- ...



----
#### [[Session - Hibernate - Flashcards|Link to flashcards]]



---
### References:

