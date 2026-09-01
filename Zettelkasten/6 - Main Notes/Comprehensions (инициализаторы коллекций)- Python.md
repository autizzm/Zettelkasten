
28-08-2026 19:27

Status:

Tags: [[Python]]

---
# Comprehensions (инициализаторы коллекций)- Python


Работает со всеми типами коллекций

```python
x = [x for x in range(5)]

print(x) # [0,1,2,3,4]
```


```python
x = [x + 5 for x in range(5)]

print(x) # [5,6,7,8,9]
```

```python
x = [0 for x in range(5)]

print(x) # [0,0,0,0,0]
```

```python
x = [i for i in range(50) if i % 5 == 0] # добавляем в список только если нацело делится на 5

print(x) # [0,5,10,15,20,25,30,35,40,45]
```


### Пример с dict

```python
x = {i:0 for i in range(30) if i % 5 == 0}  
  
print(x)
# {0: 0, 5: 0, 10: 0, 15: 0, 20: 0, 25: 0}
```


### Пример с tuple

```python
x = tuple(i for i in range(30) if i % 5 == 0)  
  
print(x)
# (0, 5, 10, 15, 20, 25)
```

----
#### [[Comprehensions (инициализаторы коллекций)- Python - Flashcards|Link to flashcards]]



---
### References:

