
Theory for the cards: [[Built-in functions - SQL]]

FILE TAGS: sql

Q: Как можно получить строку из БД, приведенную к верхнему или нижнему регистру?
A: При SELECT использовать LOWER(), UPPER():
```sql
SELECT LOWER('SQL Academy') AS lower_string;
```
<!--ID: 1757841003464-->


Q: Как можно получить индекс первого символа или подстроки в строке в sql?
A:  
```sql
SELECT POSITION('academy' IN 'sql-academy') AS idx;
```
<!--ID: 1757841003475-->


Q: Что выведеть в консоль эта команда?
```sql
SELECT POSITION('-' IN 'sql-academy');
```
A: Она выведет 4. Т.к в sql индексация строк начинается с 1.
<!--ID: 1757841003480-->


Q: Как получить часть даты из поля с типом даты?
A:  
```sql
EXTRACT(field FROM source)
```
- **field** — часть даты (YEAR, MONTH, DAY и т.д.),
<!--ID: 1757841003487-->


- **source** — это выражение с типом даты/времени (DATE, TIMESTAMP, TIMESTAMPTZ, INTERVAL).

Q: Как вывести длины строк хранимых в поле `name` таблицы `myTable`? (вывести их в представление с именем столбца "Names Length")
A:   
```sql
SELECT LENGTH(name) AS "Names Length" FROM myTable;
```
<!--ID: 1757841003492-->

