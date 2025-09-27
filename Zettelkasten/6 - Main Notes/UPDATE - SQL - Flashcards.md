
Theory for the cards: [[UPDATE - SQL]]

FILE TAGS: sql

Q: Как выглядит синтаксис UPDATE запроса в sql?
A:   
```sql
UPDATE table_name
SET table_field1 = table_field_value1,
    table_fieldN = table_field_valueN
[WHERE the_conditions_of_the_limitations]
```
	
ПРИ ПРОПУСКЕ `WHERE` - обновлены будут ВСЕ СТРОКИ
<!--ID: 1758970317598-->


Q: Как увеличить стоимость всех комнат на 10 одним sql запросом?
![[Pasted image 20250927135038.png]]
A:  В `UPDATE` запросах можно использовать текущие значения колонок таблицы:
```sql
UPDATE Rooms
SET price = price +10;
```
<!--ID: 1758970317611-->


