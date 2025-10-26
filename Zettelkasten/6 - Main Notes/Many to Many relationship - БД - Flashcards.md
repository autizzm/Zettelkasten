
Theory for the cards: [[Many to Many relationship - БД]] 

FILE TAGS: databases

Q: Как сделать так, чтобы к каждому Activity могло относиться несколько студентов, и к каждый студент мог относится к нескольким Activities?
```sql
CREATE TABLE Students (
    student_id SERIAL PRIMARY KEY,  
    name TEXT NOT NULL,
    class INT NOT NULL
);
	
CREATE TABLE Activities (
    id SERIAL PRIMARY KEY, 
    name TEXT NOT NULL,
    number_of_students INT DEFAULT 0
);
```

A: Достигается связью Many To Many для Students и Activities. Реализуется через связывающую таблицу:
```sql
CREATE TABLE Students (
    student_id SERIAL PRIMARY KEY, 
    name TEXT NOT NULL,
    class INT NOT NULL
);
	
CREATE TABLE Activities (
    id SERIAL PRIMARY KEY, 
    name TEXT NOT NULL,
    number_of_students INT DEFAULT 0
);
	
CREATE TABLE Activities_Students(
	activity_id REFERENCES Activities(id),
	student_id REFERENCES Students(student_id),
	PRIMARY KEY (student_id, id)
);
```
	
> [!warning] 
> В связывающей таблице две колонки формируют составной PRIMARY KEY, чтобы, например Activity 2 не относилась к одному и тому же студенту несколько раз -> (2,2) - не должны повторяться.
<!--ID: 1761503739373-->
