
14-09-2025 16:57

Status: #child

Tags: [[SQL]]

---
# Basic Logical Operators - SQL

## Logical operators

- ~={cyan}AND=~
	
- ~={cyan}OR=~
	
- ~={cyan}NOT=~ - превращает результат в противоположный
	
- ~={cyan}XOR=~ - выбирает записи, где ТОЛЬКО ОДНО условие выполняется. ~={red}!=~ ~={yellow}Только для MySQL.=~ ~={red}!=~
Пример:

|Departs  <br>from Moscow|Arrives  <br>in Paris|Included  <br>in result|Explanation|
|:-:|:-:|:-:|---|
|Yes|No|✅ Yes|Only the first condition is met|
|No|Yes|✅ Yes|Only the second condition is met|
|Yes|Yes|❌ No|Both conditions are met — XOR excludes these|
|No|No|❌ No|Neither condition is met|

Для Postgres (заменитель XOR):
```sql
SELECT * FROM trip
WHERE (town_from = 'Moscow' AND town_to != 'Paris')
   OR (town_from != 'Moscow' AND town_to = 'Paris');
```


## Приоритет выполнения логических операторов

1. NOT
2. AND
3. XOR
4. OR

Для изменения порядка выполнения логических операций можно использовать скобки.
Операции в скобках выполняются в первую очередь:
PostgreSQL 17.5
```sql
SELECT *
FROM Trip
WHERE (
		town_to = 'Paris'
		OR (
			plane = 'Boeing'
			AND (NOT town_from = 'Moscow')
		)
	); /*тут порядок не изменится от скобок, он будет выполняться в том же порядке, что выполнялось бы без скобок */
```

----
#### [[Basic Logical Operators - SQL - Flashcards|Link to flashcards]]



---
### References:

