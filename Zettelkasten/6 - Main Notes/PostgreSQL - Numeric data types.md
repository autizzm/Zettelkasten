
17-09-2025 11:22

Status: #child

Tags: [[SQL]] [[PostgreSQL specifics]]

---
# PostgreSQL - Numeric data types


## Целые числа (Integers)

| Type                                                 | Memory size | Range                                                    |
| :--------------------------------------------------- | :---------- | :------------------------------------------------------- |
| ~={cyan}SMALLINT=~                                   | 2 bytes     | from -32768 to 32767~={red} (like ***short*** in Java)=~ |
| ~={cyan}INT  <br>INTEGER =~<br>~={orange}(aliases)=~ | 4 bytes     | from -2147483648 to 2147483647                           |
| ~={cyan}BIGINT=~                                     | 8 bytes     | from -9223372036854775808 to 9223372036854775807         |

## Auto-increment types

| Type                | Memory size | Range                         |
| :------------------ | :---------- | :---------------------------- |
| ~={cyan}SMALLSERIAL | 2 bytes     | from 1 to 32767               |
| ~={cyan}SERIAL=~    | 4 bytes     | from 1 to 2147483647          |
| ~={cyan}BIGSERIAL=~ | 8 bytes     | from 1 to 9223372036854775807 |

> [!note] **SERIAL - макрос**
> ~={orange}SERIAL=~ - псевдотип, он представляет собой макрос, с автоувеличивающимся ~={orange}INTEGER=~


## Точные дробные типы

| Type                                                                                                        | Точность                   | Range                                                       |
| :---------------------------------------------------------------------------------------------------------- | :------------------------- | :---------------------------------------------------------- |
| ~={cyan}DECIMAL[(precision, scale)]  =~<br>~={cyan}NUMERIC[(precision, scale)]  =~<br>~={orange}(aliases)=~ | Определяется пользователем | До 131072 символов перед запятой и до 16383 символов после. |

Используется когда критически важна точность:
```sql
CREATE TABLE Users (
    ...
    salary NUMERIC(10,2) -- 10 целых цифр, 2 - после запятой
    -- от -99999999.99 до 99999999.99.
);
```


## Floating point types

- Очень быстрые.
    
- Подходят для научных расчётов, статистики, машинного обучения, графики.
    
- Но не подходят для денег и финансов → из-за ошибок округления. Например:
    
    ```sql
    `SELECT 0.1 + 0.2; 
    -- вернёт 0.30000000000000004`
    ```


| Type                       | Memory size | Precision            | Range                 |
| :------------------------- | :---------- | :------------------- | :-------------------- |
| ~={cyan}REAL=~             | 4 bytes     | ~={green}6=~ digits  | from 1E-37 to 1E+37   |
| ~={cyan}DOUBLE PRECISION=~ | 8 bytes     | ~={green}15=~ digits | from 1E-307 to 1E+308 |

----
#### [[PostgreSQL - Numeric data types - Flashcards|Link to flashcards]]



---
### References:

