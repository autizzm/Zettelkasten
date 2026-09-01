
28-08-2026 18:45

Status:

Tags: [[Python]]

---
# Slicing - Python

Работает с `str` и коллекциями типа `list`, `tuple`.

```python
list = [0,1,2,3,4,5,6,7,8,9]

sliced = list[start:stop:step]
# start - включительно
# stop - включительно 


# Example

sliced = list[0:5:2]
# Отбирает начиная с 0 с шагом 2 до индекса 4:
# [0,2,4]

```


> [!tip] **Разворот строки/коллекции**
> ```python
> my_message = '0123456789'  
>  
>print(my_message[::-1]) # 9876543210
> ```


### Со строками - аналогично

`my_message[0:5]` - вернет строку начиная с индекса 0 (inclusive) до индекса 5 (inclusive)

- `my_message[:5]` - вернет строку начиная с начала до индекса 5 (inclusive)
- `my_message[6:]` - аналогично

```python
my_message = '0123456789'  
  
print(my_message[:5]) # 01234
```



----
#### [[Slicing - Python - Flashcards|Link to flashcards]]



---
### References:

