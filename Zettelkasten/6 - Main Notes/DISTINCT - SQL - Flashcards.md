
Theory for the cards: 

FILE TAGS: sql

Q: Что делает оператор DISTINCT?
A: Он убирает строки с повторяющимися значениями. Только те строки, где все выводимые значения совпадают.
<!--ID: 1757841718409-->


Q: Уберет ли оператор DISTINCT строку John Scott?
```sql
SELECT DISTINCT first_name, last_name FROM User;
```
	
| first_name | last_name | Age |
| ---------- | --------- | --- |
| John       | Scott     | 18  |
| William    | Dawson    | 34  |
| Raul       | Hartman   | 52  |
| William    | Hartman   | 32  |
| John       | Scott     | 61  |
| John       | Hartman   | 34  |
A: Да, т.к. мы выводим только first_name и last_name, поэтому эти строки в представлении будут иеть все одинаковые поля => DISTINCT удалит одну из них.
<!--ID: 1757841718420-->
