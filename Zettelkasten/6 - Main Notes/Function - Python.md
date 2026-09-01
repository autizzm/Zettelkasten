
28-08-2026 19:35

Status:

Tags: [[Python]]

---
# Function - Python

Функции в Python - являются объектами, то есть их можно возвращать и передавать в качестве аргументов.


```python

def func():
	print('Run')
	
def sum(x, y):
	return x + y	

func()
sum(1,2)

```

В python можно возвращать несколько аргументтов, что по сути представляет собой возврат tuple:

```python
def math_shit(x, y):  
    return x+y, x*y  
  
results = math_shit(1,2)  
print(results) # (1,2)


# Но rule of thumb:
r1, r2 = math_shit(1,2)
```


#### Optional parameter


```python
def func(x, y, z=None):
	print(x, y, z)

func(1,2,3) 
func(1,2)

```

----
#### [[Function - Python - Flashcards|Link to flashcards]]



---
### References:

