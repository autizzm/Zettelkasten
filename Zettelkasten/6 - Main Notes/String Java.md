
01-09-2025 14:15

Status: #child 

Tags: [[Java Core]] [[Java data types]]

---
# String Java

> [!warning]
> Строки в Java - неизменяемы. Любые операции, изменяющие строку, создают новый объект String, а не изменяют существующий.

### Useful Methods:


##### Static:
- ~={orange}String.format(String str, Object args )=~ - static метод, форматирует аналогично `System.out.println()` и `PrintStream.format()`
-   ~={orange}**String.join ( ",", myListOfStrings)**=~ - соединит строки из myListOfStrings через символ "," И В КОНЦЕ НЕ ПОСТАВИТ ЕГО

##### Non Static

- ~={orange} **length()**=~ - длина строки
-  ~={orange}**substring( int beginIndex, int endIndex)**=~ - метод для копирования подстрок String
-  ~={orange}**split( String regEx)**=~ - возвращает массив строк, деля исходную строку согласно regEx
- ~={orange} **toLowerCase()**=~ - приведет строку к нижнему регистру
- ~={orange} **toUpperCase()**=~ - приведет строку к верхнему регистру 
-  ~={orange}**trim()** =~- удалит все пробелы до и после строки, если они есть
###### search in String:
- *int*   ~={orange}**indexOf( Char/String )** =~-  the ~={red}first=~ occurance
- *int*  ~={orange} **lastIndexOf( Char/String )**=~ - the last occurance
- *boolean*  ~={orange} **contains( CharSequence )**=~ - есть ли в строке такая подстрока?


### Уточнения по `.valueOf()` и `.toString`

#### `XXX.valueOf() - static`

~={yellow}String.valueOf( someInt ); =~- преобразует значение `someInt` в строку
~={yellow}
Integer.valueOf( someConvertibleObject ); =~- преобразует значение `someConvertibleObject` в объект `Integer`

### `.toString() - non static`

~={yellow}someStr.toString()=~ - бесполезен. НЕ КОПИРУЕТ, возвращает ссылку на себя же




----
#### [[String Java Flashcards|Link to flashcards]]



---
### References:

- [[StringBuilder Java]]
- [[Number classes Java]]