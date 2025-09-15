
15-09-2025 19:00

Status: #child

Tags: [[SQL]]

---
# Creating and deleting databases - SQL


## DB creation

```sql
CREATE DATABASE database_name;
```


Как проконтролировать, создалась ли БД? [[viewing DBs - PostgreSQL]]


## DB deletion

```sql
DROP DATABASE database_name;
```



## `IF [NOT] EXISTS` construction

При попытке удалить (или создать) БД, если она уже была удалена (создана) -> возникнет ошибка.

```sql
CREATE DATABASE IF NOT EXISTS database_name; --создаем БД, только, если имя database_name ещё не занято
DROP DATABASE IF EXISTS database_name; --удаляем БД, только если есть БД с таким именем
```

----
#### [[Creating and deleting databases - SQL - Flashcards|Link to flashcards]]



---
### References:

