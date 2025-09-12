
11-09-2025 20:59

Status: #baby

Tags: [[Java Core]] [[Small Important Moments Java]]

---
# Initializer - Java

**Field Initializer** (инициализатор поля) – выражение, стоящее справа от объявления поля.  
Пример:

```java
static final int X = 42; //это константа, т.к. ей присвоен литерал

static final float X = 12 / 3; //это константа, т.к. ей присвоен реазультат простой арифметической операции

static final int y = computeY(); // это просто static final, ей присваивается
// сначала default value, а потом уже на этапе инициализации
```

----
#### [[Initializer; constants & just static finals - Java - Flashcards|Link to flashcards]]



---
### References:

- [[Жизненный цикл класса Java]]
- [[clinit method - Java]]