
27-08-2026 16:46

Status:

Tags: [[Python]]

---
# Хранение объектов в памяти - Python


В Python переменная хранит **ссылку (reference) на объект**, а сам объект находится в памяти. - **~={orange}концептуально похоже на Java=~**



При присваивании объект не копируется:
```python
a = [1, 2]
b = a

b.append(3)

print(a)  # [1, 2, 3]
```


----
#### [[Хранение объектов в памяти - Python - Flashcards|Link to flashcards]]



---
### References:

