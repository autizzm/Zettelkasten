
Theory for the cards: [[Autoboxing & Autounboxing - Java Core]]

FILE TAGS: java java_core

Q: Что такое autoboxing и когда он происходит?
A: Autoboxing - автоматическое преобразование примитива в соответствующий wrapper-тип.
	
- присваивание примитива wrapper-переменной;
- **передача примитива в метод, ожидающий wrapper**;
- добавление примитивов в generic-коллекции (`List<Integer>`, `Map<Integer, ...>`); - по сути тоже **передача в метод, ожидающий Wrapper**
- возврат примитива из метода с wrapper-типом.
<!--ID: 1785858957743-->


Q: Что такое autounboxing и когда он происходит?
A: Autounboxing - автоматическое преобразование wrapper-типа в примитивный тип.
	
- присваивание Wrapper примитиву;
- передача Wrapper переменной в метод, требующий примитив;
- арифметические операции (`+`, `-`, `*`, `/`, `%`);
- сравнения (`>`, `<`, `>=`, `<=`);
- `switch` по wrapper-типу.
	
```java
Integer x = 10;
int y = x;               // x.intValue()
	
int sum = x + 5;         // unboxing
if (x > 3) { }           // unboxing
switch (x) { ... }       // unboxing
	
void print(int x) {
    System.out.println(x);
}
	
print(value);            // unboxing
```
<!--ID: 1785858957753-->


Q: Что тут не так?
```java
Long sum = 0L;
for (long i = 0; i < 1_000_000; i++) {
    sum += i; 
}
```
A: При каждой итерации тут происходит autounboxing (для выполнения арифметич. операции инкремента) и autoboxing (чтобы сложить значение обратно в `Long`).
	
```java
long sum = 0L; // Выход: используем примитив
for (long i = 0; i < 1_000_000; i++) {
    sum += i;
}
```
<!--ID: 1785858957760-->

