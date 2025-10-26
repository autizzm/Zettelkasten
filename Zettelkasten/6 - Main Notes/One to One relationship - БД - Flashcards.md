
Theory for the cards: [[One to One relationship - БД]]

FILE TAGS: databases

Q: Какой это тип связи?
```sql
CREATE TABLE Student (
    id SERIAL PRIMARY KEY,
    name TEXT NOT NULL,
    class INT NOT NULL
);
	
CREATE TABLE Student_id_cards (
    registration_number INT PRIMARY KEY,
    start_date DATE NOT NULL, 
    end_date DATE NOT NULL,
    student_id REFERENCES Students(id) 
);
```
A: Это связь One to One, хотя, в теории, с такой реализацией студент может иметь несколько id карт. Такая реализация применяется в случае, если PRIMARY KEY таблицы с уникальными реальными (не искусственно созданными) данными является конфиденциальным и швырять его по всему приложению - не варик. (в нашем случае registration_number - конфиденциален).
	
Если бы не была нужна изоляция registration_number, мы могли бы его использовать вместо искусственно генерируемого id:
```sql
CREATE TABLE Student (
    card_registration_number REFERENCES Students_id_cards(registration_number) PRIMARY KEY --чтобы нельзя было создать две id карты, ссылающихся на одного человека 
    name TEXT NOT NULL,
    class INT NOT NULL
);
	
CREATE TABLE Student_id_cards (
    registration_number INT PRIMARY KEY,
    start_date DATE NOT NULL, 
    end_date DATE NOT NULL,
);
```
<!--ID: 1761502374175-->
