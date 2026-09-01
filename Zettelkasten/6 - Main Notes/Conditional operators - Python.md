
27-08-2026 16:43

Status:

Tags: [[Python]]

---
# Conditional operators - Python


Есть два основных вида сравнения:

- `is` — **сравнение идентичности**: это один и тот же объект?
- `==` — **сравнение значения**: объекты равны по содержимому/правилу `__eq__`.

```python
a = [1, 2]
b = [1, 2]
c = a

a == b  # True — одинаковое содержимое
a is b  # False — разные объекты

a is c  # True — одна и та же ссылка
```


> [!warning]
> `is` **не стоит использовать для сравнения значений**, даже если иногда это случайно работает:
> ```python
> a = 100
>b = 100
>
>a is b  # может быть True из-за кеширования небольших int
>a == b  # True — корректное сравнение значений
> ```


### Chaining conditionals

```python
# Evaluation order
1. not
2. and
3. or

x = 0
y = 9
z = 0

result1 = x == y
result3 = y > x
result2 = z  < x + 2


result4 = result1 or result2 and not result3


```

----
#### [[Conditional operators - Python - Flashcards|Link to flashcards]]



---
### References:

