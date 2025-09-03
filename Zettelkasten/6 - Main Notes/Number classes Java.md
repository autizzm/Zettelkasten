
01-09-2025 17:53

Status: #child 

Tags: [[Java Core]] [[Java data types]]

---
# Number classes Java

Оборачивает примитивные типы данных, предоставляя расширенный функционал.

#### Subclasses:
- Byte
- Integer
- Double
- Short
- Float
- Long
- BigDecimal
- BigInteger
- AtomicInteger
- AtomicLong

### Useful Methods:

#### Static:
-  ~={green}.parseXXXX( String )=~ - более эффективно для парсинга строк. ~={red}Doesn't wrap the result.=~ => ==Возвращает примитивное значение==
- ~={green}.valueOf( String/ int )=~ - ~={red}Does wrapping=~ => Возвращает наследник Number

#### Non static:
- ~={green}.xxxValue()=~ - преобразует объект класса `<? extends Number>` в примитивный тип
- *int* ~={green}.compareTo( anotherNumberSubclassObj )=~ -  возвращает целочисленную разницу:
```java
Integer int1 = Integer.valueOf(5);
int1.compareTo(7); // = -2
```


### [[Неизменяемость наследников Number]]



----
#### [[Number classes Java Flashcards|Link to flashcards]]



---
### References:

