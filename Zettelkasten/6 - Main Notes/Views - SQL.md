
17-09-2025 12:47

Status: #child 

Tags: [[SQL]]

---
# Views - SQL

View (представление) - это "виртуальные таблицы" для просмотра информации из БД. Они конструируются с помощью SELECT запросов. И при обновлении информации в БД, она обновляется также и в View.

#### Для чего нужны

This can be useful, for example, if your company's policy does not allow everyone to use confidential user information. Therefore, instead of allowing direct access to the user table (Users), you define a view named ViewUsers and require everyone to use it to access user data.


### Синтаксис


#### General syntax:
```sql
CREATE [OR REPLACE] VIEW view_name [(view_column_names)]
AS select_expression
```

~={cyan}OR REPLACE=~ - при использовании этого параметра, ~={orange}если=~ View уже существует, он~={orange} будет удалён и будет создан новый=~.  Иначе, ~={green}без=~ использования ~={cyan}OR REPLACE=~, ~={green}если=~ View с таким именем уже ~={green}существует=~ - ~={green}будет ошибка=~.

#### Пример:
```sql
CREATE VIEW ViewUsers AS
    SELECT id,
           name,
           CONCAT(SUBSTR(email, 1, 2), '****', RIGHT(email, 4)) AS email
FROM Users;
```

----
#### [[Views - SQL - Flashcards|Link to flashcards]]



---
### References:

