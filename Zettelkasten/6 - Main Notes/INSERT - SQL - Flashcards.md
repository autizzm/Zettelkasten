
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
