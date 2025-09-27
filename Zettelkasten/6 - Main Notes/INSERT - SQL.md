
27-09-2025 12:41

Status: #child

Tags: [[SQL]]

---
# INSERT - SQL

### Синтаксис:

```sql
INSERT INTO table_name [(table_field, ...)]
VALUES (value_of_table_field, ...) | SELECT table_field, ... FROM table_name ...
```

Можно и через `VALUES` и через `SELECT`.

Пример с `SELECT` для наглядности:

```sql
INSERT INTO Goods (good_id, good_name, type)
SELECT 20, 'Table', 2;
```

Пример с `VALUES`:

```sql
INSERT INTO Goods (good_id, good_name, type)
VALUES (20, 'Table', 2);
```

---

> [!note] **В INSERT можно использовать агрегатные функции**
> Например (НЕЖЕЛАТЕЛЬНО ТАК ДЕЛАТЬ) заполнение PRIMARY KEY поля с помощью `MAX():`
> ```sql
> INSERT INTO Goods SELECT MAX(good_id) + 1, 'Table', 2 FROM Goods;
> ```
> ТАК МОЖНО ДЕЛАТЬ толко с SELECT:
> ```sql
> -- ошибка:
> INSERT INTO Goods VALUES(MAX(good_id) + 1, 'Table', 2 );
> -- aggregate functions are not allowed in VALUES
> ```


> [!note] **Поле SERIAL заполнять не надо**
> Поле `SERIAL` заполнять не надо:
> ```sql
> CREATE TABLE Goods (
>	good_id SERIAL,
>	good_name VARCHAR(255),
>	type INT
>);
> ```
> 
>```sql
>INSERT INTO Goods (good_name, type) VALUES ('Table', 2);
>```
> Но, если укажем явно -> ошибки не будет:
> ```sql
> INSERT INTO Goods (good_id, good_name, type) VALUES (101,'Table', 2);
> --ошибки не будет, если нет конфликта (например, PRIMARY KEY/UNIQUE).
> ```


----
#### [[INSERT - SQL - Flashcards|Link to flashcards]]



---
### References:

