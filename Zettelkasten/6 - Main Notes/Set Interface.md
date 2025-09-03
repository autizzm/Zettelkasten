
02-09-2025 10:28

Status: #child 

Tags: [[Java Core]] [[Java Collections]]

---
# Set Interface


## Реализации:

##### HashSet 
- best performing
- no order
- ~={orange}Быстрая=~ работа:~={cyan} O(1) =~на вставку, удаление, поиск (в среднем).
##### TreeSet
- Red-Black tree
- хранит все в отсортированном виде (строки - по алфавиту)
- Более ~={orange}медленный=~: ~={cyan}O(log n)=~ на вставку, удаление, поиск.
##### LinkedHashSet
- **сохраняет порядок вставки элементов**,
- работает на основе **`HashMap` + двусвязный список** для хранения порядка.
- ~={orange}Быстрая=~ работа:~={cyan} O(1) =~на вставку, удаление, поиск (в среднем).
-  **под капотом - LinkedHashMap**


### Methods Notes:

- *boolean* ~={green}add( ... )=~ - возвратит false, если элемент уже есть в Set ( и он не смог его добавить )


----
#### [[Set Interface Flashcards|Link to flashcards]]



---
### References:

- [[SortedSet Interface]]
