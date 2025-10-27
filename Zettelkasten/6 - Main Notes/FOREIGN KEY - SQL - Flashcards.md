
Theory for the cards: 

FILE TAGS: sql

Q: Что произойдёт если удалить запись о Компании, на которую ссылается два пользователя?
```sql
CREATE TABLE Users (
    id INTEGER,
    name VARCHAR(255) NOT NULL,
    age INTEGER NOT NULL DEFAULT 18,
    company INTEGER,
    PRIMARY KEY (id),
    FOREIGN KEY (company) REFERENCES Companies (id)
);
```
A: Компания не будет удалена, т.к. по умолчанию используется `ON DELETE RESTRICT`, который запрещает удаление записи, если на нёё ссылается хоть одна запись из других таблиц.
<!--ID: 1761581631036-->
