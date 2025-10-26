
26-10-2025 20:50

Status: #child

Tags: [[Базы данных]]

---
# One to Many relationship - БД

![[Pasted image 20251026203718.png]]

![[Pasted image 20250915203923.png]]

![[Pasted image 20251026203822.png]]


> [!note] 
> FOREIGN KEY - содержится в дочерней таблице..
> 
> В это таблице записи могут иметь ссылки на одни и те же записи Parent таблицы


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


----
#### [[One to Many relationship - БД - Flashcards|Link to flashcards]]



---
### References:

- [[DB Relationships]]
- [[One to One relationship - БД]]