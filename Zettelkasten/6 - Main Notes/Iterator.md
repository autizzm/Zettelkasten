
02-09-2025 12:37

Status: #child

Tags: [[Java Core]] [[Java Collections]]

---
# `Iterator<E>`


### Useful Methods:

- ~={green}hasNext()=~
- ~={green}next()=~
- ~={red}*void* =~ ~={green}remove()=~

### Метод next() и remove()

1. `[a,^b, c]` 
2. вызов `next();` -> получаем `b`
3. `[a, b, ^c]`
4. вызов remove() -> ничего не получаем
5. `[a, c]`

> [!note]
> remove() удаляет последний элемент, который возвратил next() (элемент перед положением итератора)

> [!warning]
> Если вызвать remove() до первого вызова next() => IllegalStateException



----
#### [[Iterator Flashcards|Link to flashcards]]



---
### References:

- [[ListIterator]]
- 