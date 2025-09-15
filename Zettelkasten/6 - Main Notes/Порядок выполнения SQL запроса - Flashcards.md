
Theory for the cards: [[Порядок выполнения SQL запроса]]

FILE TAGS: sql

Q: В каком порядке выполняются операторы в этом sql запросе?
```sql
SELECT constants, aggregate_functions, grouping_fields
FROM table_name
WHERE row_limit_conditions
GROUP BY grouping_fields
HAVING row_limit_conditions_after_grouping
ORDER BY sorting_condition
```
A: 1. FROM -> Сначала из соответствующей таблицы достаются строки
2. WHERE -> Строки фильтруются
3. GROUP BY -> отфильтрованные строки группируются
4. HAVING -> строки с информацией о группе фильтруются
5. ORDER BY -> отфильтрованные строки с информацией о группе сортируются
<!--ID: 1757946022721-->
