
Theory for the cards: [[INSERT - SQL]]

FILE TAGS: sql

Q: Можно ли использовать агрегатные функции при вставке данных в таблицу sql (INSERT)?
A: Да, можно. Но только при использовании вставки с `SELECT`:
```sql
INSERT INTO Goods (good_id, good_name, type)
SELECT COUNT(*)+1, 'Table', 2;
```
<!--ID: 1758969018951-->


Q: Как выглядит синтаксис вставки данных в таблицу sql?
A:   
```sql
INSERT INTO table_name [(table_field, ...)]
VALUES (value_of_table_field, ...) | SELECT table_field, ... FROM table_name ...
```
	
Можно и через `VALUES` и через `SELECT`.
	
Пример с `SELECT` для наглядности: (внутри `SELECT` можно использовать агрегатные функции)
	
```sql
INSERT INTO Goods (good_id, good_name, type)
SELECT 20, 'Table', 2;
```
	
Пример с `VALUES`: (внутри `VALUES` НЕЛЬЗЯ использовать агрегатные функции)
	
```sql
INSERT INTO Goods (good_id, good_name, type)
VALUES (20, 'Table', 2);
```
<!--ID: 1758969106274-->

Q: Надо ли самому заполнять поле `SERIAL` при вставке данных через `INSERT`?
A: Нет, это не обязательно, оно само заполнится следующим значением:
```sql
CREATE TABLE Goods (
	good_id SERIAL,
	good_name VARCHAR(255),
	type INT
);
```
	
```sql
INSERT INTO Goods (good_name, type) VALUES ('Table', 2);
```
	
Но, если укажем явно -> ошибки не будет:
 ```sql
 INSERT INTO Goods (good_id, good_name, type) VALUES (101,'Table', 2);
--ошибки не будет, если нет конфликта (например, PRIMARY KEY/UNIQUE).
 ```
<!--ID: 1759599237757-->

