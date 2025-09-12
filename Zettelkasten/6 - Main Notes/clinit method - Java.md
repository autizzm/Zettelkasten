
11-09-2025 21:19

Status: #baby 

Tags: [[Java Core]] [[Under the hood - Java]]

---
# clinit method - Java

(JLS §12.4.2) [Link to JLS](https://docs.oracle.com/javase/specs/jls/se21/html/jls-12.html#jls-12.4.2)


`<clinit>` — это **служебный метод**, который компилятор генерирует для каждого класса или интерфейса в Java, ~={green}если=~ в нём ~={green}есть=~ ~={orange}статические инициализаторы=~ (поля со значениями или `static {}`-блоки).



## Порядок выполнения static initializers в `<clinit>` 

Все статические инициализаторы (и поля, и блоки) выполняются **в текстовом порядке**, т.е. в том порядке, в котором они написаны в исходном коде.


Пример:

```java
class Example {
    static int x = initX();
    static {
        System.out.println("block 1");
    }
    static int y = initY();
    static {
        System.out.println("block 2");
    }

    static int initX() { System.out.println("initX"); return 1; }
    static int initY() { System.out.println("initY"); return 2; }
}
```

`javap -c Example` покажет в `<clinit>` примерно такой порядок:

```java
0: invokestatic  #2  // initX()
3: putstatic     #3  // x
6: getstatic     #4  // System.out
9: ldc           #5  // "block 1"
...
15: invokestatic #6  // initY()
18: putstatic    #7  // y
21: getstatic    #4  // System.out
24: ldc          #8  // "block 2"
...
30: return
```

Вывод при загрузке класса будет:

```
initX
block 1
initY
block 2
```

---

### 📌 Когда он вызывается?

- Вызов происходит **при инициализации класса** (см. **JLS §12.4.1**).
    
- Его вызывает JVM **один раз** на класс (или интерфейс).
    
- Гарантируется, что перед первым доступом к любому статическому полю или вызовом статического метода `<clinit>` будет выполнен.
    

---

### 📌 Важные свойства

- `<clinit>` не принимает аргументов и всегда `void`.
    
- Если в классе нет статических инициализаторов — метода просто нет.
    
- Исключение, брошенное в `<clinit>`, заворачивается в `ExceptionInInitializerError`.
    
- Выполнение `<clinit>` синхронизируется: если несколько потоков одновременно инициализируют класс, то метод выполнится только один раз.

----
#### [[clinit method - Java - Flashcards|Link to flashcards]]



---
### References:

- [[Initializer; constants & just static finals - Java]]
- [[Detailed Initialization Procedure - Java]]