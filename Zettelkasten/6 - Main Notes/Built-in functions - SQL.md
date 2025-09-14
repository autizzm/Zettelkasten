
14-09-2025 11:30

Status: #child 

Tags: [[SQL]]

---
# Built-in functions - SQL

У Postgres и MySQL встроенные функции весьма разные.

Вот краткий список тех, что совпадают.

- **Арифметические и математические**: `ABS()`, `ROUND()`, `CEIL()`, `FLOOR()`, `POWER()`, 
    
- **Строковые**: `LENGTH()`, `SUBSTRING()`, `TRIM()`, `LOWER()`, `UPPER()`, `CONCAT()`, 
    
- **Дата и время**: `NOW()`, (Postgres - `CURRENT_DATE`, MySQL - `CURDATE()`)
    
- **Агрегатные**: `SUM()`, `AVG()`, `COUNT()`, `MAX()`, `MIN()`


## Примеры для MySQL
#### **LOWER**

Returns a string in which all characters are written in lowercase

```sql
SELECT LOWER('SQL Academy') AS lower_string;
```

|lower_string|
|---|
|sql academy|

---
#### **EXTRACT**

Extracts date parts (year, month, day, etc.) from a given date

```sql
EXTRACT(field FROM source)
```
- **field** — часть даты (YEAR, MONTH, DAY и т.д.),

- **source** — это выражение с типом даты/времени (DATE, TIMESTAMP, TIMESTAMPTZ, INTERVAL).


Пример:
```sql
SELECT EXTRACT(YEAR FROM DATE '2022-06-16') AS year;
```

|year|
|---|
|2022|

~={red}!!!!=~ ~={yellow}Здесь `DATE '2022-06-16` - это приведение литерала к типу `DATE`=~ ~={red}!!!!=~ 

---
#### **POSITION**

Searches for a substring in a string, returning the position of its first character. At the same time, the countdown starts with one, not zero, as in most programming languages.

~={red}!!!!=~ ~={yellow}индексация начинается с 1=~ ~={red}!!!!=~

```sql
SELECT POSITION('academy' IN 'sql-academy') AS idx;
```

|idx|
|---|
|5|

---
#### **LENGTH**

Returns the length of the specified string.


```sql
SELECT LENGTH('sql-academy') AS str_length;
```

|str_length|
|---|
|11|

----
#### [[Built-in functions - SQL - Flashcards|Link to flashcards]]



---
### References:

