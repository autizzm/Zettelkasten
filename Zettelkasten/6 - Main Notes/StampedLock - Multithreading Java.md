
21-01-2026 20:01

Status:

Tags: [[Multithreading]] [[Java+]]

---
# StampedLock - Multithreading Java


- **writeLock() / unlockWrite(stamp)**  
    Эксклюзивная блокировка: только один писатель, читатели и другие писатели ждут.​
    Если один поток уже поднял `writeLock`, то остальные потоки:
    - ❌ **НЕ МОГУТ** зайти в `readLock()` — писатели ждут освобождения всех читателей
    -  ❌ **НЕ МОГУТ** зайти в `writeLock()` — писатели ждут освобождения всех читателей
    
- **readLock() / unlockRead(stamp)**  
    Разделяемая блокировка чтения: много читателей одновременно, но без писателя.​
    Если один поток уже поднял `readLock`, то остальные потоки:
    - ✅ **Зайти в `readLock()`** — **множество читателей одновременно** (shared read lock)
	- ❌ **НЕ МОГУТ** зайти в `writeLock()` — писатели ждут освобождения всех читателей
    
- **tryOptimisticRead() / validate(stamp)**  
    Оптимистичное чтение без реального блокирования: читаем данные, затем проверяем, не изменились ли они в момент чтения; если изменились — повторяем чтение под обычной read/write‑блокировкой.

## Как работают stamp'ы

1. **Каждый вызов** `readLock()`, `writeLock()`, `tryOptimisticRead()` возвращает **уникальный** `long stamp` — это **номер поколения состояния** блокировки (счётчик, который увеличивается при каждом  `write`).​
    
2. **При unlock**: `unlockWrite(stamp)` / `unlockRead(stamp)` проверяют, что переданный stamp **действителен** (соответствует текущему состоянию блокировки):
    
    - Если stamp совпадает → блокировка снимается ✅
        
    - Если stamp **неверный** (протух/неактуален) → `IllegalMonitorStateException` ❌
        
3. **При validate(stamp)** для optimistic read:
    
    - Проверяет, не изменилось ли **состояние блокировки** с момента получения stamp
        
    - Если stamp **протух** (была write-блокировка между `tryOptimisticRead()` и `validate()`) → возвращает `false`
        
    - **НЕ откатывает** изменения, а говорит: "данные могли измениться, перечитай под защитой"

```java
StampedLock lock = new StampedLock();

// Поток A: optimistic read
long stampA = lock.tryOptimisticRead();  // stamp = 1000
double x = point.x; 
double y = point.y;

// Поток B: запись (меняет состояние!)
long stampB = lock.writeLock();          // stamp = 1001 (счётчик +1)
point.x = 10; point.y = 20;
lock.unlockWrite(stampB);

// Поток A: проверка
if (!lock.validate(stampA)) {            // 1000 != текущее состояние → false
    // НЕ откатывает изменения B!
    // Просто повторяет чтение под readLock
    long newStamp = lock.readLock();
    try {
        x = point.x; 
        y = point.y;        // теперь читает актуальные 10, 20
    } finally {
        lock.unlockRead(newStamp);
    }
}
```



### Очень важный недостаток StampedLock

**Он не реентерабельный**

Это самая частая ловушка.

Такой код может зависнуть:

```java
void foo() {
    long s = lock.writeLock();
    try {
        bar();
    } finally {
        lock.unlockWrite(s);
    }
}

void bar() {
    long s = lock.writeLock(); // deadlock
    ...
}
```

`ReentrantReadWriteLock` такое позволяет.

Поэтому `StampedLock` требует гораздо более аккуратного проектирования.

### Чем `readLock()` у `StampedLock` отличается от аналога в `ReentrantLock`?

Ничем: `readLock()` у `StampedLock` захватывает полноценную разделяемую блокировку на чтение, почти аналогично `ReentrantReadWriteLock.readLock().lock()`.

`readLock()` часто служит fallback-механизмом, если оптимистичное чтение оказалось недействительным.


----
#### [[StampedLock - Multithreading Java - Flashcards|Link to flashcards]]



---
### References:

