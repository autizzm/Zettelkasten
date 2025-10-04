
Theory for the cards: [[DB Relationships]]

FILE TAGS: sql

Q: Какое это отношение? Каая сущность является Parent, а какая Child?
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
A: 
