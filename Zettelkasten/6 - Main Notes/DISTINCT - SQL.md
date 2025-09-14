
14-09-2025 12:12

Status:

Tags:

---
# DISTINCT - SQL

Выводит только уникальные значения.

```sql
SELECT DISTINCT class FROM Student_in_class;
```

### DISTINCT for multiple fields


Исходная таблица:

| first_name     | last_name       |
| -------------- | --------------- |
| ~={cyan}John=~ | ~={cyan}Scott=~ |
| William        | Dawson          |
| Raul           | Hartman         |
| William        | Hartman         |
| ~={cyan}John=~ | ~={cyan}Scott=~ |
| John           | Hartman         |

запрос с DISTINCT оператором вернет все комбинации за исключением строки, где ~={orange}оба поля повторяются=~, т.е. "John Scott". Её выведет только один раз.


```sql
SELECT DISTINCT first_name, last_name FROM User;
```

Результат:

| first_name     | last_name       |
| -------------- | --------------- |
| ~={cyan}John=~ | ~={cyan}Scott=~ |
| William        | Dawson          |
| Raul           | Hartman         |
| William        | Hartman         |
| John           | Hartman         |

----
#### [[DISTINCT - SQL - Flashcards|Link to flashcards]]



---
### References:

