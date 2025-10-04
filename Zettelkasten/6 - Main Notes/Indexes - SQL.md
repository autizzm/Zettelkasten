
17-09-2025 13:09

Status: #child

Tags: [[SQL]]

---
# Indexes - SQL


У обычных таблиц нет порядка хранения дынных. Информация сохраняется в любое свободное место в файле.

Индексы хранятся в отдельной таблице, которая имеет определённый порядок.
В этой таблице хранится только столбец (или столбцы) используемые для поиска строк в таблице данных, вместе с информацией о том, где физически в файле находится строка.

> [!note] **Primary key is already indexed**
> Для PRIMARY KEY автоматически создаётся специальный индекс, который контролирует, чтобы каждое значение в колонке (или колонках), которые заданы как PRIMARY KEY, было уникальным и не было NULL. 


### [Как оптимизировать индексы?](https://sql-academy.org/en/guide/indexes#how-indexes-are-used)


### Добавление индексов в существующую таблицу

```sql
CREATE INDEX idx_email
    ON My_table (email_column);
```

#### [[Queries to system DBs - PostgreSQL#How to view existing indexes|How to view existing indexes in PostgreSQL]]


### Удаление индексов

```sql
DROP INDEX idx_email;
```

## Unique Indexes

~={red}!!!=~ ~={yellow}Добавлять UNIQUE при создании PRIMARY KEY - не нужно, он и так уникален=~ ~={red}!!!=~

```sql
CREATE UNIQUE INDEX idx_email
    ON Users (email);
```

Можно добавлять сколько угодно индексов в таблицу. Но большое количество повлияет на производительность.



## Multi-Column Indexes

Можно создавать индексы на несколько колонок. Например, для поиска по last_name и first_name учеников, можно сделать такой композитный индекс:

```sql
CREATE INDEX idx_full_name
    ON Student (last_name, first_name);
```

~={red}!!!=~ ~={yellow}Однако, такой индекс улучшит производительность только тех запросов, где указаны и ~={cyan}first_name=~ и ~={cyan}last_name=~. Запросы, где указан только ~={cyan}first_name=~ или ~={cyan}last_name=~ - он не улучшит=~ ~={red}!!!=~

~={red}!!!=~ ~={yellow}Порядок тоже имеет значение=~ ~={red}!!!=~ 
В нашем случае такой индекс улучшит скорость поиска только если указаны ~={cyan}last_name=~, ~={cyan}first_name=~ в том порядке, в котором они указаны в индексе.

Можно сделать несколько индексов и указать в них те же колонки, но в ругом порядке.

----
#### [[Indexes - SQL - Flashcards|Link to flashcards]]



---
### References:

