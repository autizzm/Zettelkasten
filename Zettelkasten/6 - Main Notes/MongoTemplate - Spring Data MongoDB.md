
03-07-2026 14:45

Status:

Tags: [[Spring Data MongoDB]] [[Spring Data Access]] [[Spring]]

---
# MongoTemplate - Spring Data MongoDB


### Поиск документов

**Найти все**

```java
find(Query query, Class<T> entityClass)
```

Возвращает `List<T>`.


**Найти один документ**

```java
findOne(Query query, Class<T> entityClass)
```

Возвращает первый найденный документ или `null`.


**Найти по id**

```java
findById(Object id, Class<T> entityClass)
```


---
### Необычные Queries


**Проверить существование**

```java
exists(Query query, Class<T> entityClass)
```

Возвращает `boolean`.


**Подсчитать количество**

```java
count(Query query, Class<T> entityClass)
```


---
## Сохранение

**Вставить новый документ**

```java
insert(entity)
```

или

```java
insert(Collection<T>)
```

**~={orange}Не работает, если документ с таким `_id` уже существует.=~**


**Сохранить**

```java
save(entity)
```

- новый документ → `insert`
    
- существующий → `replace`


---
### Удаление


**Удалить по Query**

```java
remove(Query query, Class<T> entityClass)
```

**Удалить объект**

```java
remove(entity)
```


---

### Обновление


> [!warning]
> **В Criteria и в Update рекомендуется использовать имена полей Java-сущности**. Spring Data MongoDB сам преобразует их в имена полей документа с помощью mapping context.

**`updateFirst()`**

Обновляет **первый** документ.

```java
Update update = new Update()
    .set("status", SUCCESS);

mongoTemplate.updateFirst(
    query,
    update,
    Payment.class
);
```


**`updateMulti()`**

Обновляет **все** подходящие документы.

```java
mongoTemplate.updateMulti(
    query,
    update,
    Payment.class
);
```


**`upsert()`**

Если документ найден → обновить.

Если нет → создать.

```java
mongoTemplate.upsert(
    query,
    update,
    Payment.class
);
```


**`replace()`**

Полностью заменяет документ.


---
### Атомарное обновление с фильтрацией

Используется сочетание

```text
Query
+
Update
```

Например

```java
Query query = Query.query(
    Criteria.where("status").is(NEW)
);

Update update = new Update()
    .set("status", PROCESSING);

mongoTemplate.updateFirst(
    query,
    update,
    Payment.class
);
```

MongoDB выполнит это **атомарно** на уровне одного документа.

**~={orange}findAndModify()=~**

Самый мощный метод.

> **~={red}?!=~** Но работает только с одним документом. Батчем, несколько документов так не обновить.

Позволяет

- найти документ;
    
- атомарно изменить его;
    
- вернуть старую или новую версию.
    

Пример:

```java
Query query = Query.query(
    Criteria.where("status").is(NEW)
);

Update update = new Update()
    .set("status", PROCESSING);

Payment payment = mongoTemplate.findAndModify(
    query,
    update,
    Payment.class
);
```

или

```java
List<Payment> batch = new ArrayList<>();

for (int i = 0; i < 20; i++) {

    Payment payment = mongoTemplate.findAndModify(
        query,
        update,
        FindAndModifyOptions.options().returnNew(true),
        Payment.class
    );

    if (payment == null) {
        break;
    }

    batch.add(payment);
}
```


Это аналог SQL:

```sql
UPDATE payment
SET status = 'PROCESSING'
WHERE status = 'NEW'
RETURNING *
```

(Хотя внутренне реализовано иначе.)


**`findAndReplace()`**

Находит документ и полностью заменяет его.


**`findAndRemove()`**

Находит документ и удаляет его.

---

# Aggregation

Для аналитических запросов.

```java
mongoTemplate.aggregate(...)
```

Поддерживает

- group
    
- lookup
    
- unwind
    
- project
    
- sort
    
- count
    

#### Агрегация с фильтрацией

```java
Aggregation aggregation = Aggregation.newAggregation(
    Aggregation.match(
        Criteria.where("status").is(PaymentStatus.SUCCESS)
    ),
    Aggregation.group("customerId")
            .sum("amount").as("total"),
    Aggregation.sort(Sort.Direction.DESC, "total")
);
```


---

# Bulk Operations

Для массовых операций.

```java
mongoTemplate.bulkOps(...)
```

Позволяет выполнять тысячи операций одним запросом.

---

# Индексы

Получить доступ к индексам

```java
mongoTemplate.indexOps(...)
```

Создать индекс

```java
mongoTemplate.indexOps(User.class)
    .ensureIndex(...);
```

---

# Коллекции

```java
createCollection()

dropCollection()

collectionExists()

getCollection()
```

---

# Исполнение команд MongoDB

Можно выполнить произвольную команду.

```java
mongoTemplate.executeCommand(...)
```

---

# Основные классы

```text
MongoTemplate
      │
      ├── Query
      ├── Criteria
      ├── Update
      ├── Aggregation
      └── BulkOperations
```

---

# Есть ли атомарное обновление по фильтру?

**Да**, и это одна из ключевых возможностей MongoDB.

Наиболее часто используются:

|Метод|Что делает|Атомарность|
|---|---|---|
|`updateFirst()`|Обновляет первый документ, удовлетворяющий фильтру|✅ Да (на уровне одного документа)|
|`updateMulti()`|Обновляет все документы по фильтру|✅ Каждый документ обновляется атомарно, но вся операция не является атомарной как единое целое|
|`findAndModify()`|Найти → изменить → вернуть документ|✅ Да|
|`upsert()`|Обновить или вставить|✅ Да (для одного документа)|

## Практический пример: "захват" задачи (lease)

Именно так обычно реализуют очереди задач и outbox-паттерн без `SKIP LOCKED`:

```java
Query query = Query.query(
    Criteria.where("status").is(Status.NEW)
            .and("lockedUntil").lt(Instant.now())
);

Update update = new Update()
    .set("lockedUntil", Instant.now().plusSeconds(30))
    .set("workerId", workerId);

Task task = mongoTemplate.findAndModify(
    query,
    update,
    FindAndModifyOptions.options().returnNew(true),
    Task.class
);
```

Здесь поиск и обновление выполняются **как одна атомарная операция**. Если два воркера одновременно попытаются захватить один и тот же документ, только один из них сможет успешно обновить его — второй либо получит другой документ, удовлетворяющий фильтру, либо `null`, если подходящих документов больше нет. Это делает `findAndModify()` основным инструментом для реализации конкурентного "захвата" документов в MongoDB.


----
#### [[MongoTemplate - Spring Data MongoDB - Flashcards|Link to flashcards]]



---
### References:

