
01-09-2025 20:09

Status: #child

Tags: [[Java Core]]

---
# Arrays class

### Useful Methods:
#### ~={orange}(All static)=~

- ~={green}.fill( Object[]a, Object value )=~ - заполняет массив a значениями value
- ~={green}.sort( Object[] a )=~ - сортирует по возрастанию, использует Dual-Pivot QuickSort - O(n log(n))
- ~={green}stream( T[] a)=~, ~={green}stream(T[], int from, int to)=~ - создает stream из массива
- ~={green}.asList()=~ <=> ~={green}List.of(T[])=~ - возвращают ~={red}FIX-SIZED=~ Lists

- int ~={green}Arrrays.binarysearch(`byte[] a , byte key`)=~ - работает для массивов всех типов, возвращает индекс элемента `key` в массиве. ~={red}Если элемент не найден returns int < 0 || int > array.length=~ - в документации никакой конкретики.
- `T[]` ~={green}Arrays.copyOf(`T[] source`)=~ - создаёт копию массива



----
#### [[Arrays class Flashcards|Link to flashcards]]



---
### References:

