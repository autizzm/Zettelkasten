
30-06-2026 19:27

Status:

Tags: [[Spring Data MongoDB]]

---
# Transactions - Spring Data Mongo DB


MongoDB поддерживает ACID-транзакции:

- **MongoDB 4.0** — транзакции внутри Replica Set.
    
- **MongoDB 4.2** — транзакции между shard'ами.
    

Если MongoDB работает как standalone, транзакции **не поддерживаются**.
Даже если сервер один, очень часто можно увидеть:
```
Replica Set

node1
```

То есть физически одна машина, но MongoDB запущена как Replica Set. Тогда транзакции работают корректно.


---
### Как включить транзакции

Нужен `MongoTransactionManager`.

```java
@Configuration
@EnableTransactionManagement
class MongoConfig {

    @Bean
    MongoTransactionManager transactionManager(
            MongoDatabaseFactory factory) {
        return new MongoTransactionManager(factory);
    }

}
```

> [!note]
> `@EnableTransactionManagement` автоматически включается в SpringBoot
>

После этого можно использовать привычный

```java
@Transactional
public void createOrder(...) {
    ...
}
```


---
# Какой TransactionManager используется?

Используется

```java
MongoTransactionManager
```

Он реализует 

```java
PlatformTransactionManager
```


Поэтому Spring Transaction API работает одинаково.

Под капотом менеджер:

1. открывает ClientSession;
    
2. вызывает
    

```java
session.startTransaction();
```

3. все операции MongoTemplate или Repository используют этот session;
    
4. затем
    

```java
commitTransaction()
```

или

```java
abortTransaction()
```

---

# Что должно использоваться

Все операции должны использовать один и тот же

```java
MongoDatabaseFactory
```

иначе транзакция работать не будет.

---

# Работают ли Repository?

Да.

И

```java
MongoRepository
```

и

```java
MongoTemplate
```

автоматически начинают использовать текущую транзакцию.

Например

```java
@Transactional
public void transfer() {

    userRepository.save(...);

    orderRepository.save(...);

}
```

Обе операции попадут в одну MongoDB transaction.

---

# Reactive Mongo

Для реактивного приложения используется

```java
ReactiveMongoTransactionManager
```

и

```java
@Transactional
```

работает совместно с Reactor Context.


---

# Что происходит без транзакции

MongoDB гарантирует атомарность

**только одного документа**.

Например

```java
update user
```

атомарен.

Но

```text
update user

insert order
```

уже нет.

Если второе действие упало — первое останется.

---

# TransactionOptions

MongoDB поддерживает собственные настройки транзакции:

```java
TransactionOptions.builder()
        .readConcern(...)
        .writeConcern(...)
        .readPreference(...)
```

Spring обычно создает их автоматически.

---

# Работают ли Propagation?

Да.

Spring поддерживает обычные propagation:

```java
REQUIRED
```

```java
REQUIRES_NEW
```

```java
SUPPORTS
```

```java
MANDATORY
```

и т.д.

Но есть нюанс.

MongoDB **не поддерживает nested transactions**.

Поэтому

```java
Propagation.NESTED
```

не имеет смысла.

---

# Работают ли уровни изоляции?

Практически **нет**.

Это один из самых популярных вопросов.

Хотя

```java
@Transactional(isolation = Isolation.SERIALIZABLE)
```

компилируется,

MongoTransactionManager **игнорирует Isolation**.

Например

```java
@Transactional(isolation = Isolation.READ_COMMITTED)
```

никак не изменит поведение MongoDB.

---

Почему?

Потому что MongoDB использует собственую модель согласованности:

- snapshot isolation;
    
- read concern;
    
- write concern.
    

Она не соответствует SQL уровням

- READ_UNCOMMITTED
    
- READ_COMMITTED
    
- REPEATABLE_READ
    
- SERIALIZABLE
    

Spring не может сопоставить их MongoDB.

---

# Что вместо Isolation?

Используются настройки MongoDB:

## ReadConcern

Определяет,

**какие данные можно читать.**

Например

```text
local
majority
snapshot
linearizable
```

---

## WriteConcern

Определяет,

**когда запись считается успешной.**

Например

```text
ACKNOWLEDGED

MAJORITY

UNACKNOWLEDGED
```

---

## ReadPreference

Откуда читать:

```text
primary

secondary

secondaryPreferred

nearest
```

---

# Как работает rollback

Как и обычно.

Если внутри метода произошло RuntimeException

```java
@Transactional
public void foo() {

    repo.save(...);

    throw new RuntimeException();

}
```

Spring вызовет

```java
abortTransaction()
```

и изменений не будет.

Для checked exception нужны привычные

```java
rollbackFor
```

---

# Ограничения внутри транзакции

Во время транзакции нельзя делать некоторые административные операции.

Например:

- создание коллекции;
    
- удаление коллекции;
    
- создание индекса.
    

Эти операции должны выполняться вне транзакции.

---

# Производительность

Транзакции в MongoDB значительно тяжелее обычных операций.

Если изменения затрагивают один документ:

```text
Лучше вообще не использовать транзакцию.
```

MongoDB уже гарантирует атомарность изменения документа.

---

# Отличия от JPA

|Возможность|JPA|MongoDB|
|---|---|---|
|@Transactional|✅|✅|
|PlatformTransactionManager|✅|✅|
|TransactionManager|JpaTransactionManager|MongoTransactionManager|
|Propagation|✅|✅ (кроме NESTED)|
|Isolation|✅|❌ игнорируется|
|Nested transactions|Частично (через savepoints, зависит от СУБД)|❌|
|ACID|✅|✅ (Replica Set/Sharded Cluster)|
|Атомарность одной записи без транзакции|Нет|✅ Один документ|
|Rollback|✅|✅|

# Что важно запомнить для собеседования

1. Для Spring Data MongoDB используется **`MongoTransactionManager`** (или **`ReactiveMongoTransactionManager`** для реактивных приложений).
    
2. Транзакции работают **только на Replica Set или Sharded Cluster**, но не на standalone-сервере.
    
3. Аннотация `@Transactional` работает так же, как и в JPA: Spring открывает транзакцию, привязывает `ClientSession` к текущему потоку (или Reactor Context), а затем выполняет `commitTransaction()` или `abortTransaction()`.
    
4. **Уровни изоляции (`Isolation.READ_COMMITTED`, `REPEATABLE_READ` и т.д.) не поддерживаются и игнорируются** `MongoTransactionManager`.
    
5. Вместо SQL-уровней изоляции MongoDB использует собственые механизмы: **Read Concern**, **Write Concern** и **Read Preference**.
    
6. **`Propagation.NESTED` не поддерживается**, поскольку MongoDB не имеет механизма savepoint'ов.
    
7. Если операция затрагивает **только один документ**, транзакция обычно не нужна — MongoDB уже гарантирует атомарность изменений документа.


----
#### [[Transactions - Spring Data Mongo DB - Flashcards|Link to flashcards]]



---
### References:

