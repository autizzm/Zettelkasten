
17-09-2025 13:46

Status: #baby 

Tags: [[SQL]]

---
# Column definition options


### Main types of SQL constraints:

1. **PRIMARY KEY** 
2. **FOREIGN KEY** 
3. **UNIQUE** 
4. **NOT NULL** 
5. **CHECK** — verifies that data meets a specified condition
6. **DEFAULT** 



- [[PRIMARY KEY - SQL|PRIMARY KEY]]
    
    Устанавливает поле (~={orange}или несколько полей=~) как~={cyan} primary key=~
    
- [[FOREIGN KEY - SQL|FOREIGN KEY]] 
	
	Одна или несколько колонок таблицы, которые ссылаются на PRIMARY KEY другой таблицы
	
- ~={cyan}SERIAL =~or ~={cyan}GENERATED ALWAYS AS IDENTITY=~ 
    
    ~={cyan}SERIAL=~ - синтаксический сахар (это макрос):
    - Создаёт колонку `id integer NOT NULL`.
    
	- Создаёт последовательность (sequence) с именем вроде `users_id_seq`.
    
	- Назначает для `id` значение по умолчанию `nextval('users_id_seq'::regclass)`.
	
	Пример использования:
	```sql
	CREATE TABLE users (
	id SERIAL PRIMARY KEY,
	name text
	);
	```
	
	~={cyan}GENERATED ... AS IDENTITY=~ - более **современный SQL-стандартный** способ.  Под капотом это тоже использует sequence, но поведение более «чистое» и управляемое.
	
	Пример использования:
	```sql
	CREATE TABLE users (
    id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name text
	);
	```
	
- ~={cyan}UNIQUE=~
    
    Указывает, что значение этой колонки должно быть разным у всех строк таблицы
    
- ~={cyan}NOT NULL=~
    
    Указывает, что значение этой колонки не может быть NULL
    
- ~={cyan}DEFAULT=~
	
	Определяет default значение поля
	

```sql
CREATE TABLE Users (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    age INTEGER NOT NULL DEFAULT 18
);
```
- ~={cyan}CHECK=~
	
	Проверяет условие при вставке записи в таблицу.

	Пример:
	```sql
	CREATE TABLE Employees (
	    employee_id INT PRIMARY KEY,
	    first_name VARCHAR(50) NOT NULL,
	    last_name VARCHAR(50) NOT NULL,
	    birth_date DATE NOT NULL,
	    hire_date DATE NOT NULL,
	    CONSTRAINT chk_dates CHECK (hire_date > birth_date)
	);
```



## Adding and Removing Constraints

Constraints can be added not only when creating a table but also when modifying it:

```sql
-- Adding a PRIMARY KEY constraint
ALTER TABLE Users
ADD PRIMARY KEY (id);

-- Adding a FOREIGN KEY constraint
ALTER TABLE Orders
ADD CONSTRAINT fk_user FOREIGN KEY (user_id) REFERENCES Users(id);

-- Adding a UNIQUE constraint
ALTER TABLE Users
ADD CONSTRAINT uq_email UNIQUE (email);

-- Adding a CHECK constraint
ALTER TABLE Products
ADD CONSTRAINT chk_price CHECK (price > 0);

-- Adding a NOT NULL constraint
ALTER TABLE Users
ALTER COLUMN username SET NOT NULL;

-- Adding a default value
ALTER TABLE Orders
ALTER COLUMN status SET DEFAULT 'Pending';
```

Constraints can also be removed using the ALTER TABLE command:

```sql
-- Removing a PRIMARY KEY constraint
ALTER TABLE Users
DROP CONSTRAINT users_pkey;

-- Removing a FOREIGN KEY constraint
ALTER TABLE Orders
DROP CONSTRAINT fk_user;

-- Removing a UNIQUE constraint
ALTER TABLE Users
DROP CONSTRAINT uq_email;

-- Removing a CHECK constraint
ALTER TABLE Products
DROP CONSTRAINT chk_price;

-- Removing a NOT NULL constraint
ALTER TABLE Users
ALTER COLUMN username DROP NOT NULL;

-- Removing a default value
ALTER TABLE Orders
ALTER COLUMN status DROP DEFAULT;
```

----
#### [[Constraints -SQL - Flashcards|Link to flashcards]]



---
### References:

