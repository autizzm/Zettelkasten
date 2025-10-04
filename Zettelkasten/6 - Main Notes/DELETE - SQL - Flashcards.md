
Theory for the cards:  [[DELETE - SQL]]

FILE TAGS: sql

Q: Как в sql полностью очистить таблицу? (Удалить все строки)
A: Можно с помощью `DELETE` без условий:
```sql
DELETE FROM table_name;
```
	
Можно с помощью `TRUNCATE`:
```sql
TRUNCATE TABLE table_name;
```
- это быстрее:
	- `DELETE` -  удаляет все строки по порядку
	- `TRUNCATE` - удаляет всю таблицу и создаёт заново
- НО при использовании `TRUNCATE` не вызываются триггеры удаления.
<!--ID: 1759598735394-->


Q: Можно ли одним sql запросом удалять строки из нескольких таблиц?
A: Нет, одним запросом - нельзя. Но можно несколькими запросами в одной транзакции:
```sql
BEGIN;
DELETE FROM Reservations
USING Rooms
WHERE Reservations.room_id = Rooms.id
AND Rooms.has_kitchen = false;
	
	
DELETE FROM Rooms
WHERE Rooms.has_kitchen = false;
COMMIT;
```
<!--ID: 1759598789908-->




Q: Как удалить только те строки из таблицы Reservations, которые связаны с теми комнатами (записями из т. Rooms), где есть кухня (Rooms.has_kitchen = false)
A: В sql нельзя удалять из двух таблиц одноременно, но проверять связанные записи, при удалении из одной таблицы - МОЖНО.
	
Для этого используем `USING` (аналог `JOIN`) + проверяем, чтобы id-шник совпадал:
```sql
DELETE FROM Reservations
USING Rooms
WHERE Reservations.room_id = Rooms.id
AND Rooms.has_kitchen = false;
```
<!--ID: 1759598735426-->
