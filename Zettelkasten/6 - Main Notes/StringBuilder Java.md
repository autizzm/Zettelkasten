
01-09-2025 17:16

Status: #child

Tags: [[Java Core]] [[Java data types]]

---
# StringBuilder Java

Позволяет создавать ~={orange}изменяемые=~ объекты, хранящие символы. И получать из него неизменяемые строки String.

> [!note]
> Под капотом это - массив char

#### Useful Constructor:
- ~={green}**StringBuilder( String str )**=~ - создаст объект StringBuilder, содержащий последовательность символов строки str

#### Useful Methods:
- ~={green}append ( any type )=~
- ~={green}delete( int start, int end )=~
- ~={green}deleteCharAt( int index )=~
- ~={green}insert( int startIndex, any type)=~ - вставляем объект ПЕРЕД индексом start
- ~={green}reverse()=~ - развернет хранимую последовательность char
- ~={pink}**toString()**=~ - получение объекта String

> [!note]
> .toString() НЕ ЯВЛЯЕТСЯ терминальной операцией StringBuilder. После вызова toString() можно продолжать использовать объект StringBuilder.


----
#### [[StringBuilder Java Flashcards|Link to flashcards]]



---
### References:

