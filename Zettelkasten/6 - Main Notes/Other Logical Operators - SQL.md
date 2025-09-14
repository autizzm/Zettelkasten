
14-09-2025 17:01

Status: #child

Tags: [[SQL]]

---
# Other Logical Operators - SQL

### **BETWEEN**

Оператор ~={cyan}BETWEEN min AND max=~ позволяет проверить находится ли проверяемое значение в диапазоне от ~={cyan}min=~ до ~={cyan}max=~.

```sql
SELECT * FROM Payments
WHERE unit_price BETWEEN 100 AND 500;
```


### **IN**

Оператор ~={cyan}IN=~ позволяет проверить, совпадает ли значение поля со ~={cyan}значениями из списка=~:
```sql
SELECT * FROM FamilyMembers
WHERE status IN ('father', 'mother');
```


### **LIKE**

Используется для проверки поля на ~={cyan}соответствие=~ определенному ~={cyan}паттерну=~.

Синтаксис:
```sql
... WHERE table_field [NOT] LIKE string_pattern
```

Паттерн может включать следующие специальные символы:

| Character | Description                                                                |
| :-------- | :------------------------------------------------------------------------- |
| %         | Любая последовательность символов~={cyan} в любом количестве=~ (хоть ноль) |
| _         | ~={cyan}Один=~ любой символ                                                |
Пример:
```sql
SELECT name, email FROM Users
WHERE email LIKE '%@hotmail.%'
```

####         -   **ESCAPE**

> [!note] **Если нужно использовать `_` или `%` как часть паттерна**
> Чтобы символы `_` или `%` как часть паттерна -> используем ~={green}ESCAPE=~
> Синтаксис:
> ```sql
>expr LIKE pattern [ESCAPE escape_character]
> ```
>- `pattern` — строка-шаблон, где `%` и `_` — спецсимволы.
 >   
>- `escape_character` — один символ (обычно `\`, `!`, `#` и т.п.).

С помощью ~={cyan}`ESCAPE`=~ можно указать символ, который будет означать «восприми следующий символ буквально».

Пример:
```sql

`SELECT * FROM books WHERE title LIKE '100!% complete' ESCAPE '!';`
```

- Здесь `!%` → ищем **буквально `%`**, а не «любой набор символов».
    
- Если в таблице есть запись `"100% complete"`, то она попадёт в выборку.


----
#### [[Other Logical Operators - SQL - Flashcards|Link to flashcards]]



---
### References:

