
15-09-2025 15:58

Status: #baby 

Tags: [[SQL]]

---
# Operator HAVING - SQL

Оператор HAVING предназначен для проверки условий. Он аналогичен WHERE, но работает для сгруппированных представлений.

Например, нам нужно отобразить только те результаты выборки, где `avg_price > 50`.
Наш текущий запрос:
```sql
SELECT home_type, AVG(price) as avg_price FROM Rooms
GROUP BY home_type
```

Результат:

| home_type       | avg_price |
| --------------- | --------- |
| Private room    | 89.4286   |
| Entire home/apt | 148.6667  |
| Shared room     | 40        |

> [!note] **Почему нельзя использовать WHERE с ORDER BY**
> В соответствии с [[Порядок выполнения SQL запроса|порядком выполнения SQL запроса]] ~={cyan}WHERE=~ выполняется ~={orange}перед группировкой=~ -> это будет сортировка полей до группировки
> 
> Если мы хотим сортировать результат группировки (и мб использовать агрегатные функции при этом), то нужно использовать ~={cyan}HAVING=~, т.к. он фильтрует строки уже ~={orange}после группировки=~

Попытка использовать WHERE:
```sql
SELECT home_type, AVG(price) as avg_price FROM Rooms
GROUP BY home_type
WHERE AVG(price) > 50; -- ошибка, мы пытаемя сравнить среднее по группе до того как группа создаётся
```


Правильный синтаксис фильтрации этого группированного представления:
```sql
SELECT home_type, AVG(price) as avg_price FROM Rooms
GROUP BY home_type
HAVING AVG(price) > 50;
```


> [!warning] **PostgreSQL feature**
> В PostgreSQL  aliases - не могут быть использованы в HAVING:
> ```sql
SELECT home_type, AVG(price) as avg_price FROM Rooms
GROUP BY home_type
HAVING avg_price > 50; --ошибка
>```



----
#### [[Operator HAVING - SQL - Flashcards|Link to flashcards]]



---
### References:

