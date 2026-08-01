
01-07-2026 14:14

Status:

Tags: [[Spring Data MongoDB]] [[Spring Data Access]] [[Spring]]

---
# Aggregation Framework - Spring Data MongoDB


**Aggregation Framework** — механизм MongoDB для обработки и преобразования данных. Он позволяет выполнять операции, аналогичные:

- `GROUP BY`
- `HAVING`
- `JOIN`
- `DISTINCT`
- `COUNT`
- `SUM`
- `AVG`
- `MIN`
- `MAX`
- оконные функции (в современных версиях MongoDB)
- преобразование документов

В Spring Data MongoDB используется класс `Aggregation` совместно с `MongoTemplate`.

---

# Архитектура

Aggregation представляет собой **Pipeline** (конвейер).

Каждая стадия получает результат предыдущей.

```
Collection
      │
      ▼
$match
      │
      ▼
$project
      │
      ▼
$group
      │
      ▼
$sort
      │
      ▼
Result
```

MongoDB выполняет стадии последовательно.


---
# Основные классы

## Aggregation

Описывает весь pipeline.

```java
Aggregation aggregation =
    Aggregation.newAggregation(
        ...
    );
```


---
## AggregationOperation

Каждая стадия pipeline реализует этот интерфейс.

Например:

- `MatchOperation`
    
- `GroupOperation`
    
- `SortOperation`
    
- `ProjectOperation`
    

Обычно напрямую их создавать не требуется — используются фабричные методы `Aggregation`.

---

## AggregationResults

Результат выполнения.

```java
AggregationResults<UserStats> result =
    mongoTemplate.aggregate(
        aggregation,
        User.class,
        UserStats.class
    );
```

Получение данных

```java
List<UserStats> stats =
    result.getMappedResults();
```

---

# Выполнение Aggregation

```java
Aggregation aggregation =
    Aggregation.newAggregation(...);

AggregationResults<ResultDto> result =
    mongoTemplate.aggregate(
        aggregation,
        User.class,
        ResultDto.class
    );
```

Параметры:

- pipeline;
    
- исходная коллекция (или класс документа);
    
- класс результата.


---
# Основные стадии Pipeline

---

## $match

Фильтрация документов.

Использует обычный `Criteria`.

```java
Aggregation.match(
    Criteria.where("active").is(true)
)
```

MongoDB

```javascript
{
    $match: {
        active: true
    }
}
```

Обычно `$match` ставят как можно раньше, чтобы уменьшить объем обрабатываемых данных.

---

## $group

Аналог SQL `GROUP BY`.

SQL

```sql
SELECT country, COUNT(*)
FROM users
GROUP BY country;
```

Spring

```java
Aggregation.group("country")
           .count()
           .as("count");
```

MongoDB

```javascript
{
    $group: {
        _id: "$country",
        count: {
            $sum: 1
        }
    }
}
```



## Группировка для всех документов коллекции

MongoDB:

```js
db.users.aggregate([
    {
        $group: {
            _id: null,
            count: { $sum: 1 }
        }
    }
])
```

Spring Data:

```java
Aggregation aggregation = Aggregation.newAggregation(
    Aggregation.group()
               .count()
               .as("count")
);
```

Обратите внимание:

```java
Aggregation.group()
```

без параметров.


---

# Агрегатные функции

## COUNT

```java
Aggregation.group("country")
           .count()
           .as("count");
```

---

## SUM

```java
Aggregation.group("country")
           .sum("salary")
           .as("totalSalary");
```

---

## AVG

```java
Aggregation.group("country")
           .avg("salary")
           .as("averageSalary");
```

---

## MIN

```java
Aggregation.group("country")
           .min("salary")
           .as("minSalary");
```

---

## MAX

```java
Aggregation.group("country")
           .max("salary")
           .as("maxSalary");
```

---

## FIRST

Первое значение в группе.

```java
.first("name")
.as("firstUser")
```

---

## LAST

Последнее значение.

```java
.last("name")
.as("lastUser")
```

---

## PUSH

Собрать значения в массив.

```java
.push("name")
.as("users")
```

---

## ADD_TO_SET

Собрать уникальные значения.

```java
.addToSet("country")
.as("countries")
```

---

# $project

Изменяет структуру документов.

Например

```java
Aggregation.project(
    "name",
    "age"
);
```

Получится

```javascript
{
    name: ...,
    age: ...
}
```

Можно переименовывать поля.

```java
Aggregation.project()
    .and("fullName")
    .as("name");
```

---

# $sort

```java
Aggregation.sort(
    Sort.by("country")
);
```

или

```java
Aggregation.sort(
    Sort.Direction.DESC,
    "salary"
);
```

---

# $limit

```java
Aggregation.limit(10);
```

---

# $skip

```java
Aggregation.skip(100);
```

---

# $unwind

Разворачивает массив.

Документ

```json
{
    "roles": [
        "ADMIN",
        "USER"
    ]
}
```

После

```java
Aggregation.unwind("roles")
```

получится

```
ADMIN

USER
```

Каждый элемент массива становится отдельным документом.

Используется перед `group`.

---

# $lookup

Аналог SQL JOIN.

Коллекции

```
users

orders
```

```java
Aggregation.lookup(
    "orders",
    "_id",
    "userId",
    "orders"
);
```

Получится

```json
{
    "_id": ...,
    "orders": [
        ...
    ]
}
```

MongoDB не поддерживает JOIN в классическом смысле, но `$lookup` решает большинство подобных задач.

---

# $count

Подсчет документов.

```java
Aggregation.count()
           .as("count");
```

---

# Сложный пример

Посчитать количество активных пользователей по странам.

```java
Aggregation aggregation =
    Aggregation.newAggregation(

        Aggregation.match(
            Criteria.where("active").is(true)
        ),

        Aggregation.group("country")
            .count()
            .as("count"),

        Aggregation.sort(
            Sort.Direction.DESC,
            "count"
        )
    );
```

Pipeline

```
Collection
      │
      ▼
$match
      │
      ▼
$group
      │
      ▼
$sort
```

---

# Mapping результата

Пусть результат выглядит так:

```json
{
    "_id": "Germany",
    "count": 42
}
```

DTO

```java
public class CountryStat {

    private String id;

    private long count;

}
```

или

```java
public record CountryStat(
    String id,
    long count
) {}
```

Spring автоматически выполнит маппинг.

> **Важно:** значение поля `_id` агрегированной группы маппится в Java-поле `id` (или поле, явно сопоставленное через `@Field("_id")`).

---

# AggregationOptions

Позволяет настроить выполнение.

Например

```java
AggregationOptions.builder()
```

Основные настройки:

- `allowDiskUse`
    
- `collation`
    
- `comment`
    
- `hint`
    
- `maxTime`
    

---

# TypedAggregation

Вместо

```java
Aggregation.newAggregation(...)
```

можно использовать

```java
TypedAggregation<User> aggregation =
    Aggregation.newAggregation(
        User.class,
        ...
    );
```

Преимущество:

- известно, с какой коллекцией работает pipeline;
    
- лучше проверяются имена полей при использовании средств Spring Data (хотя Java всё равно не становится полностью type-safe).
    

---

# Ограничения

Aggregation не поддерживает:

- изменение данных (`insert`, `update`, `delete`);
    
- транзакционную бизнес-логику;
    
- ленивую загрузку.
    

Он предназначен исключительно для чтения и преобразования данных.

---

# Производительность

### Всегда ставьте `$match` как можно раньше

Хорошо

```
$match

↓

$group
```

Плохо

```
$group

↓

$match
```

Иначе MongoDB будет группировать всю коллекцию.

---

### Используйте индексы

Стадия `$match` может использовать индексы.

После `$group` большинство индексов уже неприменимы.

---

### Не злоупотребляйте `$lookup`

Для очень больших коллекций он может быть дорогим.

Иногда лучше денормализовать данные.

---

# Когда использовать Aggregation

|Задача|`Query`|`Aggregation`|
|---|:-:|:-:|
|Поиск документов|✅|❌ (избыточно)|
|Фильтрация|✅|✅ (`$match`)|
|Сортировка|✅|✅ (`$sort`)|
|Пагинация|✅|✅ (`$skip`, `$limit`)|
|`GROUP BY`|❌|✅|
|`COUNT` по группам|❌|✅|
|`SUM`, `AVG`, `MIN`, `MAX`|❌|✅|
|`JOIN` (`$lookup`)|❌|✅|
|Преобразование структуры (`$project`)|❌|✅|
|Работа с массивами (`$unwind`)|Ограниченно|✅|

---

# Рекомендации

- Используйте **`Criteria + Query`** для CRUD и динамической фильтрации.
    
- Используйте **Aggregation Framework** для аналитических и отчётных запросов.
    
- Размещайте `$match` в начале pipeline, чтобы максимально использовать индексы и сократить объём обрабатываемых данных.
    
- Предпочитайте проекцию результатов в отдельные DTO/record-классы вместо возврата `Document` — это делает код типобезопаснее и проще для сопровождения.
    
- Если после агрегации требуется постраничная выдача, добавляйте в pipeline стадии `$sort`, `$skip` и `$limit`, а для получения общего количества записей обычно выполняется отдельная агрегация с `$count` или используется стадия `$facet`, если нужно получить данные и общее количество за один проход.


----
#### [[Aggregation Framework - Spring Data MongoDB - Flashcards|Link to flashcards]]



---
### References:

