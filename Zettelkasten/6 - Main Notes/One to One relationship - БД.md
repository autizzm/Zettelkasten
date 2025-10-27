
26-10-2025 20:49

Status: #child

Tags: [[Базы данных]]

---
# One to One relationship - БД


![[Pasted image 20251026204702.png]]

![[Pasted image 20250915203533.png]]

![[Pasted image 20251026210206.png]]

~={orange}Заметь=~, на диаграмме отношение показано как прямая линия, так как обе стороны являются "One Sides"

Пример:
```sql
CREATE TABLE Student (
    id SERIAL PRIMARY KEY, --parent (the One)
    name TEXT NOT NULL,
    class INT NOT NULL
);

CREATE TABLE Student_id_cards (
    registration_number INT PRIMARY KEY,
    start_date DATE NOT NULL, 
    end_date DATE NOT NULL,
    student_id REFERENCES Students(id) UNIQUE --child
);
```

> Вообще, при отношении One To One нет смысла делать PRIMARY KEY в обоих таблицах. 
> 
> Например здесь можно было бы в качестве id использовать registration_number студенческого билета. 
> 
> Но если он является конфиденциальным и швырять его по всему приложению - не варик    -> создаём искусственный `id` как PRIMARY KEY и делаем через FOREIGN KEY связь таблиц. 

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

----
#### [[One to One relationship - БД - Flashcards|Link to flashcards]]



---
### References:

- [[DB Relationships]]
- [[One to Many relationship - БД]]
- [[Many to Many relationship - БД]]