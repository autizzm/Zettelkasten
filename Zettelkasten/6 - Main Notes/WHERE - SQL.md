
14-09-2025 16:00

Status: #child

Tags: [[SQL]]

---
# WHERE - SQL

Условный оператор.

#### Операторы сравнения:
- `=`
- `<>, !=` - операторы неравенства
- `<, >`
- `<=, >=`

> [!note] **Сравнение с NULL**
> ~={red}!!!!=~ ~={yellow}Любое сравнение с `NULL` даёт результат **`UNKNOWN`**, а не `TRUE` и не `FALSE`.=~ ~={red}!!!!=~
> 
> SQL использует **трёхзначную логику**: `TRUE`, `FALSE`, `UNKNOWN`.  
В `WHERE` остаются только строки, где условие = `TRUE`.
>
>- `FALSE` → строка отбрасывается
  >  
>- `UNKNOWN` → строка тоже отбрасывается
  >  
>- `TRUE` → строка остаётся

## Правильная проверка на `NULL`

Используется `IS NULL` и `IS NOT NULL`:

`SELECT * FROM people WHERE age IS NULL;     -- работает SELECT * FROM people WHERE age IS NOT NULL; -- работает`


----
#### [[WHERE - SQL - Flashcards|Link to flashcards]]



---
### References:

- [[Basic Logical Operators - SQL]]