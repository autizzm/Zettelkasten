
14-09-2025 10:02

Status: #child 

Tags: [[SQL]]

---
# Literals - SQL

Главные типы данных в SQL:

- string
- numeric
- logical
- NULL
- date and time literal

### String literals

String literal - всё, что между кавычками.

~={red}!!!!=~ ~={yellow}В ~={orange}MySQL=~ Можно использовать и одинарные `'` и двойные `"` кавычки=~ ~={red}!!!!
=~
~={red}!!!!=~ ~={yellow}В ~={cyan}Postgres=~ Можно использовать ~={cyan}ТОЛЬКО=~ одинарные `'` кавычки. 
Двойные кавычки в ~={cyan}Postgres=~ - ~={cyan}ТОЛЬКО=~ для идентификаторов (=~Например `"My Table"`, нужно, если в названии идентификатора есть пробелы~={yellow})=~ ~={red}!!!!

Например, `'this is a string'` и `"this is a string"`.

Строки в SQL могут содержать специальные символы. Они начинаются с `\`. 
Например, символ новой строки: `\n` - new line.

```sql
SELECT 'Line \n Another line' as String
```

| String            |
| ----------------- |
| Line Another line |


### Numeric literals

|                                                                                                               | Example                                       |
| :------------------------------------------------------------------------------------------------------------ | --------------------------------------------- |
| Includes integers and floating-point numbers. The decimal separator for a floating-point number is "." (dot). | ~={cyan}1=~, ~={cyan}2.9=~, ~={cyan}0.01=~    |
| Can have only an integer part, a fractional part, or both.                                                    | ~={cyan}.2=~, ~={cyan}1.1=~, ~={cyan}10=~     |
| Can be positive or negative (it's not necessary to specify a sign for a positive number).                     | ~={cyan}+1=~, ~={cyan}-10=~, ~={cyan}-2.2=~   |
| Can be represented in exponential notation.                                                                   | ~={cyan}1e3=~ (=1000) ~={cyan}1e-3=~ (=0.001) |

#### Arithmetic opersators

For numeric literals, SQL has all the arithmetic operators we are familiar with:

| Operator                     . | Description                      . | Example               . |
| :----------------------------: | :--------------------------------- | :---------------------- |
|   ~={cyan}%=~, ~={cyan}MOD=~   | Modulus division                   | ~={cyan}11 % 5 = 1=~    |
|          ~={cyan}*=~           | Multiplication                     | ~={cyan}10 * 16 = 160=~ |
|          ~={cyan}+=~           | Addition                           | ~={cyan}98 + 2 = 100=~  |
|          ~={cyan}-=~           | Subtraction                        | ~={cyan}50 - 51 = -1=~  |
|          ~={cyan}/=~           | Division                           | ~={cyan}1 / 2 = 0.5=~   |
|         ~={cyan}DIV=~          | Integer division                   | ~={cyan}10 DIV 4 = 2=~  |

Пример:

```sql
SELECT (5 * 2 - 6) / 2 AS Result;
```

|Result|
|---|
|2|

### Date and Time Literals

Для обозначения времени можно использовать и строки (~={cyan}"1970-12-30"=~ или ~={cyan}"19701230"=~) и цифры (~={cyan}19701230=~). 
В любом случае эти  значения будут интерпретированы как ~={cyan}"December 30th, 1970"=~.

| Description   | Format                                               |                                                                                                                                                                                                                                               |
| :------------ | :--------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Date          | Interpreted as a date with zero time                 | ~={cyan}YYYY-MM-DD=~, ~={cyan}YYYYMMDD=~  <br>  <br>~={red}!!!!=~ ~={yellow}Any punctuation mark can be used instead of the "-" separator. =~ ~={red}!!!!=~<br>  <br>For example:  <br>~={cyan}'2020-01-01'=~ = `January 1st, 2020, 00:00:00` |
| Time          | Contains only time without a specific date           | ~={cyan}hh:mm:ss=~, ~={cyan}hh:mm=~, ~={cyan}hh=~, ~={cyan}ss=~  <br>  <br>The separator can also be omitted.  <br>  <br>For example:  <br>~={cyan}12:11=~ = `12:11:00`                                                                       |
| Date and time | Date with the possibility of setting a specific time | ~={cyan}YYYY-MM-DD hh:mm:ss=~, ~={cyan}YYYYMMDDhhmmss =~ <br>  <br>For example:  <br>~={cyan}'20200101183030'=~ = `January 1st, 2020, 18:30:30`                                                                                               |

### Logical literals

~={cyan}TRUE=~ или ~={cyan}FALSE=~

В MySQL логические литералы превращаются в ~={cyan}1=~ (~={cyan}TRUE=~) или ~={cyan}0=~ (~={cyan}FALSE=~) (хранится как ~={cyan}TINYINT(1)=~).

В PostgreSQL boolean - полноценный тип данных.

## NULL

~={red}!!=~ ~={yellow}Не тип данных, просто значение=~ ~={red}!!=~

Значение ~={cyan}NULL=~ - обозначент "нет данных". Используется, чтобы визуально отличать пустую строку или пробел от отсутствия данных.

----
#### [[Literals - SQL - Flashcards|Link to flashcards]]



---
### References:

