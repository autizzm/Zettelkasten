
28-10-2025 21:33

Status: #baby 

Tags: [[Hibernate & JPA]] [[Java+]]

---
# Состояния сущностей в JPA



#### Transient state

Объект пока никак не связан с Hibernate и Hibernate его никак не отслеживает.

#### Managed (Persistent) state

Объект становится Managed тогда, когда подключается к контексту через

```java
entityManager.persist(obj1); //загрузит в Context



Person obj2 = session.find(Person.class, id); // возвратит managed instance
```

> [!note]
> При вызове entityManager.persist(obj1); объект obj1 становится Managed -> не нужно вызывать:
> ```java
> entityManager.persist(obj1);
> obj1 = entityManager.merge(obj1); //не имеет смысла, т.к. объект obj1 станет Managed уже после первого действия
> ```

#### Detached state

Объект перестаёт быть Managed.

```java
entityManager.persist(obj1);

obj1.setName("Jake"); //тут объект ещё подключён к Context, это изменение отобразится в БД

entityManager.detach(obj1); //после этого действия obj1 станет Detached

obj1.setName("Kane"); //это изменение НЕ отобразится в БД
```

~={red}**Все объекты становятся Detached после закрытия сессии**=~


#### Removed state

Объект помечен для удаления и следующий `commit()` удалит соответствующую запись из БД. 

```java
entityManager.getTransaction().begin();

entityManager.remove(obj1); //объект obj1 помечен для удаления из БД

entityManager.getTransaction.commit(); //объект obj1 удалён из БД
```


----
#### [[Состояния сущностей в JPA - Flashcards|Link to flashcards]]



---
### References:

- [[EntityManager - JPA]]
- [[Session - Hibernate]]