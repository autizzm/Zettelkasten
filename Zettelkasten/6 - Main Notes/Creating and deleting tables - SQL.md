
15-09-2025 19:35

Status: #baby - no flashcards yet

Tags: [[SQL]]

---
# Creating and deleting tables

## Creating a table

Синтаксис:
```sql
CREATE TABLE [IF NOT EXISTS] table_name (
     column_1 data type,
    [column_2 data type,]
    ...
    [column_n data type,]
);
```


> [!note] 
> Если имя таблицы clashes with some Postgres keywords -> используем двойные кавычки:
> ```sql
> CREATE TABLE "Order" (
> ...
> )
> ```
> (слово `Order` - является ключевым в Postgres)

## [[Column definition options (Constraints) - SQL]]

----
#### [[Creating and deleting tables - Flashcards|Link to flashcards]]



---
### References:

