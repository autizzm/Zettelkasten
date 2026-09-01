
27-08-2026 15:44

Status:

Tags: [[Python]]

---
# Strings - Python

> [!tip]
>
>```python
> name = 'Michael' # string
>
>print(dir(name)) # outputs all the methods available for this data type
>```
>
> Full description:
> ```python
> print(help(str)) # str - string class name
> # Will output full class description with each method's behavior
>
> # Method info:
> print(help(str.lower)) # .lower - без скобок
> ```



String - класс в Python

```python
message = 'Michael'

print(type(message))
# <class 'str'>
```


----
### Виды кавычек (quotes)


**Одинарные:**

```python
message = 'My message'
```

Проблема - если в слове есть апостраф - путает его с символом конца строки:

```python
message = 'Bobby's message'

# P.S. можно решить через escape symbol:
message 'Bobby\'s message'
```



**Двойные:**

Решают эту проблему:

```python
message = "Bobby's message"
```

Но аналогичная проблема с двойными кавычками:

```python

message = "He said: "Hi""

# Solutions:
message = "He said \"Hi\""

message = 'He said "Hi"'
```


### Multi-line strings

```python
message = """Some 
multiline 
message"""
```


---
### Методы работы со String

```python
message = 'Hello World'

len(message) # вернет длину строки

message.lower() #to lowercase
message.uppper() #to uppercase

message.count('l') # вернет количество символов l в строке message
message.count('Hello') # вернет количество подстрок 'Hello' в строке message

message.find('l') # Вернет индекс первого символа с начала строки
message.find('World') # вернет индекс первого сивола первой встреченной подстроки 'Hello' в строке 'message' (здесь вернет 6)
message.find('Universe') # вернет -1


new_message = message.replace('World', 'Universe') # Создаст новую строку с замененной подстрокой `World` на `Universe`
```

#### Concatenation

```python
greeting = 'Hello'
name = 'Michael'

message = greeting + ', ' + name
```

String multiplication:

```python
x = 'hello'
y = 3

print(x * y)
# hellohellohello
```

#### Formatting strings

```python
greeting = 'Hello'
name = 'Michael'

message = '{}, {}. Welcome!'.format(greeting, name)
```


**F-strings** - позволяют упростить форматирование

```python
greeting = 'Hello'
name = 'Michael'

message = f'{greeting}, {name}. Welcome!'

# Позволяют использовать функции над вставляемыми строками более явно
message = f'{greeting.upper()}, {name}. Welcome!'
```

#### Random access:

```python
my_string[i]  # доступ к символу по индексу `i`
```



----
#### [[Strings - Python - Flashcards|Link to flashcards]]



---
### References:

- [[Slicing - Python]]
