
15-09-2025 15:00

Status: #child 

Tags: [[SQL]]

---
# Grouping, GROUP BY operator

Группирует строки по одному полю.

~={red}!!!=~ ~={yellow}Считает все значения NULL равными -> значения NULL будут сгруппированы вместе=~ ~={red}!!!=~

Т.к. теперь каждая запись в полученном представлении относится ко всей группе, то мы не можем просто вывести остальные поля так, как это сделали бы без GROUP BY.

#### При использовании GROUP BY мы можем вывести только:

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


### Группировка по одному полю

![[Pasted image 20250915151347.png]]

Группируются строки с одинаковым значением поля по которму группируем.


### Группировка по двум и более полям

![[Pasted image 20250915151200.png]]

Т.е. теперь в группы собираются строки с одинаковой комбинацией значений этих двух полей.

Код запроса:
```sql
SELECT home_type, has_tv, AVG(price) AS avg_price FROM  Rooms
GROUP BY  home_type, has_tv;
```

----
#### [[Grouping, GROUP BY operator - Flashcards|Link to flashcards]]



---
### References:

