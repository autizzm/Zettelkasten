
15-09-2025 19:21

Status: #baby

Tags: [[SQL]]

---
# viewing DBs - PostgreSQL

> [!note] **Встроенные БД**
В Postgres помимо пользовательских БД есть "встроенные": 
postgres, template0, template1

ЧТобы просмотреть список всех пользовательских БД в Postgres можно сделать запрос:
```sql
SELECT datname 
FROM pg_database 
WHERE datistemplate = false;
```

|datname|
|---|
|user_database_1|
|user_database_2|
|postgres|

### Что делают части запроса:

- **`pg_database`** — системная таблица с информацией о всех БД (имя, владелец, настройки, служебные флаги).
    
- **`datname`** — имя базы данных.
    
- **`datistemplate`** — булево поле, которое показывает, является ли база данных «шаблоном» (template).
    
    - В PostgreSQL по умолчанию есть `template0` и `template1`.
        
    - Обычно при создании новой БД она копируется с `template1`.


## Просмотр информации о таблицах

```sql
SELECT column_name, data_type, is_nullable, column_default
FROM information_schema.columns
WHERE table_name = 'users';
```


----
#### [[viewing DBs - PostgreSQL - Flashcards|Link to flashcards]]



---
### References:

