
Index: [[PostgreSQL DB]]

FILE TAGS: databases postgres





Q: Расскажи о плюсах и минусах PostgreSQL.
A:   
**Плюсы:**
- Поддержка ACID
- Обширный список типов данных
- Open Source
- Расширение -> Постоянное развитие через добавление новых расширений в состав ядра Postgres (кто-то пилит расширение, успешно юзает -> расширение набирает популярность -> команда postgres видит спрос  -> добавляет в состав ядра)
- Большое сообщество
	
---
**Минусы:**
- Создание кластера - необходимость создания кластеров вручную. Нет default функции создания классов из колробки.
- Ограниченность коннектов - Postgres не любит когда к нему подключается миллион пользователей.
- Default настройки - хуйня -> надо жоска настраювати.
- Частые UPDATE/DELETE - медленно.
<!--ID: 1770567039101-->


Q: Чем отличается Ребилд vs reorganize index в Postgres?
A: Rebuild — создать индекс заново. Reorganize — реорганизовать существующую структуру без полного пересоздания. В PostgreSQL отдельного `REORGANIZE INDEX` нет; для rebuild используется `REINDEX`.
	
**Синтаксис:**
	
```sql
REINDEX INDEX index_name;
```
	
```sql
REINDEX INDEX CONCURRENTLY index_name;
```
<!--ID: 1788359637606-->


Q: Расскажи про то, как B-Tree работает с диапазонами значений?
A: B-tree эффективен для range queries благодаря отсортированным ключам и связному leaf level: после поиска начала диапазона можно последовательно сканировать соседние leaf pages, не возвращаясь к root.
<!--ID: 1788359637609-->


Но точнее сказать так:
```
            Internal nodes
           ┌───────────────┐
           │  10 | 20 | 30 │
           └───────────────┘
           /     |    |    \
           ↓     ↓    ↓     ↓
	
Leaf level:
	
[1 5 8] → [10 12 15] → [20 22 25] → [30 35 40]
             ↑
          нашли 10
             ↓
       читаем вправо → → →
```
	
Для:
	
```sql
WHERE x BETWEEN 10 AND 30
```
	
PostgreSQL:
	
1. спускается по дереву к первой leaf page, где находится `10`;
2. затем **идёт по leaf pages последовательно через sibling links**;
3. останавливается после `30`.

Q: Что такое статистика при выполнении запроса к БД (почему статистика может забуксовать?)
A: Статистика PostgreSQL — это информация о распределении данных, которую planner использует для оценки cardinality и стоимости планов. «Статистика забуксовала» обычно означает, что данные уже существенно изменились, а статистика ещё не отражает эти изменения, поэтому planner может выбрать плохой execution plan.
	
Для каждой колонки PostgreSQL собирает, например:
	
- `n_distinct` — приблизительное количество различных значений;
- **most common values** — наиболее частые значения;
- частоты этих значений;
- **histogram bounds** — границы диапазонов значений;
- correlation — корреляция порядка значений с физическим порядком строк;
- для некоторых типов данных — дополнительную статистику.
	
То есть статистика может устареть при вставке значений и если неучтенных значений много, то планировщик будет строить неэффективные запросы, ориентируясь на устаревшую статистику. Статистика не обновляется автоматически, т.к. это довольно дорогостоящая операция
	
	
**Синтаксис ручного ANALYZE:**
	
```sql
ANALYZE orders;
```
	
----
**Autovacuum/autoanalyze** запускает `ANALYZE`, когда в таблице накопилось достаточно изменений. 
	
```
INSERT / UPDATE / DELETE
        ↓
счётчик изменений ↑
        ↓
достигнут threshold
        ↓
autovacuum launcher
        ↓
ANALYZE
        ↓
статистика обновлена
```
	
Порог зависит от размера таблицы и настроек, в частности:
	
```
analyze threshold ≈
autovacuum_analyze_threshold
+
autovacuum_analyze_scale_factor × number_of_rows
```
	
Например, при дефолтных значениях для таблицы с 1 млн строк порог порядка:
	
```
50 + 0.1 × 1 000 000 ≈ 100 050 изменений
```
	
То есть PostgreSQL **намеренно допускает**, что статистика некоторое время будет неактуальной.
<!--ID: 1788359637613-->


Q: Что оптимальнее `BIGINT` или `UUID` в качестве PK? Верно ли, что оследовательный UUID быстрее Int?
A:    
- для `INT` — **4 bytes;**
- для `BIGINT` — **8 bytes**;
- UUID — **16 bytes**.
	
Если ожидается больше ~2.1 млрд положительных ID, обычно используют `BIGINT`, а не `INT`.
	
Это означает более компактный B-tree:
```
INT       → больше ключей помещается на страницу
BIGINT    → меньше
UUID      → ещё меньше
```
Следовательно, для UUID индекс обычно больше, а B-tree может иметь больше страниц/уровней и хуже использовать CPU cache.
	
>Если UUID используется как PK, его размер начинает влиять **не только на PK index, но и на FK, содержащие этот PK в других таблицах**. — это не только вопрос размера самой таблицы. Он **транзитивно увеличивает размер всех индексов и таблиц, содержащих этот ID/FK**.
	
---
**Последовательный UUID (UUIDv7)**
	
У случайного UUIDv4:
	
```
UUIDv4:
	
A7...
12...
F3...
48...
```
вставки происходят в разных местах B-tree.
	
У UUIDv7 значения примерно упорядочены по времени:
	
```
UUIDv7:
	
0198...A
0198...B
0198...C
0198...D
```
Поэтому новые записи преимущественно идут **в конец индекса**, как у sequence-based `BIGINT`.
	
То есть UUIDv7 решает главную проблему UUIDv4 — плохую locality:
	
```
UUIDv4:
random → много random insert → page splits
	
UUIDv7:
ordered → append-like insert → хорошая locality
	
BIGINT sequence:
ordered → append-like insert + минимальный размер
```
	
Если чисто по производительности БД/размеру индексов — **BIGINT обычно выигрывает**.
	
UUIDv7 выбирают не ради большей скорости, а ради **глобально независимой генерации ID + хорошей временной локальности**.
<!--ID: 1788359637616-->


Q: Почему bulk Insert, bulk-update лучше обычного insert,update какие оптимизации он использует?
A:   Плюсы:
1. Меньше сетевых round-trip'ов
2. Меньше overhead на parsing/planning
3. WAL эффективнее используется - вместо одной записи на каждую вставленную строку - одна запись на каждую вставленную страницу
Минусы:
-  Большое потребление WAL - реплика может сильно отстать;
- Длинная транзакция удерживает старый snapshot **→ VACUUM не может очистить старые версии → table bloat**. Это значит, что другие данные (не задействованные в текущем обновлении/вставке), даже если следующие транзакции их уже удалили, не могут быть очищены
- Больше блокировок и дольше они живут - Row-level locks будут удерживаться **до конца транзакции (скорее актуально для UPDATE)**.
- Больше риск rollback
	
**Tradeoffs:**
	
```
              Большая транзакция
                     │
       ┌─────────────┴─────────────┐
       │                           │
    Плюсы                       Минусы
       │                           │
  atomicity                  long snapshot -> table bloat
  меньше commits             locks держатся долго
  меньше overhead            replication lag
                             большой rollback
```
	
Если **атомарность всего bulk operation не требуется**, часто лучше:
	
```
BEGIN
  UPDATE 10 000 rows
COMMIT
	
BEGIN
  UPDATE 10 000 rows
COMMIT
	
BEGIN
  UPDATE 10 000 rows
COMMIT
...
```
<!--ID: 1788359637619-->



Q:  Сравни`COPY` vs multi-row `INSERT`
A:



Есть ещё `COPY` в контексте **новой/пустой таблицы**. Если таблица ещё не содержит данных и нужно загрузить огромный объём, можно получить ещё больший выигрыш, если минимизировать работу с индексами/constraints на время загрузки — например, загрузить данные в staging table, а затем построить индексы.

Построить индекс после bulk load часто выгоднее, чем поддерживать B-tree **на каждую вставляемую строку**:

```
1 млн INSERT
   ↓
обновлять B-tree 1 млн раз
	
vs
	
COPY 1 млн rows
   ↓
CREATE INDEX
   ↓
построить B-tree bulk-операцией
```
	
Поэтому для ETL огромных объёмов часто используют:
	
```
COPY → staging table → transformations → INSERT/merge → indexes
```
а не огромный `INSERT ... VALUES`.


Q: Какие уровни lock есть в Postgres?
A: Каждой операции в Postgres соответствует два уровня lock: 
- Table-level lock
- Row-level lock 
	
При чем Row level lock есть не всегда.
	
---
**Row-level Locks:**
	
![[Pasted image 20260819191028.png]]
	
![[Pasted image 20260819190957.png]]
	
---
**Table-level locks:**
	
![[Pasted image 20260819191127.png]]
	
![[Pasted image 20260819191054.png]]
<!--ID: 1788359637623-->


### 1. MVCC + VACUUM

Q: Как работает MVCC в PostgreSQL и почему `UPDATE` не изменяет строку "на месте"?

A: PostgreSQL использует MVCC (Multi-Version Concurrency Control). При `UPDATE` старая версия строки остаётся в таблице, а создаётся новая версия. Каждая версия содержит служебные данные (`xmin`, `xmax`), по которым PostgreSQL определяет, какие транзакции могут её видеть.
	
Это позволяет читателям не блокировать писателей и наоборот. Но обратная сторона — старые версии (`dead tuples`) нужно периодически удалять.
<!--ID: 1788359637627-->


---

Q: Что такое dead tuple?

A: Это старая версия строки, которая больше не видна ни одной активной транзакции.
	
Например:
	
```sql
UPDATE users SET name = 'Bob' WHERE id = 1;
```
	
Старая версия:
	
```text
id=1, name='Alice'
```
	
становится потенциально мёртвой, а новая:
	
```text
id=1, name='Bob'
```
	
становится актуальной.
	
После завершения всех транзакций, которые ещё могли видеть старую версию, VACUUM может освободить её пространство для повторного использования.
<!--ID: 1788359637630-->


---

Q: Что произойдёт, если VACUUM работает плохо или не успевает за изменениями?

A: Растёт количество dead tuples. Это приводит к нескольким проблемам:
	
- таблица физически раздувается;
    
- индексы также могут раздуваться;
    
- увеличивается количество страниц, которые нужно прочитать;
    
- ухудшается cache hit ratio;
    
- возрастает количество I/O;
    
- autovacuum приходится работать всё интенсивнее;
    
- при определённых сценариях может расти latency запросов.
    
Растет **latency запросов**, особенно чтения больших объёмов данных. Но VACUUM может влиять и на latency записи из-за конкуренции за I/O и CPU.
<!--ID: 1788359637633-->


---

Q: Почему dead tuples особенно вредны для индексов?

A: При `UPDATE` PostgreSQL обычно должен создать новые индексные записи. Старые индексные записи не всегда могут быть немедленно удалены.
	
В результате индекс может содержать большое количество устаревших записей. Даже если логически в таблице находится 10 млн строк, физически индекс может быть значительно больше необходимого.
<!--ID: 1788359637636-->


---

Q: Чем отличаются `VACUUM`, `VACUUM FULL` и autovacuum?

A:  
	
**VACUUM**
	
- очищает dead tuples;
    
- позволяет повторно использовать освободившееся пространство;
    
- обычно не блокирует таблицу для обычной работы;
    
- не уменьшает физический размер файла таблицы обратно ОС.
    
	
**VACUUM FULL**
	
- фактически переписывает таблицу;
    
- может существенно уменьшить физический размер;
    
- требует значительной I/O;
    
- получает сильную блокировку таблицы;
    
- обычно применяется как специальная операция, а не как регулярная уборка.
    
	
**autovacuum**
	
- автоматический механизм, который запускает VACUUM/ANALYZE;
    
- отслеживает изменения таблиц;
    
- особенно важен для активно изменяемых таблиц.
<!--ID: 1788359637639-->



---

Q: Почему `VACUUM` не обязательно уменьшает размер таблицы на диске?

A: VACUUM обычно помечает пространство dead tuples как свободное для повторного использования **внутри самой таблицы**.
	
Например:
	
```text
до:
[used][dead][used][dead][used]
	
после VACUUM:
[used][free][used][free][used]
```
	
Размер файла может остаться прежним, но следующие `INSERT` смогут использовать эти свободные области.
<!--ID: 1788359637642-->


---

Q: Что такое transaction ID wraparound и какое отношение к нему имеет VACUUM?

A: PostgreSQL использует ограниченное пространство для transaction IDs. Если старые XID не замораживать, со временем может возникнуть опасность переполнения.
	
VACUUM выполняет freezing старых tuple (`FrozenXID`), благодаря чему PostgreSQL может безопасно считать очень старые версии видимыми для всех.
	
Поэтому VACUUM — это не только оптимизация производительности, но и **механизм, необходимый для предотвращения transaction ID wraparound**.
	
Здесь важно разделить **XID транзакции** и `xmin/xmax` конкретной tuple
	
У каждой транзакции PostgreSQL есть transaction ID (XID). В tuple действительно хранятся `xmin` и `xmax` — это XID транзакции, которая создала tuple, и транзакции, которая её удалила/заменила. То есть твоя интуиция правильная: **XID действительно записываются в tuples**.
	
Проблема в том, что XID в PostgreSQL имеют ограниченный диапазон — фактически это 32-битное пространство идентификаторов, которое используется циклически.
	
Упрощённо:
```
1 → 2 → 3 → ... → 4 294 967 295 → 0 → 1 → 2 → ...
```
	
Поэтому PostgreSQL не может просто бесконечно хранить старые XID и сравнивать их как обычные числа. Ему нужно "замораживать" достаточно старые tuple.
	
Например, представим:
	
tuple:
	
    xmin = 100
	
Если эта tuple настолько старая, что PostgreSQL уже знает: "транзакция 100 точно завершилась давно и tuple создана транзакцией, которая видима всем", то PostgreSQL может **заморозить** её XID.
	
После freezing PostgreSQL фактически перестаёт зависеть от старого XID при определении видимости этой tuple.
	
Именно это является одной из важнейших функций VACUUM:
```
старые tuples
      ↓
VACUUM
      ↓
dead tuples очищаются
+
старые XID замораживаются
      ↓
можно безопасно продолжать использовать новые XID
```
	
Если VACUUM слишком долго не работает, старые XID остаются незамороженными. В какой-то момент PostgreSQL может приблизиться к **transaction ID wraparound** — ситуации, когда старые XID могут начать выглядеть как относительно новые из-за циклического диапазона.
	
Это уже не обычная проблема производительности. PostgreSQL должен предотвратить потенциально неправильное определение видимости данных, поэтому при критическом приближении к wraparound система начинает принудительно заниматься freezing, а в крайнем случае может прекратить нормальные операции с базой, чтобы защититься от повреждения логики видимости.
	
Поэтому VACUUM нужен не только для:
```
dead tuples → освобождение места
```
	
но и для:
```
старые XID → freezing → защита от transaction ID wraparound
```
	
**Что важно для Senior:** не нужно запоминать конкретные внутренние детали сравнения XID. Главное понимать причинную цепочку:
	
```
XID ограничены и циклически переиспользуются
	
        ↓
	
старые XID нельзя оставлять незамороженными навсегда
	
        ↓
	
VACUUM выполняет freezing старых tuples
	
        ↓
	
это предотвращает XID wraparound
```
	
	
И да: `xmin/xmax` действительно находятся в tuple header. Именно поэтому старые tuple должны быть обработаны VACUUM.
<!--ID: 1788359637645-->


---

### 2. Индексы под реальные сценарии запросов

Q: Почему порядок колонок в составном B-tree индексе имеет значение?

A: Для индекса:
	
```sql
CREATE INDEX idx ON orders(customer_id, created_at);
```
	
PostgreSQL организует данные сначала по `customer_id`, затем внутри каждого значения — по `created_at`.
	
Поэтому индекс отлично подходит для:
	
```sql
WHERE customer_id = 123
```
	
и:
	
```sql
WHERE customer_id = 123
  AND created_at > '2026-01-01'
```
	
Но обычно значительно хуже для:
	
```sql
WHERE created_at > '2026-01-01'
```
	
потому что первая колонка индекса (`customer_id`) не ограничена.
<!--ID: 1788359637649-->


---

Q: Какое практическое правило выбора порядка колонок в составном индексе?

A: Для классических B-tree запросов часто полезно думать так:
	
```text
equality conditions
        ↓
range conditions
        ↓
ordering
```
	
Например:
	
```sql
WHERE tenant_id = ?
  AND status = ?
  AND created_at > ?
ORDER BY created_at
```
	
может хорошо соответствовать:
	
```text
(tenant_id, status, created_at)
```
	
Но это не абсолютное правило: реальный workload и selectivity нужно проверять через `EXPLAIN`.
<!--ID: 1788359637651-->


---

Q: Что такое partial index?

A: Индекс только для строк, удовлетворяющих условию.
	
Например:
	
```sql
CREATE INDEX idx_orders_pending
ON orders(created_at)
WHERE status = 'PENDING';
```
	
Если таблица содержит 100 млн заказов, но только 100 тыс. находятся в `PENDING`, такой индекс может быть намного меньше полного.
	
Запрос:
	
```sql
SELECT *
FROM orders
WHERE status = 'PENDING'
  AND created_at > now() - interval '1 hour';
```
	
может эффективно использовать этот индекс.
<!--ID: 1788359637654-->


---

Q: Что такое covering index в PostgreSQL?

A: Это индекс, содержащий все данные, необходимые запросу, благодаря чему PostgreSQL потенциально может выполнить **Index Only Scan** и не обращаться к heap за каждой строкой.
	
Например:
	
```sql
CREATE INDEX idx_users_email
ON users(email)
INCLUDE (name);
```
	
Для:
	
```sql
SELECT name
FROM users
WHERE email = 'a@example.com';
```
	
`name` присутствует непосредственно в индексе.
	
Но Index Only Scan возможен эффективно только когда visibility map показывает, что соответствующие heap pages не требуют проверки видимости.
<!--ID: 1788359637658-->


---

Q: Почему ORM может незаметно привести к полному сканированию таблицы?

A: ORM генерирует SQL, а индекс работает именно с SQL, а не с исходным Java/Kotlin-кодом.
	
Например, есть индекс:
	
```sql
CREATE INDEX idx_users_email ON users(email);
```
	
Но ORM генерирует:
	
```sql
WHERE LOWER(email) = 'john@example.com'
```
	
Обычный индекс по `email` здесь может не помочь.
	
Нужен, например:
	
```sql
CREATE INDEX idx_users_email_lower
ON users(LOWER(email));
```
	
Другие распространённые причины:
	
- `LIKE '%abc'` — ведущий wildcard;
    
- приведение типов;
    
- функции над колонкой;
    
- `OR`, делающий план менее эффективным;
    
- отсутствие подходящего составного индекса;
    
- низкая selectivity;
    
- неправильная статистика;
    
- запрос возвращает слишком большую долю таблицы.
    
	
Главный инструмент проверки:
	
```sql
EXPLAIN (ANALYZE, BUFFERS)
SELECT ...
```
<!--ID: 1788359637662-->


---

### 3. Блокировки и DDL

Q: Какие режимы блокировок PostgreSQL нужно знать Senior-разработчику?

A:
	
|Lock|Типичный пример|Что важно|
|---|---|---|
|`ACCESS SHARE`|обычный `SELECT`|самый слабый; конфликтует практически только с `ACCESS EXCLUSIVE`|
|`ROW SHARE`|`SELECT ... FOR UPDATE` / `FOR SHARE`|используется при блокировке строк|
|`ROW EXCLUSIVE`|`INSERT`, `UPDATE`, `DELETE`|обычные DML|
|`SHARE UPDATE EXCLUSIVE`|`VACUUM`, `ANALYZE`, некоторые `ALTER TABLE`|maintenance/schema operations|
|`SHARE`|обычный `CREATE INDEX`|может блокировать запись|
|`SHARE ROW EXCLUSIVE`|некоторые DDL|сильнее, чем обычные DML locks|
|`EXCLUSIVE`|некоторые специальные операции|блокирует почти всё|
|`ACCESS EXCLUSIVE`|многие `ALTER TABLE`, `DROP TABLE`, `TRUNCATE`|самый сильный|

FOR KEY SHARE, FOR KEY UPDATE

Q: Какой lock получает обычный SELECT?

A: Обычный:
<!--ID: 1788359637665-->


```
SELECT * FROM users;
```

получает:
	
`ACCESS SHARE`
	
Он не блокирует обычные:

```
INSERT
	
UPDATE
	
DELETE
```
	
Именно поэтому обычное чтение и запись в PostgreSQL могут нормально выполняться одновременно.
	
Но `ACCESS SHARE` конфликтует с `ACCESS EXCLUSIVE`.
	
Например:
	
```
Transaction A:
	
SELECT * FROM users;
	
-- долго выполняется
	  
	
Transaction B:
	
ALTER TABLE users ADD COLUMN age integer;
```
	
Если `ALTER TABLE` требует `ACCESS EXCLUSIVE`, он будет ждать завершения `SELECT`.

---

Q: Какой lock получают INSERT, UPDATE и DELETE?

A: Они получают:
	
```
ROW EXCLUSIVE
```
Например:
```
UPDATE users
	
SET name = 'Bob'
	
WHERE id = 1;
```
получает table-level `ROW EXCLUSIVE` и дополнительно блокирует изменяемую строку на row level.
<!--ID: 1788359637669-->


Поэтому важно не путать:

ROW EXCLUSIVE

с:

row-level lock

Название немного обманчиво: `ROW EXCLUSIVE` — это **table-level lock mode**.

---

Q: Что происходит при `SELECT ... FOR UPDATE`?

A:

SELECT *

FROM orders

WHERE id = 123

FOR UPDATE;

Получает table-level:

ROW SHARE

и одновременно устанавливает **row-level lock** на найденную строку.

Именно row-level lock здесь важен для конкурентного изменения:

T1:

SELECT ... FOR UPDATE;

  

T2:

UPDATE ... WHERE id = 123;

       ↓

      WAIT

---

Q: Что такое `SHARE UPDATE EXCLUSIVE` и почему он важен?

A: Это lock, который используется различными maintenance/schema operations.
<!--ID: 1788359637673-->


Например, обычный:

VACUUM users;

получает `SHARE UPDATE EXCLUSIVE`.

Также этот режим используется рядом операций вроде некоторых форм:

ALTER TABLE

CREATE/DROP INDEX CONCURRENTLY

ANALYZE

VACUUM

Это существенно более мягкий режим, чем `ACCESS EXCLUSIVE`: обычные `SELECT`, `INSERT`, `UPDATE`, `DELETE` обычно могут продолжать работать.

---

Q: Какой lock получает обычный `CREATE INDEX`?

A: Обычный:
<!--ID: 1788359637676-->


CREATE INDEX idx_users_email

ON users(email);

получает `SHARE`.

Главная проблема: `SHARE` конфликтует с `ROW EXCLUSIVE`, который получают `INSERT/UPDATE/DELETE`.

Поэтому на большой production-таблице обычный:

CREATE INDEX

может мешать записи.

Именно поэтому для активно используемых таблиц часто используют:

CREATE INDEX CONCURRENTLY ...

У него существенно более сложная схема блокировок, но он позволяет значительно лучше сосуществовать с обычным DML.

---

Q: Какие maintenance operations важно знать в контексте блокировок?

A: Основные примеры:
<!--ID: 1788359637680-->


**VACUUM**

VACUUM users;

Использует `SHARE UPDATE EXCLUSIVE`.

Обычный DML продолжает работать.

**ANALYZE**

ANALYZE users;

Также использует относительно мягкую блокировку и предназначен для обновления planner statistics.

**CREATE INDEX**

CREATE INDEX idx ON users(email);

Использует более конфликтующий `SHARE`, поэтому может мешать DML.

**CREATE INDEX CONCURRENTLY**

CREATE INDEX CONCURRENTLY idx ON users(email);

Предназначен для создания индекса с минимальным вмешательством в обычную работу таблицы.

**VACUUM FULL**

VACUUM FULL users;

Это принципиально другая история: таблица переписывается, поэтому требуется очень сильная блокировка, фактически блокирующая конкурентный доступ на время операции.

**TRUNCATE**

TRUNCATE users;

получает `ACCESS EXCLUSIVE`.

**DROP TABLE**

DROP TABLE users;

также требует `ACCESS EXCLUSIVE`.

**ALTER TABLE**

Зависит от конкретной операции, но многие распространённые операции требуют очень сильную блокировку, вплоть до:

ACCESS EXCLUSIVE

Поэтому Senior должен всегда спрашивать:

> "Какой lock требует конкретная DDL-операция и можно ли выполнить её online/concurrently?"
---

Q: Почему простой `ALTER TABLE` может внезапно остановить production?

A: Потому что многие DDL-операции получают `ACCESS EXCLUSIVE`.
	
Например:
	
```sql
ALTER TABLE users ADD COLUMN foo text;
```
	
может ожидать существующие транзакции.
	
Но ситуация становится хуже, если DDL уже ждёт блокировку:
	
```text
long SELECT
     ↓
ALTER TABLE ждёт
     ↓
новые запросы тоже начинают ждать
     ↓
растёт очередь
     ↓
растёт latency
```
	
Поэтому проблема может выглядеть как "весь сервис внезапно стал медленным", хотя причиной является один DDL.
<!--ID: 1788359637682-->


---

Q: Как посмотреть текущие блокировки и кто кого блокирует?

A:  
```sql
SELECT
    blocked.pid AS blocked_pid,
    blocked.query AS blocked_query,
    blocking.pid AS blocking_pid,
    blocking.query AS blocking_query
FROM pg_stat_activity blocked
JOIN pg_locks blocked_locks
    ON blocked.pid = blocked_locks.pid
JOIN pg_locks blocking_locks
    ON blocking_locks.locktype = blocked_locks.locktype
   AND blocking_locks.database IS NOT DISTINCT FROM blocked_locks.database
   AND blocking_locks.relation IS NOT DISTINCT FROM blocked_locks.relation
   AND blocking_locks.granted = true
   AND blocking_locks.pid != blocked_locks.pid
JOIN pg_stat_activity blocking
    ON blocking.pid = blocking_locks.pid
WHERE blocked_locks.granted = false;
```
<!--ID: 1788359637684-->


Также полезно:

```sql
SELECT pg_blocking_pids(pid)
FROM pg_stat_activity;
```

---

Q: Как безопаснее выполнять создание индекса на production?

A: Для больших активно используемых таблиц часто используют:
<!--ID: 1788359637687-->


```sql
CREATE INDEX CONCURRENTLY idx_users_email
ON users(email);
```

В отличие от обычного `CREATE INDEX`, операция значительно меньше мешает обычным `INSERT/UPDATE/DELETE`.

Но есть trade-offs:

- занимает больше времени;
    
- выполняется отдельными фазами;
    
- требует больше ресурсов;
    
- нельзя выполнять внутри обычного transaction block;
    
- при ошибке может остаться invalid index, который нужно удалить.
    

---

Q: Как безопасно выполнять массовое обновление миллионов строк?

A: Не делать:
<!--ID: 1788359637691-->


```sql
UPDATE orders
SET processed = true
WHERE processed = false;
```

если это десятки/сотни миллионов строк.

Лучше разбить операцию:

```sql
UPDATE orders
SET processed = true
WHERE id IN (
    SELECT id
    FROM orders
    WHERE processed = false
    ORDER BY id
    LIMIT 10000
);
```

И повторять batch за batch.

Преимущества:

- меньше длительность транзакций;
    
- меньше WAL burst;
    
- меньше блокировок;
    
- меньше dead tuples за одну транзакцию;
    
- меньше вероятность огромного rollback;
    
- проще контролировать нагрузку.
    

Размер batch подбирается экспериментально.

---

Q: Почему нельзя просто обновить 100 млн строк одной транзакцией?

A: Потому что одна огромная транзакция:
<!--ID: 1788359637694-->


- генерирует огромное количество WAL;
    
- создаёт огромное количество dead tuples;
    
- удерживает snapshot;
    
- может мешать VACUUM;
    
- занимает ресурсы;
    
- долго откатывается при ошибке;
    
- может вызвать replication lag;
    
- создаёт длительные блокировки.
    

---

### 4. Уровни изоляции и аномалии

Q: Какие уровни изоляции PostgreSQL реально нужно знать?

A:

|Isolation|Что гарантирует|
|---|---|
|`Read Committed`|каждый statement видит свой snapshot|
|`Repeatable Read`|вся транзакция работает с одним snapshot|
|`Serializable`|результат эквивалентен некоторому последовательному выполнению|

PostgreSQL фактически не имеет отдельной реализации классического ANSI `Read Uncommitted`: он работает как `Read Committed`.

---

Q: Что означает `Read Committed` в PostgreSQL?

A: Каждый SQL statement получает собственный snapshot.
<!--ID: 1788359637697-->


Например:

```text
T1: BEGIN
T1: SELECT ...  → видит A

T2: UPDATE ...
T2: COMMIT

T1: SELECT ...  → уже может видеть B
```

То есть два `SELECT` внутри одной транзакции могут увидеть разные данные.

---

Q: Что меняется в `Repeatable Read`?

A: Транзакция получает snapshot, который сохраняется.
<!--ID: 1788359637701-->


```text
T1: BEGIN
T1: SELECT → A

T2: UPDATE
T2: COMMIT

T1: SELECT → всё ещё A
```

При конфликтующих concurrent updates PostgreSQL может завершить транзакцию ошибкой serialization failure.

---

Q: Что такое Serializable и зачем он нужен?

A: `Serializable` пытается гарантировать, что concurrent transactions дадут результат, эквивалентный некоторому последовательному выполнению.
<!--ID: 1788359637704-->


Но за это приходится платить:

```text
serialization failure
        ↓
transaction rollback
        ↓
application retry
```

Поэтому приложение должно уметь повторять транзакцию.

---

Q: Что такое lost update?

A: Две транзакции читают одно значение и затем перезаписывают его:
<!--ID: 1788359637708-->


```text
balance = 100

T1: read 100
T2: read 100

T1: write 80
T2: write 70

result = 70
```

Изменение T1 фактически потеряно.

Решения зависят от сценария:

- `SELECT ... FOR UPDATE`;
    
- optimistic locking (`version`);
    
- atomic SQL update;
    
- более высокий isolation level.
    

---

Q: Как дебажить "пропавшие" или "дублирующиеся" строки?

A: Нужно прежде всего восстановить **timeline конкурентных транзакций**.
<!--ID: 1788359637711-->


Полезные инструменты:

```sql
SELECT *
FROM pg_stat_activity;
```

для текущих транзакций.

```sql
SELECT *
FROM pg_locks;
```

для блокировок.

```sql
SELECT pg_blocking_pids(pid);
```

для поиска блокирующих процессов.

Также:

- PostgreSQL logs;
    
- `log_min_duration_statement`;
    
- `log_lock_waits`;
    
- `deadlock_timeout`;
    
- `pg_stat_statements`;
    
- `EXPLAIN (ANALYZE, BUFFERS)`;
    
- tracing на уровне приложения;
    
- correlation/request IDs.
    

При расследовании полезно записать:

```text
T1 BEGIN
T1 SELECT
T2 BEGIN
T2 UPDATE
T2 COMMIT
T1 UPDATE
T1 COMMIT
```

и определить snapshot/lock каждого шага.

---

Q: Как понять, что проблема действительно вызвана concurrency, а не багом приложения?

A: Нужно воспроизвести конкурентный сценарий.
<!--ID: 1788359637714-->


Например, открыть две `psql` сессии и вручную выполнить:

```text
Session A                 Session B

BEGIN                     BEGIN
SELECT ...                SELECT ...
UPDATE ...                UPDATE ...
```

После этого можно увидеть:

- кто ждёт;
    
- какой lock удерживается;
    
- какой snapshot используется;
    
- возникает ли deadlock;
    
- возникает ли serialization failure.
    

Это гораздо надёжнее, чем анализировать только Java-код.

---

### 5. Управление соединениями

Q: Почему слишком большое количество PostgreSQL connections вредно?

A: Каждое соединение — это backend process PostgreSQL с собственными ресурсами.
<!--ID: 1788359637717-->


Слишком большое количество connections приводит к:

- дополнительному memory overhead;
    
- context switching;
    
- конкуренции за CPU;
    
- большему количеству active queries;
    
- увеличению lock contention;
    
- ухудшению cache locality;
    
- резкому падению производительности под нагрузкой.
    

Поэтому:

```text
more connections ≠ more throughput
```

---

Q: Почему connection pool в приложении необходим?

A: Создание PostgreSQL connection — относительно дорогая операция.
<!--ID: 1788359637720-->


Pool позволяет:

```text
request
   ↓
borrow connection
   ↓
execute SQL
   ↓
return connection
```

вместо создания нового connection для каждого request.

Для Java обычно используется HikariCP.

---

Q: Как выбирать размер connection pool?

A: Не существует универсального:
<!--ID: 1788359637723-->


```text
pool = CPU * 2
```

Нужно учитывать:

- количество application instances;
    
- CPU PostgreSQL;
    
- характер запросов;
    
- latency;
    
- concurrency;
    
- количество других клиентов;
    
- connection limits.
    

Критически важно смотреть на **сумму**:

```text
pool_size × number_of_instances
```

Например:

```text
20 connections × 30 pods = 600 connections
```

даже если каждый pod сам по себе выглядит нормально.

---

Q: Что такое PgBouncer?

A: PgBouncer — lightweight connection pooler перед PostgreSQL.
<!--ID: 1788359637727-->


Архитектура:

```text
1000 application connections
          ↓
      PgBouncer
          ↓
     50 PostgreSQL
     connections
```

Он позволяет отделить количество client connections от реального количества PostgreSQL backend connections.

---

Q: Какие режимы pooling есть в PgBouncer?

A:

**Session pooling**

```text
client → PostgreSQL connection
```

соединение закрепляется за клиентом на всю session.

**Transaction pooling**

connection выделяется на время транзакции:

```text
BEGIN
  ↓
Postgres connection
  ↓
COMMIT
  ↓
connection возвращается в pool
```

Обычно это наиболее эффективный режим для stateless applications.

Но некоторые session-level features могут работать некорректно.

**Statement pooling**

connection возвращается после каждого statement.

Самый агрессивный режим, но с большим количеством ограничений.

---

Q: Что такое `idle in transaction`?

A: Connection находится внутри транзакции, но в данный момент ничего не выполняет:
<!--ID: 1788359637731-->


```text
BEGIN;
SELECT ...;

-- application ничего не делает

idle in transaction
```

Это опасно, потому что transaction может удерживать snapshot и блокировать очистку старых tuple.

Можно искать:

```sql
SELECT pid, usename, state,
       xact_start, query_start, query
FROM pg_stat_activity
WHERE state = 'idle in transaction';
```

---

Q: Почему долгий `idle in transaction` может мешать VACUUM?

A: Транзакция удерживает старый snapshot.
<!--ID: 1788359637734-->


VACUUM не может удалить tuple, который потенциально всё ещё нужен этой транзакции.

Поэтому:

```text
idle transaction
      ↓
старый snapshot
      ↓
dead tuples нельзя удалить
      ↓
table bloat
      ↓
ухудшение performance
```

---

### 6. WAL, checkpoints и репликация

Q: Что такое WAL?

A: WAL (Write-Ahead Log) — журнал изменений PostgreSQL.
<!--ID: 1788359637738-->


Основной принцип:

```text
изменение
   ↓
WAL записан
   ↓
data pages могут быть записаны позже
```

Это позволяет:

- восстановиться после crash;
    
- реплицировать изменения;
    
- выполнять PITR;
    
- поддерживать durability.
    

---

Q: Что такое checkpoint?

A: Checkpoint — момент, когда PostgreSQL обеспечивает запись dirty pages на disk и создаёт checkpoint record в WAL.
<!--ID: 1788359637741-->


После crash PostgreSQL может начинать recovery с последнего checkpoint вместо обработки всей истории WAL.

---

Q: Как checkpoints могут вызывать скачки latency?

A: Если PostgreSQL вынужден интенсивно записывать огромное количество dirty pages за короткий период, возникает I/O burst.
<!--ID: 1788359637743-->


Упрощённо:

```text
обычная работа
   ↓
dirty pages накапливаются
   ↓
checkpoint
   ↓
массовая запись на disk
   ↓
I/O contention
   ↓
query latency spike
```

Особенно плохо это проявляется на storage с ограниченным IOPS/throughput.

---

Q: Какие настройки checkpoint особенно важны?

A: В первую очередь:
<!--ID: 1788359637747-->


```text
checkpoint_timeout
max_wal_size
checkpoint_completion_target
```

**`checkpoint_timeout`**

Максимальное время между checkpoints.

**`max_wal_size`**

Приблизительный объём WAL, после которого checkpoint может потребоваться раньше.

**`checkpoint_completion_target`**

Определяет, насколько равномерно PostgreSQL старается выполнять checkpoint writes в течение checkpoint interval.

Идея:

```text
плохо:
████████████████
        checkpoint burst

лучше:
███ ███ ███ ███
равномерный I/O
```

---

Q: Почему увеличение `max_wal_size` иногда уменьшает latency spikes?

A: Если checkpoint слишком часто запускается из-за быстрого накопления WAL, увеличение допустимого WAL объёма позволяет делать checkpoints реже.
<!--ID: 1788359637749-->


Но это не бесплатная оптимизация: после crash потенциально потребуется обработать больше WAL.

---

Q: Что такое streaming replication?

A: Primary отправляет WAL records на replica.
<!--ID: 1788359637753-->


```text
Primary
   ↓ WAL
Replica
```

Replica применяет WAL и поддерживает собственную копию данных.

---

Q: Почему replica может отставать?

A: Основные причины:
<!--ID: 1788359637756-->


1. Primary генерирует WAL быстрее, чем replica его получает.
    
2. Network bottleneck.
    
3. Replica медленно записывает WAL.
    
4. Replica медленно применяет WAL.
    
5. На replica выполняются тяжёлые read queries.
    
6. Storage имеет низкую производительность.
    
7. CPU replica перегружен.
    
8. Большой batch update на primary создаёт WAL burst.
    
9. Конфликты между recovery и запросами на hot standby.
    

---

Q: Чем отличаются write lag, flush lag и replay lag?

A: Они показывают разные стадии доставки WAL.
<!--ID: 1788359637759-->


Упрощённо:

```text
Primary generates WAL
       ↓
network
       ↓
Replica receives
       ↓
flush to disk
       ↓
replay/apply
```

Поэтому replica может:

- получить WAL, но ещё не flush;
    
- flush WAL, но ещё не replay;
    
- replay существенно отставать от primary.
    

---

### 7. Основы планировщика запросов

Q: Что делает `EXPLAIN (ANALYZE, BUFFERS)`?

A: `EXPLAIN` показывает предполагаемый execution plan.
<!--ID: 1788359637762-->


`ANALYZE` **реально выполняет запрос** и показывает фактические показатели.

`BUFFERS` показывает информацию о работе с PostgreSQL buffer/cache.

Например:

```sql
EXPLAIN (ANALYZE, BUFFERS)
SELECT *
FROM users
WHERE email = 'a@example.com';
```

---

Q: Что означает `Buffers: shared hit`?

A: PostgreSQL нашёл нужную страницу уже в shared buffers/cache.
<!--ID: 1788359637765-->


Например:

```text
Buffers: shared hit=1000
```

означает, что 1000 buffer pages были найдены в памяти PostgreSQL без чтения с disk.

---

Q: Что означает `shared read`?

A: Страница потребовалась, но не была доступна в shared buffers, поэтому PostgreSQL пришлось её прочитать с storage.
<!--ID: 1788359637768-->


Например:

```text
Buffers: shared hit=100
         shared read=5000
```

может указывать на существенный I/O.

---

Q: Что означает `shared dirtied`?

A: Сколько shared buffers были изменены во время выполнения.
<!--ID: 1788359637771-->


Это особенно полезно для write-heavy queries.

---

Q: Что означает `shared written`?

A: Сколько shared buffers было записано на storage во время выполнения.
<!--ID: 1788359637775-->


Это позволяет увидеть, что запрос не только читает, но и вызывает значительную запись.

---

Q: Что такое проблема estimated rows vs actual rows?

A: Планировщик строит план на основе статистики.
<!--ID: 1788359637778-->


Например:

```text
estimated rows = 10
actual rows    = 5,000,000
```

Планировщик ожидал маленький result set и выбрал index scan, но реальность оказалась огромной.

Это может привести к очень плохому execution plan.

---

Q: Как исправляют устаревшую статистику?

A:

```sql
ANALYZE users;
```

Обычно autovacuum также выполняет autoanalyze.

Если стандартной статистики недостаточно, можно использовать extended statistics:

```sql
CREATE STATISTICS ...
```

Они помогают планировщику учитывать зависимости между колонками, например когда:

```text
country
и
city
```

не являются независимыми величинами.

---

### 8. Наблюдаемость и slow queries

Q: Какие PostgreSQL-метрики наиболее полезны для production?

A: Хороший минимальный набор:
<!--ID: 1788359637782-->


```text
query latency p95/p99
active connections
idle in transaction
lock waits
deadlocks
cache hit ratio
temporary files
WAL generation
checkpoint frequency
checkpoint write time
autovacuum activity
replication lag
database size
table/index bloat
```

---

Q: Как обычно собирают PostgreSQL metrics?

A: Частый production stack:
<!--ID: 1788359637785-->


```text
PostgreSQL
    ↓
postgres_exporter
    ↓
Prometheus
    ↓
Grafana
```

При этом application metrics обычно идут через Micrometer/Prometheus отдельно.

---

Q: Как обычно реализуют slow query logging?

A: PostgreSQL предоставляет:
<!--ID: 1788359637787-->


```sql
log_min_duration_statement
```

Например:

```text
log_min_duration_statement = 500
```

означает: логировать statements, выполнявшиеся дольше 500 ms.

Это не означает "показывать только плохие запросы". Это просто duration threshold.

---

Q: Почему нельзя поставить `log_min_duration_statement = 0` на production бездумно?

A: Тогда будет логироваться практически каждый statement.
<!--ID: 1788359637790-->


При высоком QPS это может:

- резко увеличить объём логов;
    
- создать дополнительный I/O;
    
- усложнить анализ;
    
- увеличить стоимость хранения логов.
    

Поэтому threshold выбирают исходя из workload.

---

Q: Какие ещё настройки важны для диагностики медленных запросов?

A:

```text
log_min_duration_statement
log_statement
log_lock_waits
deadlock_timeout
log_checkpoints
```

А для агрегированной статистики запросов очень полезен `pg_stat_statements`.

Он позволяет смотреть:

- total execution time;
    
- calls;
    
- mean time;
    
- rows;
    
- shared blocks hit/read;
    
- temporary blocks;
    
- WAL-related statistics в поддерживаемых версиях.
    

---

Q: Чем `pg_stat_statements` отличается от slow query log?

A:

**slow query log**

```text
каждый конкретный медленный execution
```

**pg_stat_statements**

```text
агрегированная статистика по normalized query
```

Например:

```text
SELECT ... WHERE id = $1
calls = 10,000,000
total_time = ...
mean_time = ...
```

Это позволяет найти не только редкий ужасно медленный запрос, но и запрос, который выполняется миллионы раз.

---

### 9. Резервные копии и восстановление

Q: Как обычно строят backup strategy для production PostgreSQL?

A: Обычно это комбинация:
<!--ID: 1788359637793-->


```text
Base backups
      +
WAL archiving
      +
Recovery testing
```

Например:

```text
Primary
  │
  ├── base backup → backup storage
  │
  └── WAL archive → backup storage
```

Base backup даёт исходное состояние, WAL позволяет воспроизвести изменения после него.

---

Q: Что такое WAL archiving?

A: PostgreSQL может автоматически копировать завершённые WAL segments во внешнее хранилище через `archive_command` или соответствующий archive module.
<!--ID: 1788359637796-->


Концептуально:

```text
PostgreSQL WAL
      ↓
archive
      ↓
object storage / backup server
```

При восстановлении:

```text
base backup
    ↓
restore
    ↓
replay WAL
    ↓
desired point in time
```

---

Q: Что такое PITR?

A: Point-in-Time Recovery.
<!--ID: 1788359637799-->


Позволяет восстановить database до определённого момента времени.

Например:

```text
10:00 backup
10:10 INSERT
10:20 DELETE  ← ошибка оператора
10:30 обнаружили проблему
```

Можно восстановить состояние примерно на:

```text
10:19:59
```

если backup + необходимые WAL доступны.

---

Q: Почему одного daily dump недостаточно для серьёзной production базы?

A: Если backup делается раз в сутки:
<!--ID: 1788359637802-->


```text
backup 00:00
crash 23:59
```

можно потерять почти сутки данных.

WAL archiving позволяет значительно уменьшить RPO.

---

Q: Что такое RPO и RTO?

A:

**RPO — Recovery Point Objective**

Сколько данных максимально допустимо потерять.

**RTO — Recovery Time Objective**

Сколько времени допустимо восстанавливать сервис.

Например:

```text
RPO = 5 min
RTO = 30 min
```

означает:

- допустима потеря максимум ~5 минут данных;
    
- database должна быть восстановлена максимум за 30 минут.
    

---

Q: Почему backup без restore testing почти бесполезен?

A: Потому что:
<!--ID: 1788359637806-->


```text
backup успешно создан
```

не означает:

```text
database успешно восстанавливается
```

Могут отсутствовать:

- WAL;
    
- роли;
    
- passwords/secrets;
    
- extensions;
    
- конфигурация;
    
- tablespaces;
    
- нужные версии PostgreSQL;
    
- encryption keys;
    
- доступ к backup storage.
    

И самое главное — restore может занимать 12 часов вместо ожидаемых 30 минут.

---

Q: Как обычно проверяют восстановление?

A: Регулярно создают isolated environment и выполняют:
<!--ID: 1788359637808-->


```text
restore base backup
        ↓
restore roles/config
        ↓
restore extensions
        ↓
replay WAL
        ↓
run smoke tests
        ↓
measure restore duration
```

Проверяют:

- database открывается;
    
- таблицы существуют;
    
- критичные данные доступны;
    
- extensions установлены;
    
- приложения могут подключиться;
    
- PITR работает;
    
- время восстановления соответствует RTO.
    

---

Q: Почему роли и extensions нужно проверять отдельно?

A: Backup данных не всегда содержит весь внешний state PostgreSQL.
<!--ID: 1788359637812-->


Например, application может использовать:

```sql
CREATE EXTENSION pgcrypto;
```

или:

```sql
CREATE EXTENSION postgis;
```

Но на новой машине extension может отсутствовать.

Также database objects могут принадлежать определённым roles.

Поэтому production restore должен учитывать не только:

```text
tables + data
```

но и:

```text
roles
grants
extensions
configuration
tablespaces
```

---

### 10. Безопасность и права

Q: Какой главный принцип PostgreSQL security для приложения?

A: **Least privilege.**
<!--ID: 1788359637815-->


Application user должен иметь только права, необходимые для работы.

Например:

```text
application_user
    ↓
SELECT / INSERT / UPDATE
```

но не:

```text
DROP DATABASE
CREATE ROLE
SUPERUSER
```

---

Q: Почему приложение никогда не должно использовать `SUPERUSER`?

A: Потому что компрометация приложения превращается в компрометацию всей PostgreSQL instance.
<!--ID: 1788359637817-->


Если attacker получил credentials application user с `SUPERUSER`, он потенциально может:

- читать любые данные;
    
- изменять любые данные;
    
- создавать роли;
    
- менять configuration;
    
- получать доступ к объектам других приложений.
    

---

Q: Почему полезно иметь отдельного owner для database objects?

A: Например:
<!--ID: 1788359637820-->


```text
app_owner
    ↓ owns schema/tables

app_runtime
    ↓ application permissions
```

Application runtime user не обязан владеть таблицами.

Это уменьшает последствия компрометации приложения и позволяет отделить:

```text
DDL / migrations
```

от:

```text
normal runtime DML
```

---

Q: Что такое `public` schema и почему её права часто ограничивают?

A: PostgreSQL по умолчанию предоставляет определённые права через `public` schema.
<!--ID: 1788359637824-->


В production часто явно управляют этими правами, чтобы любой пользователь не мог создавать объекты там, где это не предусмотрено архитектурой.

Типичный подход:

```sql
REVOKE CREATE ON SCHEMA public FROM PUBLIC;
```

Далее приложения получают права только на необходимые schemas.

---

Q: Как ограничить сетевой доступ к PostgreSQL?

A: На нескольких уровнях:
<!--ID: 1788359637828-->


```text
network firewall/security groups
        ↓
PostgreSQL listen_addresses
        ↓
pg_hba.conf
        ↓
database roles/passwords
```

`pg_hba.conf` определяет, **кто**, **откуда**, **к какой БД** и **каким способом** может подключаться.

Например, не стоит делать:

```text
0.0.0.0/0
```

для production database без крайне веской причины.

---

Q: Что такое `pg_hba.conf`?

A: Это основной механизм host-based authentication PostgreSQL.
<!--ID: 1788359637831-->


Правило концептуально выглядит как:

```text
TYPE  DATABASE  USER  ADDRESS       METHOD
host  appdb     app   10.0.0.0/24   scram-sha-256
```

Оно определяет:

```text
кто → куда → откуда → каким методом
```

может подключаться.

---

Q: Почему SCRAM предпочтительнее старого MD5 authentication?

A: SCRAM (`scram-sha-256`) — современный механизм password authentication PostgreSQL, обеспечивающий более сильную защиту credentials по сравнению с устаревшим MD5-подходом.
<!--ID: 1788359637834-->


В production обычно стоит использовать SCRAM, если нет требований совместимости со старыми клиентами.

---

Q: Что означает "регулярная смена учётных данных"?

A: Credentials приложения не должны быть вечными.
<!--ID: 1788359637838-->


В production желательно иметь механизм:

```text
secret manager
     ↓
credential rotation
     ↓
application receives new credentials
```

При этом важно не просто менять пароль, а продумать **zero/minimal downtime rotation**:

```text
old credential valid
        +
new credential valid
        ↓
application switches
        ↓
old credential revoked
```

---

Q: Какие PostgreSQL security controls должен понимать Senior Backend Developer?

A: Минимальный набор:
<!--ID: 1788359637842-->


```text
roles
GRANT / REVOKE
ownership
schema privileges
pg_hba.conf
SCRAM
TLS
least privilege
SUPERUSER risks
PUBLIC privileges
row-level security
audit logging
credential rotation
network isolation
```

И главное — понимать security как несколько независимых уровней:

```text
Network
   ↓
Authentication
   ↓
Authorization
   ↓
Database object permissions
   ↓
Application authorization
```

Один только пароль PostgreSQL не является полноценной моделью безопасности.