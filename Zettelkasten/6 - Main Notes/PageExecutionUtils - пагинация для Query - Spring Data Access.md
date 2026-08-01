

01-07-2026 13:59

Status:

Tags: [[Spring Data Access]]

---
# PageExecutionUtils - пагинация для Query - Spring Data Access


`PageableExecutionUtils` — утилитный класс Spring Data, предназначенный для создания объекта `Page<T>` с возможностью **отложенного выполнения запроса `count()`**. Он позволяет избежать лишнего подсчета общего количества документов, если его можно определить по результатам текущей страницы.

---

## Когда использовать

Используется, когда данные получаются не через репозиторий, а вручную, например:

- `MongoTemplate`
    
- `JdbcTemplate`
    
- `EntityManager`
    
- QueryDSL
    
- jOOQ


То есть везде, где необходимо самостоятельно сформировать `Page<T>`.

---

## Основной метод

```java
PageableExecutionUtils.getPage(
    List<T> content,
    Pageable pageable,
    LongSupplier totalSupplier
)
```

Параметры:

- `content` — элементы текущей страницы;
    
- `pageable` — информация о странице;
    
- `totalSupplier` — функция, возвращающая общее количество элементов (обычно вызывает `count()`).


---

## Пример (для [[Criteria + Query - динамическая фильтрация - Spring Data MongoDB|MongoDB Query]])

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

## Оптимизация

Главная особенность — `count()` вызывается **не всегда**.

Например:

- первая страница содержит меньше элементов, чем `pageSize` → это единственная страница, `count()` не нужен;
    
- последняя страница определяется по количеству полученных элементов → `count()` также может не выполняться.
    

Если же по содержимому страницы невозможно определить общее количество элементов, `PageableExecutionUtils` вызовет `totalSupplier`.

---

## Преимущества

- Не нужно вручную создавать `PageImpl`.
    
- Может сократить число обращений к базе данных.
    
- Работает с любым источником данных, а не только с MongoDB.
    
- Стандартный способ формирования `Page` в кастомных реализациях репозиториев Spring Data.
    

---

## Ограничения

- Работает только если вы уже получили содержимое страницы (`List<T>`).
    
- Если для определения общего количества элементов необходим подсчет, `count()` всё равно будет выполнен.
    
- Не заменяет пагинацию — `skip`, `limit` и `sort` необходимо применять к запросу самостоятельно.
    

---

## Когда выбрать `PageImpl`

Если:

- оптимизация `count()` не важна;
    
- общее количество уже известно;
    
- нужен максимально простой код,
    

то можно создать страницу напрямую:

```java
return new PageImpl<>(content, pageable, total);
```

---

## Рекомендация

Для кастомных методов репозитория (`MongoTemplate`, `JdbcTemplate`, `EntityManager` и др.) предпочтительно использовать `PageableExecutionUtils.getPage(...)`, так как это стандартный подход Spring Data, позволяющий избежать лишнего запроса `count()` там, где он не требуется.


----
#### [[PageExecutionUtils - пагинация для Query - Spring Data Access - Flashcards|Link to flashcards]]



---
### References:

