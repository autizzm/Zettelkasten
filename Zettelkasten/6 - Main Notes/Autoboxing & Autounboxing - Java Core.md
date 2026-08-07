
04-08-2026 18:49

Status:

Tags: [[Java Core]]

---
# Autoboxing & Autounboxing - Java Core


### Когда происходит autoboxing

Компилятор вставляет вызов `Integer.valueOf(...)` (или аналогичного метода).

```java
Integer x = 10;        // Integer.valueOf(10)
List<Integer> list = new ArrayList<>();
list.add(5);             // boxing
Object obj = 7;          // boxing до Integer
```


**Типичные ситуации:**

- присваивание примитива wrapper-переменной;
- **~={yellow}передача примитива в метод, ожидающий wrapper=~**;
- добавление примитивов в generic-коллекции (`List<Integer>`, `Map<Integer, ...>`); - по сути тоже **~={yellow}передача в метод, ожидающий Wrapper=~**
- возврат примитива из метода с wrapper-типом.


---
### Когда происходит auto-unboxing

Компилятор вставляет вызов `intValue()`, `longValue()` и т.д.

```java
Integer x = 10;
int y = x;               // x.intValue()

int sum = x + 5;         // unboxing
if (x > 3) { }           // unboxing
switch (x) { ... }       // unboxing
```

Типичные ситуации:

- присваивание wrapper примитивной переменной;
- арифметические операции (`+`, `-`, `*`, `/`, `%`);
- сравнения (`>`, `<`, `>=`, `<=`);
- `switch` по wrapper-типу.


---
### Самые важные нюансы


### Лишний boxing/unboxing

Может создавать объекты и ухудшать производительность.


```java
Long sum = 0L;
for (long i = 0; i < 1_000_000; i++) {
    sum += i;      // unboxing + boxing на каждой итерации
}
```

Лучше:

```java
long sum = 0L;
```


**~={red}?!=~** В горячем коде предпочтительнее использовать примитивы, чтобы избежать лишнего boxing/unboxing.



----
#### [[Autoboxing & Autounboxing - Java Core - Flashcards|Link to flashcards]]



---
### References:

