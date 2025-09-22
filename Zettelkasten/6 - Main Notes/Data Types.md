
01-09-2025 13:52

Status: #child

Tags: [[Java Core]] [[Java data types]]
 
---
# Data Types

| name    | default value | description                                           |
| ------- | ------------- | ----------------------------------------------------- |
| byte    | 0             | **signed** *8-bit* (-128 <= byte <= 127)              |
| short   | 0             | **signed** *16-bit* (-32.768 <= byte <= 32.767)       |
| int     | 0             | **signed** *32-bit*                                   |
| long    | 0**L**        | **signed** *64-bit*                                   |
| float   | **0.0f**      | **signed** *32-bit* ~={cyan}**floating point type**=~ |
| double  | 0.0d          | **signed** *64-bit* ~={cyan}**floating point type**=~ |
|         |               |                                                       |
| char    | '\u000'       | single *16-bit* Unicode character                     |
| boolean | false         |                                                       |

> [!warning]
>  Default values присваиваются только полям класса, если они не заданы явно в конструкторе.  Локальным переменным default values не присваиваются!


%d - ==формативный спецификатор== для ***целых*** типов данных

%f - ==формативный спецификатор== для ***дробных*** типов данных

%.1f - обозначит количество знаков после запято1, которое будет вставлено в строку при форматировании (1 знак)


> [!warning] **Погрешности при работе с floating point types**
> при работе с `double` и `float` **в Java возникают погрешности**, и это естественно для всех **типов с плавающей точкой**:
> ```java
>double a = 0.1;
double b = 0.2;
double c = a + b;
System.out.println(c); // 0.30000000000000004
> ```
>[[PostgreSQL - Numeric data types#Floating point types|(как и в SQL)]]


Для точности используем:
- `BigInteger` - **целые числа произвольной длины**
- `BigDecimal` - **десятичные числа с произвольной точностью и масштабом**

----
### [[Data Types Flashcards |Link to flashcards]]




---
### Similar notes:

[[String Java]]
