
Theory for the cards:  [[Basic Logical Operators - SQL]]

FILE TAGS: sql



Q: В каком порядке здесь будет выполнена проверка? Что выведется в представление? 
	
```sql
SELECT *
FROM Trip
WHERE town_to = 'Paris'
	OR plane = 'Boeing'
	AND NOT town_from = 'Moscow';
```
	
|id|company|plane|town_from|town_to|time_out|time_in|
|---|---|---|---|---|---|---|
|1100|4|Boeing|Rostov|Paris|1900-01-01T14:30:00.000Z|1900-01-01T17:50:00.000Z|
|1101|4|Boeing|Paris|Rostov|1900-01-01T08:12:00.000Z|1900-01-01T11:45:00.000Z|
|7771|5|Boeing|London|Singapore|1900-01-01T01:00:00.000Z|1900-01-01T11:00:00.000Z|
|7772|5|Boeing|Singapore|London|1900-01-01T12:00:00.000Z|1900-01-02T02:00:00.000Z|
|7773|5|Boeing|London|Singapore|1900-01-01T03:00:00.000Z|1900-01-01T13:00:00.000Z|
|7774|5|Boeing|Singapore|London|1900-01-01T14:00:00.000Z|1900-01-02T06:00:00.000Z|
|7775|5|Boeing|London|Singapore|1900-01-01T09:00:00.000Z|1900-01-01T20:00:00.000Z|
|7776|5|Boeing|Singapore|London|1900-01-01T18:00:00.000Z|1900-01-02T08:00:00.000Z|
|7777|5|Boeing|London|Singapore|1900-01-01T18:00:00.000Z|1900-01-02T06:00:00.000Z|
|7778|5|Boeing|Singapore|London|1900-01-01T22:00:00.000Z|1900-01-02T12:00:00.000Z|
|8881|5|Boeing|London|Paris|1900-01-01T03:00:00.000Z|1900-01-01T04:00:00.000Z|
|8882|5|Boeing|Paris|London|1900-01-01T22:00:00.000Z|1900-01-01T23:00:00.000Z|
A: 1. Сначала проверит, что рейс НЕ из Москвы.
2. Затем проверяет, что самолёт = `'Boeing'`
3. комбинирует результат 2-го пункта с результатом 1-го пункта (проверяет `plane = 'Boeing' AND NOT town_from = 'Moscow'`)
4. после этого все строки, где `town_to = 'Paris'` добавляются в представление.
Как результат, мы получим следующую выборку:
- все рейсы НЕ из Москвы и на Boeing
- Плюс все рейсы в Париж
<!--ID: 1757857985516-->



Q: Каков порядок выполнения логических операций в sql?
A: 1. NOT
2. AND
3. XOR
4. OR
<!--ID: 1757857972783-->



Q: Есть ли в Postgres операция XOR и что она делает?
A: Нет, это операция MySQL. Она выозвращает True если выполняется только одно из двух условий:
```sql
... WHERE expr1 XOR expr2
/* TRUE, если (expr1 = FALSE и expr2 = TRUE) или (expr1 = TRUE и expr2 = FALSE)
FALSE, ЕСЛИ (expr1 = TRUE и expr2 = TRUE) или (expr1 = FALSE и expr2 = FALSE)
```
<!--ID: 1757857972790-->