
12-09-2025 10:20

Status: #baby 

Tags: [[Java Core]] [[Small Important Moments Java]]

---
# assert Java

Предназначен для отладки/диагностики, а не для управления бизнес-логикой.

>[!note]
>При цикличном вызове класса, до того как класс инициализирован в нем включены assertions ([[Detailed Initialization Procedure - Java|Порядок инициализации]]):
>```java
>public class Foo {
 >   public static void main(String[] args) {
 >       Baz.testAsserts(); 
 >       // Will execute after Baz is initialized.
>    }
>}
>class Bar {
 >   static {
>        Baz.testAsserts(); 
 >       // Will execute before Baz is initialized! 
 >       //(т.к. сначала надо инициализировать Bar, т.к. он Parent класс Baz)
>    }
>}
>class Baz extends Bar {
 >   static void testAsserts() {
>        boolean enabled = false;
 >       assert  enabled = true;
>        System.out.println("Asserts " + 
>			   (enabled ? "enabled" : "disabled"));
 >   }
>}
>```
>На консоль выведется:
>```
>Asserts enabled
>Asserts disabled
>```




# Что такое `assert` (синтаксис и определение)

`assert` — это **языковая конструкция** (ключевое слово), позволяющая встраивать в код проверку некоторого булевого условия (т. е. выражения, которое должно быть `true`). Есть две формы:

```java
assert expr1; 
assert expr1 : expr2;
```

- `expr1` должен иметь тип `boolean` или `Boolean` (ошибка компиляции иначе).
    
- Если assertion **включена** — вычисляется `expr1`; если `false` — бросается `AssertionError` (см. ниже).
    
- ==Если assertion **выключена** — оператор никак не выполняется (~={red}!!!!=~ ни `expr1`, ни `expr2` не вычисляются ~={red}!!!!=~)==



# Поведение (семантика) 

- ==**По умолчанию assertions выключены==.** При выключенных assertions выполнение оператора `assert` _не влияет_ на программу: оба выражения просто не вычисляются. 
    
- **Когда assertion включена:**
    
    1. Вычисляется `expr1`. Если `expr1` имеет тип `Boolean`, применяется unboxing (§5.1.8).
        
    2. Если `expr1 == true` — ничего не происходит.
        
    3. Если `expr1 == false`:
        
        - если есть `expr2` — он вычисляется, ~={green}его значение используется как «detail message» при создании =~`AssertionError`;
            
        - если `expr2` нет — создаётся `AssertionError` без сообщения;
            
        - затем бросается этот `AssertionError`. 
            

Пример:

```java
int x = -1;
assert x >= 0 : "x must be non-negative";
```

Если assertions включены и `x < 0` — бросится `new AssertionError("x must be non-negative")`. ==Если выключены — код работает как будто `assert` отсутствует.==



# Как включать/отключать assertions

~={red}!!!!=~ ~={yellow}JVM хранит флаг **«assertions enabled/disabled for this class»** в **метаданных класса** (в его `Class` объекте).=~ ~={red}!!!!=~

Есть **несколько способов**:

**a) Параметры запуска JVM (наиболее часто).**

- `-ea` или `-enableassertions` — включить assertions.
    
- `-da` или `-disableassertions` — отключить.  
    Эти опции поддерживают **градации**: глобально, для пакета (с `...` означающим под-пакеты), или для отдельного класса. Примеры:
    

```bash
# включить во всех классах 
java -ea MyMain  

# включить только в конкретном пакете и подпаках 
java -ea:com.example... MyMain  

# включить только для одного класса 
java -ea:com.example.MyClass MyMain  

# выключить system classes (пример) 
java -da:java.* -ea com.example.Main
```


(Синтаксис и поведение описаны в документации по включению/отключению assertions.) [Oracle Documentation](https://docs.oracle.com/cd/E19683-01/806-7930/6jgp65ikq/index.html?utm_source=chatgpt.com)

**b) Программно через `ClassLoader`.**  
API `ClassLoader` предоставляет методы:

- `setDefaultAssertionStatus(boolean enabled)` — по умолчанию для всех классов, загруженных этим загрузчиком;
    
- `setPackageAssertionStatus(String packageName, boolean enabled)` — для пакета;
    
- `setClassAssertionStatus(String className, boolean enabled)` — для конкретного класса;  
    Замечание: эти установки **не влияют** на уже инициализированные классы (т. е. если класс уже инициализирован, поменять статус для него нельзя). [cr.openjdk.org](https://cr.openjdk.org/~jlaskey/templates/docs/api/java.base/java/lang/ClassLoader.html?utm_source=chatgpt.com)

----
#### [[assert Java - Flashcards|Link to flashcards]]



---
### References:

