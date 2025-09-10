
09-09-2025 12:34

Status: #baby 

Tags: [[Java Core]] [[Java Classes]]

---
# Local classes Java

Local class - класс, поределенный в блоке (метод, конструктор,if-else, for, while,...) внешнего класса.

>[!warning]
>Имеет доступ к полям внешнего класса

> [!note]
> 1. Не могут быть static
> 2. Не могут иметь acess modifier


>[!note]
>Локальными могут быть и ~={yellow}Enum=~ классы и ~={yellow}record=~ классы и ~={yellow}Интерфейсы=~: они неявно ~={orange}static=~ -> не имеют доступа к полям Enclosing класса.
>
>Но ~={yellow}обычные local классы=~ - ~={orange}НЕ static=~ -> поэтому имеют доступ к полям Enclosing класса

Local class или local interface - не является частью пакета, Enclosing класса или интерфейса.

Local class имеет [[Fully qualified vs Simple name - Java|simple name]]


### Scope of Local class:

1. В одном и том же scope (блок, где он объявляется) нельзя создать два Local класса с одинаковым именем.
2. Нельзя создавать "cyclic declaration": класс с тем же именем внутри Local класса.

```java
class Global {
    class Cyclic {}

    void foo() {
        new Cyclic(); // create a Global.Cyclic
        class Cyclic extends Cyclic {} // circular definition (the same name)

        {
            class Local {}
            {
                class Local {} // compile-time error
            }
            class Local {} // compile-time error
            class AnotherLocal {
                void bar() {
                    class Local {} // ok
                }
            }
        }
        class Local {} // ok, not in scope of prior Local
    }
}
```

Q: Что будет, если к effectively final переменной при инициализации добавить модификатор final?
A: Ничего, она станет явно final.

Q: Что произойдет, если у final переменной убрать модификатор final? Можно ли будет использовать такую переменную там, где можно получать доступ только к final переменным (например, в local классе или лямбда выражениях)?
A: Эта переменная станет effectively final и всё так же её можно будет использовать там, где можно использовать только final.

----
#### [[ |Link to flashcards]]



---
### References:

