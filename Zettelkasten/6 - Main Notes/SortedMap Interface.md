
03-09-2025 09:41

Status: #child 

Tags: [[Java Core]] [[Java Collections]]

---
# `SortedMap<K,V> (extends Map<K,V>)` Interface


Natural order: хранит по возрастанию (числовые значения) и по алфавиту символьные.


## Реализации:

- TreeMap

### Notable Constructors:

- ~={yellow}SortedMap(`Map<K,V> map`)=~ - аналог конструктора, принимающего колллекцию для наследников Collection
- ~={yellow}SortedMap(`SortedMap<K,V> sortedMap`) =~- создает копию полученной `sortedMap` и копирует себе ее компаратор ~={red}не совершая при этом пересортировки ключей=~

### Useful methods:


- *K* ~={green}firstKey() =~- возвращает первый ключ
- *K*~={green} lastKey()=~ - возвращает последний ключ
- *`Comparator<E>`* ~={green}comparator()=~ - возвращает компаратор этого SortedMap ~={red}или `null,` если используется=~ [[Comparable Interface|natural ordering]]
~={cyan} `+` наследует методы Map=~


#### Range-Veiw methods:

- *`SortedMap<K,V>`* ~={green}subMap(`E fromEl, E toEl`)=~
- *`SortedMap<K,V>`* ~={green}headMap(`E toEl`)=~
- *`SortedMap<K,V>`* ~={green}tailMap(`E fromEl`)


----
#### [[SortedMap Interface Flashcards|Link to flashcards]]



---
### References:

- [[Map Interface]]
- [[Comparable Interface]]