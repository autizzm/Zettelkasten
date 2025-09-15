
15-09-2025 19:35

Status: #baby - no flashcards yet

Tags: [[SQL]]

---
# Creating and deleting tables

## Creating a table

Синтаксис:
```sql
CREATE TABLE [IF NOT EXISTS] table_name (
     column_1 data type,
    [column_2 data type,]
    ...
    [column_n data type,]
);
```

## Additional column definition options

- ~={cyan}PRIMARY KEY=~
    
    Устанавливает поле (~={orange}или несколько полей=~) как~={cyan} primary key=~
    

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

### PRIMARY KEY

> [!note] **Задание PRIMARY KEY**
> Его можно задать как в строке с информацией о поле таблицы:
> ```sql
> CREATE TABLE Users (
  >  id SERIAL PRIMARY KEY
>    );
> ```
> 
> Так и с помощью `PRIMARY KEY (column_1, column_2)` в конце запроса:
> ```sql
>CREATE TABLE Users (
  >  id INTEGER,
>    name VARCHAR(255) NOT NULL,
 >   age INTEGER NOT NULL DEFAULT 18,
>    PRIMARY KEY (id)
>);
>```

### FOREIGN KEY

Синтаксис:
```sql
FOREIGN KEY (<column_1>, <column_n>)
REFERENCES <external_table> (<external_table_column_1>, <external_table_column_n>)
[ON DELETE reference_option]
[ON UPDATE reference_option]
```


Пример использования:
```sql
CREATE TABLE Users (
    id INTEGER,
    name VARCHAR(255) NOT NULL,
    age INTEGER NOT NULL DEFAULT 18,
    company INTEGER,
    PRIMARY KEY (id),
    FOREIGN KEY (company) REFERENCES Companies (id)
    ON DELETE RESTRICT ON UPDATE CASCADE
);
```


~={cyan}ON DELETE RESTRICT=~ - значит, что, если попробовать удалить запись о компании из таблицы Companies (удалить id компании) И ПРИ ЭТОМ в таблице Users будут записи, ссылающиеся на эту компанию (хранящие в поле company id этой компании) -> ~={orange}БД выдаст ошибку:=~
```sql
Cannot delete or update a parent row: a foreign key constraint fails
```

~={cyan}ON DELETE CASCADE=~ - значит, если попробовать удалить запись о компании из таблицы Companies (удалить id компании) И ПРИ ЭТОМ в таблице Users будут записи, ссылающиеся на эту компанию (хранящие в поле company id этой компании) -> ~={orange}БД удалит эти записи из таблицы Users=~

~={cyan}ON DELETE SET NULL=~ - значит, если попробовать удалить запись о компании из таблицы Companies (удалить id компании) И ПРИ ЭТОМ в таблице Users будут записи, ссылающиеся на эту компанию (хранящие в поле company id этой компании) -> БД установит в поле company значение NULL  для этих записей в таблице Users 

~={cyan}ON UPDATE CASCADE=~ - значит, ~={orange}если обновляется запись о компании в таблице Companies=~, то все ~={orange}записи =~о пользователях~={orange} из=~ таблицы ~={orange}User=~, содержащих id этой компании, ~={orange}будут обновлены=~ (если поменялся id - он изменится и в поле company соотвтетсвующих записей таблицы Users)

> [!note] **Обобщение**
> Все эти правил триггерятся при изменении/удалении записи другой таблицы (чей id мы храним)

----
#### [[Creating and deleting tables - Flashcards|Link to flashcards]]



---
### References:

