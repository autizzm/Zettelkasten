
28-10-2025 08:55

Status: #baby 

Tags: [[JMM]] [[Java Core]]

---
# StringPool - JMM

Место где хранятся строковые литералы.

Позволяет переиспользовать одинаковые строковые литералы:
```java
//В StringPool будут ссылаться на один и тот же литерал
String hello = "hello";
String hi = "hello"; 
```

Creation via literal (e.g., `String hi = "hello";`) checks the pool: if a matching value exists, it reuses the reference; otherwise it creates a new one in the pool.

> [!warning]
> Создание объекта через `new String("...")` всегда создаёт НОВЫЙ объект в StringPool


StringPool - пример паттерна [[Flyweight - pattern|Flyweight]]

>Before Java 7, the JVM **placed the Java String Pool in the _PermGen_ space, which has a fixed size — it can’t be expanded at runtime and is not eligible for garbage collection**.
>
>The risk of interning _Strings_ in the _PermGen_ (instead of the _Heap_) is that **we can get an _OutOfMemory_ error** from the JVM if we intern too many _Strings_.
>
>From Java 7 onwards, the Java String Pool is **stored in the** [**_Heap_ space**](https://muratakkan.medium.com/java-memory-management-101-heap-memory-ca4060b10685)**, which is garbage collected** by the JVM_._ The advantage of this approach is the **reduced risk of _OutOfMemory_ error** because unreferenced _Strings_ will be removed from the pool, thereby releasing memory.
>

---
### Метод new String("...").intern()

Заставляет искать строку сначалао в StringPool, и только если нет - создавать новую.

Делает создание строки конструкторомв эквивалентным обычной инициализации литералом.



---
### Example

```java
String s1 = "Harry Potter";  
String s2 = "The Lord of the Rings";  
String s3 = "Harry Potter";

String s4 = new String("Harry Potter");  
String s5 = new String("The Lord of the Rings");

s1==s3 //true  
s1==s4 //false
```

> [!note] 
> Оператор `==` сравнивает адреса в памяти!!

----
#### [[StringPool - JMM - Flashcards|Link to flashcards]]



---
### References:

- [[Flyweight - pattern]]
