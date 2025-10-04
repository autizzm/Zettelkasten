
04-10-2025 19:54

Status: #child 

Tags: [[SQL]]

---
# DELETE - SQL


```sql
DELETE FROM table_name
[WHERE the_conditions_of_the_limitations];
```

~={red}!!!=~ ~={yellow}Без `WHERE` - будут удалены все данные в таблиц=~е ~={red}!!!=~


### `TRUNCATE`

> [!note] **Если надо быстро удалить ВСЕ данные из таблицы**
> Можно использовать операцию `TRUNCATE`:
> 
> ```sql
> TRUNCATE TABLE table_name;
> ```
> Это быстрее, чем удаление всех записей по порядку (так делает `DELETE`)
> 
> `TRUNCATE` - дропает таблицу и создаёт заново


~={red}!!!=~ НО `TRUNCATE` - не вызывает триггеры удаления ~={red}!!!=~


## Multi-table `DELETE` queries

В `DELETE` можно ~={orange}только проверять условия=~ из других таблиц:

```sql
DELETE FROM Reservations
USING Rooms
WHERE Reservations.room_id = Rooms.id
AND Rooms.has_kitchen = false;
```

Для данных из двух таблиц одновременно, придётся писать два запроса:

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


----
#### [[DELETE - SQL - Flashcards|Link to flashcards]]



---
### References:

