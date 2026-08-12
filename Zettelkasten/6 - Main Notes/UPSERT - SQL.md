
12-08-2026 11:28

Status:

Tags: [[SQL]] 

---
# UPSERT - SQL


UPSERT в PostgreSQL — это прежде всего конструкция `INSERT ... ON CONFLICT`, которая позволяет атомарно выполнить: «вставить строку, а если она уже существует — обновить её или ничего не делать». Она появилась в PostgreSQL 9.5 и сегодня является стандартным способом реализации idempotent insert/update


### INSERT, если строки нет

```sql
INSERT INTO users (id, name)
VALUES (1, 'Alice')
ON CONFLICT (id)
DO NOTHING;
```

Если `id=1` уже существует — операция просто ничего не сделает.


### INSERT или UPDATE

```sql
INSERT INTO users (id, name)
VALUES (1, 'Alice')
ON CONFLICT (id)
DO UPDATE
SET name = EXCLUDED.name;
```

Если запись существует, то будет выполнен UPDATE.


**Что такое EXCLUDED**

`EXCLUDED` — специальная псевдотаблица, содержащая значения, которые пытались вставить.

Пример:

```sql
INSERT INTO users (id, name, age)
VALUES (1, 'Alice', 30)
ON CONFLICT (id)
DO UPDATE
SET
    name = EXCLUDED.name,
    age  = EXCLUDED.age;
```

Здесь `EXCLUDED.name` = `'Alice'`.

Можно комбинировать существующие и новые значения:

```sql
SET counter = users.counter + EXCLUDED.counter;
```

Это особенно полезно для счётчиков.


---
### По какому конфликту определяется UPSERT

~={orange}Конфликт должен быть связан с UNIQUE или PRIMARY KEY ограничением.=~

Например:

```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    email TEXT UNIQUE,
    name TEXT
);
```

Теперь можно ~={orange}ссылаться на поле ограничения=~:

```sql
INSERT INTO users(email, name)
VALUES ('a@example.com', 'Alice')
ON CONFLICT (email)
DO UPDATE SET name = EXCLUDED.name;
```

Либо ~={orange}можно ссылаться на имя ограничения:=~

```sql
ON CONFLICT ON CONSTRAINT users_email_key
```

Это полезно, если индекс составной или выраженный.


### Составной уникальный ключ

```sql
CREATE TABLE user_roles (
    user_id INT,
    role TEXT,
    PRIMARY KEY(user_id, role)
);

INSERT INTO user_roles(user_id, role)
VALUES (1, 'ADMIN')
ON CONFLICT (user_id, role)
DO NOTHING;
```


---
### Частичное обновление

Не обязательно обновлять все поля.

```sql
INSERT INTO users(id, name, updated_at)
VALUES (1, 'Alice', now())
ON CONFLICT (id)
DO UPDATE
SET
    name = EXCLUDED.name,
    updated_at = now();
```

Часто `updated_at` берут из `now()`, а не из `EXCLUDED`.


### WHERE в DO UPDATE

Очень важная возможность — обновлять только при выполнении условия.

```sql
INSERT INTO users(id, score)
VALUES (1, 100)
ON CONFLICT (id)
DO UPDATE
SET score = EXCLUDED.score
WHERE users.score < EXCLUDED.score;
```

Такой UPSERT обновит запись только если новый score больше старого.

Если условие ложно, UPDATE не выполняется.

Это полезно для:

- хранения максимума,
    
- последних версий,
    
- optimistic update.


### RETURNING

UPSERT отлично работает с `RETURNING`.

```sql
INSERT INTO users(id, name)
VALUES (1, 'Alice')
ON CONFLICT (id)
DO UPDATE
SET name = EXCLUDED.name
RETURNING *;
```

Возвращается итоговая строка после INSERT или UPDATE.

Очень удобно в Spring JDBC, jOOQ, Hibernate Native Query.


### Что происходит под капотом

Предположим:

```sql
INSERT INTO users(id, name)
VALUES (1, 'Alice')
ON CONFLICT (id)
DO UPDATE SET name = EXCLUDED.name;
```

Процесс примерно такой:

1. PostgreSQL пытается вставить строку.
    
2. Проверяет уникальный индекс.
    
3. Если конфликта нет → INSERT завершён.
    
4. Если конфликт есть → блокирует конфликтующую строку.
    
5. Выполняет UPDATE.
    
6. Освобождает блокировку.


Всё происходит атомарно внутри одной команды.

Именно поэтому UPSERT безопаснее, чем:

```sql
SELECT ...
IF NOT EXISTS
    INSERT
ELSE
    UPDATE
```

Такой код подвержен race condition.

### Почему UPSERT атомарный

Представь два потока одновременно вставляют одного пользователя.

Без UPSERT:

```sql
T1: SELECT -> не найдено
T2: SELECT -> не найдено
T1: INSERT
T2: INSERT -> duplicate key error
```

С UPSERT:

```sql
T1: INSERT
T2: INSERT ON CONFLICT -> UPDATE
```

Никакой гонки нет.


---
### Блокировки

Во время конфликта PostgreSQL получает row-level lock на конфликтующую строку.

Особенности:

- читатели (обычный SELECT) не блокируются;
    
- другой UPDATE той же строки будет ждать;
    
- другой UPSERT той же строки тоже будет ждать.
   
При высокой конкуренции на одну и ту же строку возможны очереди ожидания.


### Производительность

UPSERT обычно очень быстрый, но важно понимать:

Если конфликта нет:

- стоимость почти как обычный INSERT.

Если конфликт есть:

- INSERT
    
- проверка индекса
    
- получение блокировки
    
- UPDATE
    
- создание новой версии строки (MVCC)

То есть конфликтный UPSERT дороже обычного UPDATE.


### Массовый UPSERT

Можно вставлять сразу множество строк.

```sql
INSERT INTO users(id, name)
VALUES
    (1, 'Alice'),
    (2, 'Bob'),
    (3, 'Carol')
ON CONFLICT (id)
DO UPDATE
SET name = EXCLUDED.name;
```

Это гораздо эффективнее, чем выполнять тысячи отдельных UPSERT.


### Частичные уникальные индексы

Очень интересный нюанс.

```sql
CREATE UNIQUE INDEX active_email_idx
ON users(email)
WHERE deleted = false;
```

Теперь UPSERT можно привязать именно к этому индексу.

```sql
ON CONFLICT (email)
WHERE deleted = false
DO UPDATE ...
```

Это позволяет иметь несколько записей с одинаковым email, если старые помечены как удалённые.

### Отличие от MERGE

Начиная с PostgreSQL 15 появился `MERGE`.

Пример:

```sql
MERGE INTO users u
USING source s
ON u.id = s.id
WHEN MATCHED THEN
    UPDATE SET name = s.name
WHEN NOT MATCHED THEN
    INSERT (id, name) VALUES (s.id, s.name);
```

Разница:

|ON CONFLICT|MERGE|
|---|---|
|Только INSERT → UPDATE/NOTHING|Более общий оператор|
|Работает через уникальный индекс|Работает через произвольное условие|
|Обычно быстрее|Более гибкий|
|Предпочтителен для классического UPSERT|Удобен для синхронизации таблиц|

Для большинства backend-задач (`idempotent API`, `cache`, `counters`, `materialized aggregates`, `outbox state`) `INSERT ... ON CONFLICT` остаётся предпочтительным вариантом.

### Практический пример: агрегирование метрик

```sql
INSERT INTO client_metrics(client_id, total_amount, events_count)
VALUES (42, 100, 1)
ON CONFLICT (client_id)
DO UPDATE
SET
    total_amount = client_metrics.total_amount + EXCLUDED.total_amount,
    events_count = client_metrics.events_count + EXCLUDED.events_count;
```

Каждое событие либо создаёт новую строку клиента, либо атомарно увеличивает агрегаты, без предварительного SELECT и без race condition между несколькими Kafka consumers.

Именно поэтому UPSERT в PostgreSQL часто является базовым строительным блоком для Kafka aggregators, materialized views, counters, leaderboard, статистики и idempotent event processing.


----
#### [[UPSERT - SQL - Flashcards|Link to flashcards]]



---
### References:

