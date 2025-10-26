
Theory for the cards: [[One to Many relationship - БД]]

FILE TAGS: databases

Q: Какое это отношение? Какая сущность является Parent, а какая Child?
```sql
CREATE TABLE Students (
    id SERIAL PRIMARY KEY,
    name TEXT NOT NULL,
    duty_id INT REFERENCES Duties(id) 
);
	
CREATE TABLE Duties (
    id SERIAL PRIMARY KEY, 
    name TEXT NOT NULL,
    number_of_stuents INT
);
```
A: Таблица Duties - parent, т.к. содержит только PRIMARY KEYs. 
Таблица Students содержит FOREIGN KEY -> запись в Student может ссылаться на любую запись в Duties и ничто не мешает двум разным записям в таблице Student ссылаться на одну и ту же Duty. 
<!--ID: 1761501165822-->
