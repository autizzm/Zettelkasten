
12-08-2026 11:42

Status:

Tags: [[Patterns]] [[System Design]]

---
# Инструменты поллинга Inbox, Outbox - Patterns


### 1. `SELECT ... FOR UPDATE SKIP LOCKED` (наиболее классический)

Идея: БД сама распределяет записи между конкурентными воркерами через row-level locks.

```sql
SELECT *
FROM outbox
WHERE processed = false
ORDER BY created_at
LIMIT 100
FOR UPDATE SKIP LOCKED;
```

Как работает:

- Воркер начинает транзакцию.
    
- Выбирает записи с `FOR UPDATE`.
    
- Уже захваченные другим воркером строки пропускаются (`SKIP LOCKED`).
    
- После обработки выполняется `UPDATE processed = true`.
    
- Транзакция коммитится.

Плюсы:

- Простая реализация.
    
- Нет двойной обработки.
    
- Хорошо масштабируется на несколько воркеров.


Минусы:

- Обработка происходит внутри транзакции (или нужно аккуратно отделять обработку от фиксации статуса).
    
- При долгих транзакциях записи долго удерживаются заблокированными.

Когда использовать: Outbox polling в PostgreSQL — это самый распространённый вариант.

### 2. Lease / Worker Token (временная аренда записи)

Идея: запись не блокируется транзакцией, а помечается как временно принадлежащая конкретному воркеру.

Типичные поля:

```
lease_token
locked_until
```

Захват записей:

```sql
UPDATE outbox
SET lease_token = :workerId,
    locked_until = now() + interval '30 seconds'
WHERE id IN (
    SELECT id
    FROM outbox
    WHERE processed = false
      AND (locked_until IS NULL OR locked_until < now())
    LIMIT 100
)
RETURNING *;
```

> [!warning]
> После `Select` record не блокируется, нужно явно брать `SELECT ... FOR UPDATE`, а поэтому и миновать другие заблокированные строки:
> ```sql
> UPDATE outbox
>SET lease_token = :workerId,
>    locked_until = now() + interval '30 seconds'
>WHERE id IN (
>    SELECT id
>    FROM outbox
>    WHERE processed = false
>      AND (locked_until IS NULL OR locked_until < now())
>    ORDER BY id
>    LIMIT 100
>    FOR UPDATE SKIP LOCKED
>)
>RETURNING *;
> ```


После успешной обработки:

```sql
UPDATE outbox
SET processed = true,
    lease_token = NULL,
    locked_until = NULL
WHERE lease_token = :workerId;
```

Плюсы:

- Нет долгих транзакций.
    
- Воркер может обрабатывать записи долго.
    
- При падении воркера lease истекает, и запись подбирает другой воркер.

Минусы:

- Нужно подбирать TTL.
    
- Нужна логика продления lease (если обработка долгая).
    
- Возможна повторная обработка после истечения lease.

Когда использовать: долгие операции (HTTP, Kafka, внешние сервисы), распределённые воркеры.


### 3. Атомарный UPDATE ... RETURNING (claim-and-fetch) (~={orange}какая-то параша=~)

Идея: одним SQL-запросом пометить записи как «в обработке» и сразу получить их обратно.

Пример:

```sql
WITH cte AS (
    SELECT id
    FROM outbox
    WHERE status = 'NEW'
    ORDER BY created_at
    LIMIT 100
)
UPDATE outbox o
SET status = 'PROCESSING'
FROM cte
WHERE o.id = cte.id
RETURNING o.*;
```

Фактически это атомарный захват партии записей.

Плюсы:

- Нет отдельного SELECT.
    
- Нет race condition между SELECT и UPDATE.
    
- Хорошая производительность.
    

Минусы:

- Нужно хранить статус (`NEW`, `PROCESSING`, `DONE`, `FAILED`).
    
- Если воркер упал в `PROCESSING`, нужна отдельная логика восстановления (timeout / retry).
    

Когда использовать: очень популярно для Inbox и Outbox, особенно вместе с retry-механизмом.

### Сравнение

|Подход|Главная идея|
|---|---|
|FOR UPDATE SKIP LOCKED|Блокировка строк транзакцией|
|Lease / Worker Token|Временная аренда записи|
|UPDATE ... RETURNING|Атомарный захват и получение записей|

### Что обычно используют в реальных проектах

- Outbox → чаще всего `FOR UPDATE SKIP LOCKED` или `UPDATE ... RETURNING`.
    
- Inbox → часто `UPDATE ... RETURNING` со статусами `NEW / PROCESSING / DONE`.
    
- Долгие внешние операции → Lease / Worker Token (именно его ты использовал ранее с `lease_token` и `locked_until`).
    

Если на интервью спросят, какой подход выбрал бы ты, хороший ответ: «Для PostgreSQL и относительно быстрых операций — `FOR UPDATE SKIP LOCKED`; для длительной обработки или распределённых воркеров — lease/token; для максимальной атомарности и минимизации количества запросов — `UPDATE ... RETURNING`».

----
#### [[Инструменты поллинга Inbox, Outbox - Patterns - Flashcards|Link to flashcards]]



---
### References:

