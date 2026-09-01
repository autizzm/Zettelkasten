
27-08-2026 16:57

Status:

Tags: [[Python]]

---
# Collections - Python



### List

Похож на stack

```python
x = [4, True, 'hi'] # May store elements of different types
```

**~={orange}Порядок вставки элементов в List сохраняется=~**

```python
y = []

len(x) # 3
len(y) # 0


x.append('hello') # x = [4, True, 'hi', 'hello']

x.extend([4, 5, 6]) # аналог addAll() в Java


last_of_x = x.pop() # removes and returns the last element of the list
# last_of_x = 'hello'

# pop по индексу
second_of_x = x.pop(1) # x = [4, 'hi']
# second-of_x = True


# Random access
value = x[1] # 'hi'

x[1] = 'hi_upd' # x = [4, 'hi_upd']
```

**Вложенные списки:**

```python
x = [[], (1, 1), [[], [0], [3, 4, 5]]]
```


---
### Tuple

**~={orange}Immutable=~ list**

НЕ аналог Set - may containe duplicates

```python
x = (0, 0, True, 'Hi')

# Random Access
value = x[0] # 0

# try to change value:
x[0] = 1 # Error
```


---
### Set

```python
x = set() # empty set

x = {} # - не подходит, т.к. пустой литерал создаст dictionary


x = {4, 32, 3}


# Добавление
x.add(5)

# Удаление
x.remove(2) # Если элемента нет в set - выкинет ошибку

# Проверка, импеется ли элемент в set
print(4 in x) # True
```

> [!note]
> Если в литерал загрузить повторяющиеся элементы - будет добавлен только один и ошибок выброшено не будет
> ```python
> x = {2, 32, 3, 2}  
  >
> print(x) # {32, 2, 3}
> ```


Bulk operations:

```python
s = {4, 32, 2}
s2 = {3, 4, 22, 1}


# Объединение
u = s.union(s2) # {4, 32, 2, 3, 22, 1}

# Отличие
d = s.difference(s2) # {32, 2} - all elements that are in this set but not the others

# Пересечение
i = s.intersection(s2) # {4}
```



---
### Dictionary (Map)

Могут хранить как ключи, так и значения разных типов

```python
x = {} # empty map literal

x = {'key': 4, 5: 'fifth value', 'list': [1,2,3]}

# Access
k = x['key']

# Добавление
x['key2'] = 5

# Удаление
del x['key'] # {'key2': 5}

# Проверка, импеется ли ключ в dict
print('key' in x) # True

# Получение list всех значений dict:
values_list = list(x.values())
# x.values() - возвращает обертку вокруг значений (view object), которую надо type кастить
# view object - If you change the dictionary later, that view updates to reflect the new values.
```


~={orange}**dict.clear**()=~ - очищает словарь.

~={orange}**dict.copy**()=~ - возвращает копию словаря.


~={orange}**dict.get**(`key[, default]`)=~ - возвращает значение ключа, но если его нет, не бросает исключение, а возвращает default (по умолчанию None).

~={orange}**dict.pop**(`key[, default]`)=~ - удаляет ключ и возвращает значение. Если ключа нет, возвращает default (по умолчанию бросает исключение).

~={orange}**dict.popitem**()=~ - удаляет и возвращает пару (ключ, значение). Если словарь пуст, бросает исключение KeyError. Помните, что словари неупорядочены.

~={orange}**dict.setdefault**(`key[, default]`)=~ - возвращает значение ключа, но если его нет, не бросает исключение, а создает ключ со значением default (по умолчанию None).

~={orange}**dict.update**(`[other]`)=~ - обновляет словарь, добавляя пары (ключ, значение) из other. Существующие ключи перезаписываются. Возвращает None (не новый словарь!).


Для итерации:

~={orange}**dict.items**()=~ - возвращает пары (ключ, значение).

~={orange}**dict.keys**()=~ - возвращает ключи в словаре.

~={orange}**dict.values**()=~ - возвращает значения в словаре.


----
#### [[Collections - Python - Flashcards|Link to flashcards]]



---
### References:

