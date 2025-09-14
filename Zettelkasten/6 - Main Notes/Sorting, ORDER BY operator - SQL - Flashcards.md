
Theory for the cards: [[Sorting, ORDER BY operator - SQL]]

FILE TAGS: sql

Q: Как отсортировать представление таблицы через sql запрос? 
A:  
```sql
SELECT table_fields FROM table_name
WHERE ...
ORDER BY column_1 [ASC | DESC][, column_n [ASC | DESC]]
```
<!--ID: 1757873430970-->


Q: В каком порядке здесь произойдет сортировка?
```sql
SELECT DISTINCT town_from, town_to FROM Trip
ORDER BY town_from;
```
A:  В порядке возрастания, т.к. если направление не указано явно - сортируется по возрастанию (`ASC`)
<!--ID: 1757873430981-->


Q: В каком порядке здесь произойдет сортировка (`has_townhall` - типа BOOLEAN)?
```sql
SELECT DISTINCT town_name, population FROM Trip
ORDER BY has_townhall;
```
A: Здесь сортировка по умолчанию -> `ASC` => Сначала будут ображены строки, где `has_townhall = FALSE` (т.к. обычно TRUE представляется как 1, а FALSE как 0 => 0 < 1)
<!--ID: 1757873430987-->


Q: В каком порядке здесь произойдет сортировка (`landmark` - текстового типа и может быть NULL)?
```sql
SELECT DISTINCT town_name, population FROM Trip
ORDER BY landmark;
```
A: Здесь сортировка по умолчанию -> `ASC` => Сначала будут ображены строки, где есть значение, а в конце те, где `landmark IS NULL` (Логики нет)
<!--ID: 1757873430992-->


Q: Как отсортируется эта таблица?
```sql
SELECT DISTINCT town_from, town_to FROM Trip
ORDER BY town_from, town_to DESC;
```
	
| town_from | town_to     |
| --------- | ----------- |
| London    | Singapore   |
| Moscow    | Rostov      |
| Rostov    | Vladivostok |
| London    | Paris       |
| Paris     | Rostov      |
| Rostov    | Paris       |


A: Сначала представление будет отсортировано по столбцу `town_from` по умолчанию, т.е. по возрастания (ASC):
	
| town_from | town_to     |
| --------- | ----------- |
| London    | Singapore   |
| London    | Paris       |
| Moscow    | Rostov      |
| Paris     | Rostov      |
| Rostov    | Vladivostok |
| Rostov    | Paris       |
2.  После этого произойдет сортировка по второму столбцу по убыванию (`DESC`) ИСКЛЮЧИТЕЛЬНО тех групп строк, где совпадает значение в уже отсортированном столбце:
	
| town_from | town_to     |
| --------- | ----------- |
| London    | Paris       |
| London    | Singapore   |
| Moscow    | Rostov      |
| Paris     | Rostov      |
| Rostov    | Paris       |
| Rostov    | Vladivostok |
<!--ID: 1757873925114-->

<!--ID: 1757873883590-->

