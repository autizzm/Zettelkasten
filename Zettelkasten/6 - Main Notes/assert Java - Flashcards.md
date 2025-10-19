
Theory for the cards:  [[assert Java]]

FILE TAGS: java_core

Q: Что за ключевое слово assert в core java? Для чего оно предназначено?
A: Это оператор проверки выполнения булевых условий. Assertions может быть включён и выключен. JVM хранит флаг "assertions enabled/disabled for this class" в метаданных класса. Это значение получается при инициализации класса от ClassLoader.
Если assertions выключены то выражения в asserions не выполняются:
```java
assert expr1; 
assert expr1 : expr2;
// ни expr1 ни expr2 вычислены не будут
```
Если же assertions включены и выражение `expr1 == false`, будет выкинуто `AssertionError(`*результат expr1* `)`
<!--ID: 1757679607497-->


Q: Что  здесь произойдет, если assertions отключены в ClassLoader?
```java
int x = -1;
assert x >= 0 : "x must be non-negative";
```
A: Ничего, строчка `assert x >= 0 : "x must be non-negative";` будет полностью проигнорирована
<!--ID: 1757679607511-->


Q: Что  здесь произойдет, если assertions включены в ClassLoader?
```java
int x = -1;
assert x >= 0 : "x must be non-negative";
```
A: Будет выкинуто `AssertionError`("x must be non-negative")
<!--ID: 1757679607518-->


Q: Что  здесь произойдет, если assertions включены в ClassLoader? Выведется ли что-то на экран?
```java
int x = -1;
assert x <= 0 : System.out.println("Hi");
```
A: Нет, т.к. `(x <= 0) == true`, выражение  `System.out.println("Hi")` Будет проигнорировано.
<!--ID: 1757679607523-->


Q: Как включить assertions для класса в Java?
A: Через параметры JVM или методами ClassLoader. Наизусть их знать не обязательно.
<!--ID: 1757679607529-->

Q: Когда выполняются assertions?
A: Они выполняются в том месте кода, где написаны — во время выполнения (runtime).
<!--ID: 1760771717425-->

