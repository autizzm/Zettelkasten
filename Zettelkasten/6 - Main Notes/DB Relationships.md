
15-09-2025 20:32

Status: #baby - no flashcards yet

Tags: [[SQL]]

---
# DB Relationships

### Изображение на диаграмме:

![[Pasted image 20250915203323.png]]


## One to One relationship

Пример:

![[Pasted image 20250915203533.png]]

~={orange}Заметь=~, на диаграмме отношение показано как прямая линия, так как обе стороны явля.тся "One Sides"

Пример:
```sql
CREATE TABLE Student (
    id SERIAL PRIMARY KEY, --parent (the One)
    name TEXT NOT NULL,
    class INT NOT NULL
);

CREATE TABLE Student_id_cards (
    registration_number INT PRIMARY KEY, --it's also one, but it;s not pbligatory
    start_date DATE NOT NULL, -- it's just cause we want One To One
    end_date DATE NOT NULL,
    student_id REFERENCES Students(id) --child
);
```
🔑 Здесь `user_id` в `Passports` помечен как `UNIQUE` (т.к. он  [[Creating and deleting tables - SQL#Additional column definition options|SERIAL]]), поэтому один пользователь не может иметь два паспорта.

---
## One to Many relationship

![[Pasted image 20250915203923.png]]


Пример:
```sql
CREATE TABLE Students (
    id SERIAL PRIMARY KEY,
    name TEXT NOT NULL,
    duty_id INT REFERENCES Duties(id) --child
);

CREATE TABLE Duties (
    id SERIAL PRIMARY KEY, --parent
    name TEXT NOT NULL,
    number_of_stuents INT
);
```
🔑 Здесь на каждую Duty может ссылаться много студентов.

---
## Many to Many relationship

![[Pasted image 20250915204122.png]]


Чтобы упростить запросы используется связывающая таблица, которая превращет отношение в два One To Many отношения:

![[Pasted image 20250915213759.png]]


Пример:
```sql
CREATE TABLE Students (
    student_id SERIAL PRIMARY KEY, -- parent of One to Many with StudentActivities 
    name TEXT NOT NULL,
    class INT NOT NULL
);

CREATE TABLE Activities (
    id SERIAL PRIMARY KEY, -- parent of One to Many with StudentActivities table
    name TEXT NOT NULL,
    number_of_students INT DEFAULT 0
);

-- связывающая таблица
CREATE TABLE StudentActivities (
    student_id INT REFERENCES Students(student_id), -- child of One to Many with Students table
    course_id INT REFERENCES Activities(id), -- child of One to Many with Activities table
    PRIMARY KEY (student_id, id)
);
```
🔑 Таблица `StudentActivities` — это «связка», которая реализует отношение многие-ко-многим.


> [!note] **About FOREIGN KEY**
> Он указывается в Child таблице отношения - той, чьи записи (в количестве > 1) будут относится к одной записи parent таблицы



----
#### [[DB Relationships - Flashcards|Link to flashcards]]



---
### References:

