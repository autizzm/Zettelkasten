
14-09-2025 09:47

Status: #child 

Tags: [[SQL]]

---
# SELECT - SQL

С помощью `SELECT` можно вывести не только данные из БД, но и любые строки, цифры, даты и т.д.:
```sql
SELECT 'Hello, World!';
```
Выведет в консоль:
```
Hello, World!
```



## Вывод данных таблиц

`*` - обозначает все столбцы таблицы

```sql
SELECT * FROM myTable; /*выведет все строки со всеми столбцами
```

```sql
SELECT member_id, member_name FROM FamilyMembers /* выведет только столбцы member_id, member_name всех строк таблицы */
```

**Output:**

| member_id | member_name |
| --- | --- |
| 1 | Headley Quincey |
|2|Flavia Quincey|
|3|Andie Quincey|
|4|Lela Quincey|
|5|Annie Quincey|
|6|Ernest Forrest|
|7|Constance Forrest|


### Оператор AS


Изменяет название столбца в представлении:
```sql
SELECT member_id, member_name AS Name FROM FamilyMembers
```

| member_id | ~={orange}Name=~  |
| --------- | ----------------- |
| 1         | Headley Quincey   |
| 2         | Flavia Quincey    |
| 3         | Andie Quincey     |
| 4         | Lela Quincey      |
| 5         | Annie Quincey     |
| 6         | Ernest Forrest    |
| 7         | Constance Forrest |



----
#### [[ |Link to flashcards]]



---
### References:

