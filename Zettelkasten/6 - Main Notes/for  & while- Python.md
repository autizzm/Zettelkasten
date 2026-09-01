
27-08-2026 17:09

Status:

Tags: [[Python]]

---
# for & while- Python


```python
for item in iterable:
    print(item)
```

`for` перебирает **элементы iterable**: `list`, `tuple`, `str`, `dict`, `set`, `range`, генераторы и т.д.

```python
for x in [1, 2, 3]:
    print(x)
```


> [!note]
> В `dictionary` - по умолчанию перебираются **ключи**:
> ```python
> for key in d:
> ```
> 
> Ключи и значения:
>```python
>for key, value in d.items():
>```
>
>Только значения:
>```python
>for value in d.values():
>```



---
### `enumerate()`

Если нужен индекс при итерации по коллекции:

```python
for i, value in enumerate(items):
    print(i, value)
```

Можно задать начальный индекс:

```python
for i, value in enumerate(items, start=1):
```


---
### `range()`

```python
range(stop)
range(start, stop) # stop - НЕ ВКЛЮЧИТЕЛЬНО
range(start, stop, step)

for i in range(5):       # 0..4
for i in range(1, 5):    # 1..4
for i in range(0, 10, 2): # 0,2,4,6,8
```


---
### `break` / `continue`

```python
for x in items:
    if x == 5:
        break       # полностью выйти из цикла
    if x % 2 == 0:
        continue    # перейти к следующей итерации
```

Вложенные циклы:

```python
for x in matrix:
    for y in x:
        print(y)
```

Для выхода только из внутреннего цикла используется `break`; отдельного `break 2`, как в некоторых языках, нет.


---
### `for` + `else`

```python
for x in items:
    if x == target:
        break
else:
    print("Не найдено")
```

`else` выполняется, **если цикл завершился естественно, без `break`**.


---
## While

```python
i = 0
while i < 10:
	print(i)
	i *= 1
	
	
	
while True:
	print(i);
	i -= 1
	if(i == 4)
		break
```



----
#### [[for - Python - Flashcards|Link to flashcards]]



---
### References:

