
29-10-2025 10:06

Status: #baby

Tags: [[SQL]]

---
# updating tables -SQL

Общий синтаксис:
```sql
ALTER TABLE table_name 
[ADD [COLUMN column_name type [CONSTRAINTS]] | [CONSTRAINT constraint_name ...]] | 
[DROP COLUMN column_name] | 
[ALTER COLUMN column_name type SET [CONSTRAINTS]];
```

```sql
ALTER TABLE table_name
-- действия: ADD, DROP, ALTER COLUMN, RENAME COLUMN и др.
```

> [!note] **В Postgres немного другой синтаксис**
> Используем `TYPE` для установки нового типа:
> ```sql
> ALTER TABLE Person
>ALTER COLUMN name TYPE VARCHAR(100);
> ```

### Examples

```sql
ALTER TABLE Employees
ALTER COLUMN name SET NOT NULL;
-- или
ALTER COLUMN name DROP NOT NULL;
```

```SQL
ALTER TABLE Good ADD CONSTRAINT CK_Good_size CHECK (size BETWEEN 36 AND 46); 

ALTER TABLE Person
ADD CONSTRAINT unique_name UNIQUE (name);
```

```sql
ALTER TABLE table_name
RENAME COLUMN old_column_name TO new_column_name;
```

----
#### [[updating tables -SQL - Flashcards|Link to flashcards]]



---
### References:

