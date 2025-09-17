
17-09-2025 13:46

Status: #baby 

Tags: [[SQL]]

---
# Constraints -SQL


### Main types of SQL constraints:

1. **PRIMARY KEY** 
2. **FOREIGN KEY** 
3. **UNIQUE** 
4. **NOT NULL** 
5. **CHECK** — verifies that data meets a specified condition
6. **DEFAULT** 


### PRIMARY KEY 

Колонка или несколько колонок, имеющая UNIQUE значение в каждой строке и при этом NOT NULL.

```sql
CREATE TABLE Users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(50),
    email VARCHAR(100)
);

-- Альтернативный способ:
CREATE TABLE Users (
    id SERIAL,
    username VARCHAR(50),
    email VARCHAR(100),
    CONSTRAINT pk_users PRIMARY KEY (id)
);
```


### FOREIGN KEY

Одна или несколько колонок таблицы, которые ссылаются на PRIMARY KEY другой таблицы

----
#### [[Constraints -SQL - Flashcards|Link to flashcards]]



---
### References:

