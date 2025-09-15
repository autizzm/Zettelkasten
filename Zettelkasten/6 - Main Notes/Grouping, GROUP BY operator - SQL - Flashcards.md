
Theory for the cards: [[Grouping, GROUP BY operator - SQL]]

FILE TAGS: sql


Q: Как происходит группировка строк по двум полям? Чем она отличается от группировки по одному полю?
A:   
Группировка по одному полю:
![[Pasted image 20250915151347.png]]
	
Группируются строки с одинаковым значением поля по которму группируем.
	
Группировка по двум полям:
![[Pasted image 20250915151200.png]]
	
Т.е. теперь в группы собираются строки с одинаковой комбинацией значений этих двух полей.
<!--ID: 1757943720434-->


Q: Как сгруппировать строки представления по одному полю с помощью sql запроса?
A: С помощью оператора GROUP BY:
```sql
SELECT home_type FROM Rooms
GROUP BY home_type
```
<!--ID: 1757943644308-->


Q: Что здесь не так?
```sql
SELECT price, home_type, has_tv
FROM Rooms
GROUP BY home_type;
```
A: Мы пытаемся вывести поля, по которым мы не группируем. Они уникальны для каждой строки, а мы пытаемся вывести их для групп -> получим ошибку.
В запросе с группировкой можгно выводить только:
```sql
SELECT [literals, aggregate_functions, grouping_fields]
FROM table_name
GROUP BY grouping_fields;
```
<!--ID: 1757943644331-->


Q: Что можно вывести в sql запросе на группировку?
A:   
```sql
SELECT [literals, aggregate_functions, grouping_fields]
FROM table_name
GROUP BY grouping_fields;
```
	
- литералы:
```sql
SELECT home_type, 'literal' FROM Rooms
GROUP BY home_type
```
	
|home_type|literal|
|---|---|
|Private room|literal|
|Entire home/apt|literal|
|Shared room|literal|
	
- Результаты агрегатных функций, т.е. ~={orange}значения=~, посчитанные~={orange} на основании нескольких значений полей этой группы=~:
```sql
SELECT home_type, AVG(price) as avg_price FROM Rooms
GROUP BY home_type
```
	
|home_type|avg_price|
|---|---|
|Private room|89.4286|
|Entire home/apt|148.6667|
|Shared room|40|
	
- Поле, по которму группируем
<!--ID: 1757943644339-->



