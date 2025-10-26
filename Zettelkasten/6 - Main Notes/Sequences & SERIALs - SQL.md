
26-10-2025 13:32

Status: #baby 

Tags: [[SQL]]

---
# Sequences & SERIALs - SQL

Объект в БД, который выдаёт ~={orange}целые=~ чила по порядку.


```sql
--создание последовательности:
CREATE SEQUENCE first_sequence;

--получение следующего значения последовательности:
SELECT nextval('first_sequence');

--удаление последовательности:
DROP SEQUENCE first_sequence;
```


> [!note] 
> Автоинкремент работает только с целыми типами (`SMALLINT`, `INT`, `BIGINT`) через:
> - `SERIAL`,
> - `BIGSERIAL` 
> - `GENERATED ... AS IDENTITY`.
> 
> Если нужно автоинкрементировать **нецелевой тип**, например `NUMERIC`, `DECIMAL`, `DOUBLE PRECISION` — придётся делать это вручную.


---
### SERIAL

Синтаксический сахар (это макрос):

- Создаёт колонку `id integer NOT NULL`.
- Создаёт последовательность (sequence) с именем вроде `users_id_seq`.
- Назначает для `id` значение по умолчанию `nextval('users_id_seq'::regclass)`.

Пример использования:
```sql
CREATE TABLE users (
id SERIAL PRIMARY KEY,
name text
);
```
	
### GENERATED ... AS IDENTITY 

Более **современный SQL-стандартный** способ.  Под капотом  тоже использует sequence, но поведение более «чистое» и управляемое.

Пример использования:
```sql
CREATE TABLE users (
id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
name text
);
```

----
#### [[Sequences & SERIALs - SQL - Flashcards|Link to flashcards]]



---
### References:

