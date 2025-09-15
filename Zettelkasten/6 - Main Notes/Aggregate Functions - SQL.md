
15-09-2025 15:21

Status: #baby 

Tags:  [[SQL]]

---
# Aggregate Functions - SQL

Агрегатная функция - это функция, которая производит~={cyan} расчет на множестве=~ значений и ~={cyan}возвращают единственное значение=~.

Используются в комбинации с GROUP BY:
```sql
SELECT [literals, aggregate_functions, grouping_fields]
FROM table_name
GROUP BY grouping_fields;
```


## Description of aggregate functions

| Function                              . | Description                   |
| :-------------------------------------- | :---------------------------- |
| ~={cyan}SUM(table_field)=~              | Returns the sum of the values |
| ~={cyan}AVG(table_field)=~              | Returns the average value     |
| ~={cyan}COUNT(table_field)=~            | Returns the number of records |
| ~={cyan}MIN(table_field)=~              | Returns the minimum value     |
| ~={cyan}MAX(table_field)=~              | Returns the maximum value     |

> [!note] 
> Агрегатные функции применяются только к значениям которые `IS NOT NULL`.
> 
>Исключение - ~={orange}COUNT(*)=~


----
#### [[Aggregate Functions - SQL - Flashcards|Link to flashcards]]



---
### References:

