
17-09-2025 13:51

Status: #baby 

Tags: [[SQL]]

---
# PRIMARY KEY - SQL

Колонка или несколько колонок, имеющая ~={orange}UNIQUE=~ значение в каждой строке и при этом ~={orange}NOT NULL=~.

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

-- ещеё способ
CREATE TABLE Users (
    id INTEGER,
    name VARCHAR(255) NOT NULL,
    age INTEGER NOT NULL DEFAULT 18,
    PRIMARY KEY (id)
);
```



----
#### [[PRIMARY KEY - SQL - Flashcards|Link to flashcards]]



---
### References:

