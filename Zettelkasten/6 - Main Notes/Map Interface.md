
02-09-2025 17:52

Status:  #baby (no merge(), putAll(), ...)

Tags: [[Java Core]] [[Java Collections]]

---
# Map Interface

##### [JavDoc Link](https://docs.oracle.com/javase/8/docs/api/java/util/Map.html "interface in java.util")


### Реализации:
##### HashMap
##### TreeMap
- хранит все в отсортированном виде (строки - по алфавиту)
##### LinkeHashMap
- хранит в порядке входа (вставки)



>[!Notes on constructors]
>У Map есть конструктор, принимающий Map для создания копий, по аналогии с конструктором коллекций реализующих Collection



### Methods:

#### ~={orange}Static=~

- ~={green}Map.of(`K k1, V v1, ... `)=~ - создает ~={red}unmodifiable=~ map

#### ~={orange}Non Static=~

- *V* ~={green} put(`K key, V value`)=~ - вставит новое значение и вернет то значение, которое было до этого
- *V*  ~={green}get(`K key`)=~ - вернет null, если ключа нет в Map
- *V* ~={green}getOrDefault(`K key, V default`)=~ - вернет default значение, если этого ключа нет в Map
- *V* ~={green}remove(`K key`) =~- возвращает значение по удаленному ключу
- ~={yellow}*boolean*=~ ~={green} remove(`K key, V value`)=~ - удаляет пару ключ-значение, только если  ключу key соответствует значение value  
- *V* ~={green}replace(`K key, V value`)=~ - ЗАМЕНЯЕТ значение по ключу key, если оно не равно `null`, возвращает предыдущее значение или ~={red}`null`, если значения по ключу key не было ( он не сохранит новое) =~

>[!question]
>метод merge()




##### ~={cyan}Bulk operations=~

- *void* putAll(Map<K, V> map) - вставляет все пары ключ значения из map

#### Collection-View methods:

>[!note]
>Collection-view содержит ссылки на элементы Map, НЕ УКАЗАТЕЛИ. Collection-View - это части Map.

- keySet() - возвращает Set ключей
- values() - возвращает Set значений
- entrySet() -  возвращает Set значений `Map.Entry<K,V>`  

>[!note] 
>- ✅ При **первом вызове** `keySet()` создаётся новый view-объект.
>- ✅ При последующих вызовах возвращается **та же ссылка** (Map хранит её в поле).


> [!warning]
> Единственный безопасный способ удалять элементы из Map во время итерации - использовать итератор по keySet и метод remove() (автоматически удалит пары ключ-значение из Map)


> [!warning]
> При итерировании по keySet НЕЛЬЗЯ добавлять элементы ни в него (~~iterator.add(e)~~ ) ни в сам Map (~~map.put(k, e)~~)

### Приколы

>[!note]
>Map может содержать себя как ключ и как значение. Однако выполнение оперций .equals() и hashCode(), если Map содержит себя как VALUE, может привести к неопределенному поведению.



> [!note]
> Если ключом в Map является изменяемый объект, измение которого влияет на результат сравнения equals() => при изменении такого ключа возникнет неопределенное поведение



>[!note]
>null-ключи разрешены только в HashMap и LinkedHashMap



>[!note]
>В Java нет MultiMaps => используем Map<..., List<...>>






----
#### [[Map Interface Flashcards|Link to flashcards]]



---
### References:

- [[SortedMap Interface]]
