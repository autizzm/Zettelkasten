
27-10-2025 19:52

Status: #baby 

Tags: [[Hibernate & JPA]]

---
# EntityManager - JPA

implements [[AutoClosable Interface - Java|AutoClosable]]

У JPA **нет своей реализации [[Session - Hibernate|Session]]** — JPA определяет только интерфейс `EntityManager`. Конкретная реализация поставляется провайдером (в твоём случае — Hibernate).

**Объект `EntityManager` фактически ~={cyan}является [[Adapter (Wrapper) - pattern|адаптером]]=~ (обёрткой) над экземпляром `org.hibernate.Session`.**



- Внутри `EntityManagerImpl` (класс из пакета `org.hibernate.jpa.internal`) хранится ссылка на `Session` (обычно `SessionImpl`).


### Создание (получение) EntityManager:

```java
EntityManager entityManager = EntityManagerFactory.createEntityManager();
```

> [!note] 
> EntityManagerFactory - ==thread-safe==

### Методы

Т.к. EntityManager является [[Adapter (Wrapper) - pattern|адаптером]] Session, то он имеет все те же методы, возможно только немного друге сигнатуры

##### Управление [[Transaction - JPA|транзакциями]]:

- beginTransaction()
- getTransaction()

- persist(`Object obj`) - добавит в Context, сделает Entity managed

- merge(`T entity`) - when you have a class instance reppresenting an object which is already in the DB, but we need to add it to the Context

> [!warning] Про merge()
> Если в БД нет объекта, соответствующего тому, который мы мерджим - он сохраняется в БД.
> Если объект в БД имеет тот же id но другие поля -> объект ~={cyan}в БД обновляется=~

- refresh(`Object entity`) - refreshes the state of the given object from the DB. ~={green}Unflushed changes will be lost.=~

> [!warning] Про refresh()
> Противоположно merge():
> обновляет instance of the class данными из БД.


- `T` find(`Class<T> entityClass, Object primaryKey`)

- remove(`Object entity`) - marking entity for removal

- detach(`Object entity`) - удаляет объект из Contxt. ~={green}Unflushed changes will be lost.=~

> [!note] 
> Объект `obj1` НЕ БУДЕТ УДАЛЁН, т.к. мы его пометили для удаления, но после этого отключили помеченный объект от контекста:
> ```java
> entityManager.remove(obj1);
> entityManager.detach(obj1);
> entityMAnager.getTransaction().commit();
> ```

- `T`getReference(`Class<T> entityClass, Object primaaryKey`) - ==lazily== версия find -> делает запрос на получение объекта только тогда, когда он используется в коде. Но в пределах транзакции.




- close()


----
#### [[EntityManager - JPA - Flashcards|Link to flashcards]]



---
### References:

