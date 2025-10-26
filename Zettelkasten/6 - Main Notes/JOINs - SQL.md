
26-10-2025 21:42

Status: #adult

Tags: [[SQL]]

---
# JOINs - SQL

### INNER JOIN

В запросе можно опустить INNER - по умолчанию используется INNER JOIN:

```sql
SELECT * FROM Person [INNER] JOIN Order ON Person.user_id = Order.user_id;
```

![[Pasted image 20251026214643.png]]

Т.к запись {4, Nick, 30} - не связана ни скакой запись в Order -> она не отображается в результатах JOIN:

![[Pasted image 20251026215254.png]]

---
### OUTER JOINs

В запросе можно опустить OUTER - по умолчанию используется INNER JOIN:

```sql
SELECT * FROM Person LEFT [OUTER] JOIN Order ON Person.user_id = Order.user_id;
```

#### LEFT (OUTER) JOIN 

![[Pasted image 20251026215553.png]]

![[Pasted image 20251026215657.png]]

ОТображает пересечение таблиц и все значения из ЛЕВОЙ таблицы, даже если они не связаны со значениями правой таблицы.

#### RIGHT (OUTER) JOIN 

Делает то же самое, что и LEFT JOIN, но для правой таблицы. Бессмысленно -> используем всегда LEFT JOIN.

![[Pasted image 20251026220004.png]]

---
### CROSS JOIN

Каждая строка из таблицы $A$ ставится в пару с каждой строкой из таблицы $B$.

![[Pasted image 20251026220250.png]]

Малополезная хуйня.

Фактически делает полность денормализованную таблицу.

----
#### [[JOINs - SQL - Flashcards|Link to flashcards]]



---
### References:

