
01-07-2026 12:59

Status:

Tags: [[Spring Data MongoDB]] [[Spring]]

---
# Criteria + Query - динамическая фильтрация - Spring Data MongoDB

> [!tip] Note
> Criteria + Query работают на уровне `MongoTemplate`: они не предоставляют возможности передать построенный фильтр в репозиторий. Чтоб консистентно работать только с репозиториями можно использовать:
> - QuerydslPredicateExecutor (он сейчас развивется отдельно от спринга)
> - Query by Example - для простых фильтраций с AND, но без OR, диапазонов, regex, [[Querying arrays (JS shell) - Mongo DB#`elemMatch`|elemMatch]]
> - Собственный фрагмент репозитория (Repository Fragment) - самый популярный вариант в курпных проектах


> [!warning]
> **`Criteria + Query` не поддерживает `GROUP BY`**.
>
> Это принципиальное ограничение API.

### Основные классы


#### Criteria

Описывает **условие поиска**. ([[Querying (JS shell) - Mongo DB#Фильтрация|фильтрационный документ]])

```java
Criteria.where("field")
```

Возвращает объект, который затем можно дополнять операторами.

> [!warning]
> In Spring Data MongoDB Criteria, you MUST use the document field names (the actual names stored in MongoDB), not the Java entity property names.


> [!example] Warning
> **В Criteria и в Update рекомендуется использовать имена полей Java-сущности**. Spring Data MongoDB сам преобразует их в имена полей документа с помощью mapping context.



---

## Query

Хранит весь запрос.

Содержит:

- Criteria
    
- сортировку
    
- limit
    
- skip
    
- проекции
    
- hints
    
- collation
    
- и др.


```java
Query query = new Query();
```

или

```java
Query query = new Query(criteria);
```

---

# Простейший запрос

MongoDB

```javascript
{
    "age": 18
}
```

Spring

```java
Query query = Query.query(
        Criteria.where("age").is(18)
);
```

или

```java
Query query = new Query();

query.addCriteria(
        Criteria.where("age").is(18)
);
```

Выполнение

```java
List<UserDocument> users =
        mongoTemplate.find(query, UserDocument.class);
```


---
### Если нужна пагинация ([[PageExecutionUtils - пагинация для Query - Spring Data Access|PageExecutionUtils]])


```java
Query query = new Query(criteria).with(pageable);

List<User> content = mongoTemplate.find(query, User.class);

Query countQuery = Query.of(query)
    .limit(-1)
    .skip(-1);

return PageableExecutionUtils.getPage(
    content,
    pageable,
    () -> mongoTemplate.count(countQuery, User.class)
);
```



---

# Fluent API

Практически все методы возвращают тот же `Criteria`.

```java
Criteria.where("age")
        .gte(18)
        .lt(65);
```

Получится

```javascript
{
    age: {
        $gte: 18,
        $lt: 65
    }
}
```

Это позволяет строить длинные цепочки.

---

# Основные операторы

## Равенство

```java
Criteria.where("name").is("John");
```

Mongo

```javascript
{
    name: "John"
}
```

---

## Не равно

```java
Criteria.where("status")
        .ne("DELETED");
```

↓

```javascript
{
    status: {
        $ne: "DELETED"
    }
}
```

---

## Больше / меньше

```java
Criteria.where("age")
        .gt(18);
```

```java
Criteria.where("age")
        .gte(18);
```

```java
Criteria.where("age")
        .lt(65);
```

```java
Criteria.where("age")
        .lte(65);
```

---

## IN

```java
Criteria.where("country")
        .in("USA", "Germany");
```

↓

```javascript
{
    country: {
        $in: ["USA", "Germany"]
    }
}
```

---

## NOT IN

```java
Criteria.where("country")
        .nin("Russia", "Belarus");
```

---

## EXISTS

```java
Criteria.where("phone")
        .exists(true);
```

Поиск документов, где поле существует.

---

## NULL

Поле равно null

```java
Criteria.where("phone")
        .is(null);
```

Поле отсутствует

```java
Criteria.where("phone")
        .exists(false);
```

Это разные вещи в MongoDB.

---

## Regex

```java
Criteria.where("name")
        .regex("^Joh");
```

или

```java
Criteria.where("name")
        .regex("john", "i");
```

где

```
i
```

означает Ignore Case.

---

# Логические операции

## AND

Самый распространенный вариант.

Можно просто писать подряд

```java
Criteria.where("age")
        .gte(18)
        .and("country")
        .is("USA");
```

Получится

```javascript
{
    age: {$gte:18},
    country:"USA"
}
```

---

Для независимых условий используют

```java
new Criteria().andOperator(
        Criteria.where("age").gte(18),
        Criteria.where("country").is("USA")
);
```

---

## OR

```java
Criteria criteria =
        new Criteria().orOperator(
                Criteria.where("status").is("NEW"),
                Criteria.where("status").is("PENDING")
        );
```

↓

```javascript
{
    $or:[
        {status:"NEW"},
        {status:"PENDING"}
    ]
}
```

---

## NOR

```java
new Criteria().norOperator(...)
```

Редко используется.

---

## NOT

```java
Criteria.where("age")
        .not()
        .gt(18);
```

↓

```javascript
{
    age:{
        $not:{
            $gt:18
        }
    }
}
```

---

# Работа с массивами

## IN

```java
Criteria.where("roles")
        .in("ADMIN");
```

Если массив содержит элемент.

---

## ALL

```java
Criteria.where("roles")
        .all("ADMIN", "USER");
```

Документ должен содержать оба элемента.

---

## Размер массива

```java
Criteria.where("roles")
        .size(2);
```

---

## elemMatch

Для массива объектов.

Документ

```json
{
    "orders": [
        {
            "price":100,
            "status":"NEW"
        }
    ]
}
```

Поиск

```java
Criteria.where("orders")
        .elemMatch(
                Criteria.where("price").gt(50)
                        .and("status").is("NEW")
        );
```

---

# Работа с вложенными документами

Используется dot notation.

```java
Criteria.where("address.city")
        .is("Berlin");
```

MongoDB автоматически понимает путь.

---
### Динамическое построение фильтра

```java
Criteria criteria = new Criteria();

if (filter.status() != null) {
    criteria.and("status").is(filter.status());
}

if (filter.minAmount() != null) {
    criteria.and("paymentAmount").gte(filter.minAmount());
}
```

Однако этот подход имеет недостатки.

Во-первых, он плохо работает со сложными `OR`.

Во-вторых, его легко сломать, если начать комбинировать `andOperator()` и `orOperator()`.


----
# Динамическое построение запроса

Самый распространенный сценарий.

DTO

```java
class UserFilter {

    String name;

    Integer age;

    Boolean active;

}
```

Построение

```java
Query query = new Query();

if (filter.name() != null) {
    query.addCriteria(
            Criteria.where("name").is(filter.name())
    );
}

if (filter.age() != null) {
    query.addCriteria(
            Criteria.where("age").gte(filter.age())
    );
}

if (filter.active() != null) {
    query.addCriteria(
            Criteria.where("active").is(filter.active())
    );
}
```

В результате в запрос попадут только те условия, которые действительно заданы.

Именно поэтому `Criteria + Query` считается аналогом `Specification`.

---

# Сортировка

```java
query.with(
        Sort.by("age")
);
```

По убыванию

```java
query.with(
        Sort.by(
                Sort.Direction.DESC,
                "age"
        )
);
```

Несколько полей

```java
query.with(
    Sort.by("country")
        .and(Sort.by("age"))
);
```

---

# Пагинация

```java
query.with(
        PageRequest.of(0, 20)
);
```

Spring автоматически добавит

```
skip
limit
sort
```

---

# Ограничение количества

```java
query.limit(10);
```

---

# Skip

```java
query.skip(100);
```

---

# Проекция (выбор полей)

Только нужные поля

```java
query.fields()
     .include("name")
     .include("age");
```

Исключить поле

```java
query.fields()
     .exclude("password");
```

Полезно для уменьшения объема передаваемых данных.

---

# Выполнение запросов

Получить список

```java
mongoTemplate.find(
        query,
        UserDocument.class
);
```

Получить один документ

```java
mongoTemplate.findOne(
        query,
        UserDocument.class
);
```

Получить первый

```java
mongoTemplate.find(query, UserDocument.class)
```

или

```java
mongoTemplate.query(UserDocument.class)
             .matching(query)
             .first();
```

Проверить существование

```java
mongoTemplate.exists(
        query,
        UserDocument.class
);
```

Посчитать количество

```java
mongoTemplate.count(
        query,
        UserDocument.class
);
```

---

# Связь с MongoTemplate

Практически все операции `MongoTemplate` принимают `Query`.

```
MongoTemplate
        │
        ▼
      Query
        │
        ▼
    Criteria
        │
        ▼
MongoDB Query Document
```

---

# Преимущества

- Полностью type-safe на уровне Java API (без строковых JSON-запросов).
    
- Fluent API, удобный для построения запросов.
    
- Простая динамическая сборка условий.
    
- Поддерживает почти все операторы MongoDB (`$gt`, `$lt`, `$in`, `$regex`, `$exists`, `$elemMatch`, `$all`, `$size`, `$and`, `$or` и др.). ([Home](https://docs.spring.io/spring-data/mongodb/reference/api/java/org/springframework/data/mongodb/core/query/Criteria.html?utm_source=chatgpt.com "Criteria (Spring Data MongoDB - Core 5.1.0 API)"))
    
- Интегрируется с пагинацией, сортировкой и проекциями.
    
- Используется как в синхронном (`MongoTemplate`), так и в реактивном (`ReactiveMongoTemplate`) API.
    

---

# Ограничения

- Поля документов задаются строками (`"name"`, `"address.city"`), поэтому IDE не проверяет их корректность при переименовании.
    
- Для очень сложных аналитических запросов (например, `group`, `lookup`, `bucket`, вычисляемые поля) следует использовать Aggregation Framework.
    
- При большом количестве фильтров имеет смысл вынести построение `Criteria` в отдельный класс или реализовать собственный аналог `MongoSpecification`, чтобы сохранить переиспользуемость и композицию условий.


----
#### [[Criteria + Query - динамическая фильтрация - Spring Data MongoDB - Flashcards|Link to flashcards]]



---
### References:

