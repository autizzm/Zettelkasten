
09-09-2025 12:34

Status: #baby 

Tags: [[Java Core]] [[Java Classes]]

---
# Local classes Java

Local class - класс, определенный в блоке (метод, конструктор,if-else, for, while,...) внешнего класса.

>[!warning]
>- Имеет доступ к полям внешнего класса. 
>
>- Имеет доступ только к [[Effectively final - Java|effectively final]] переменным из scope определния (блока кода, где Local класс определен)



> [!note]
> 1. Не могут быть static
> 2. Не могут иметь acess modifier




>[!note]
>Локальными могут быть и ~={green}Enum=~ классы и ~={green}record=~ классы и ~={green}Интерфейсы=~: они неявно ~={orange}static=~ -> не имеют доступа к полям Enclosing класса.
>
>Но ~={green}обычные local классы=~ - ~={orange}НЕ static=~ -> поэтому имеют доступ к полям Enclosing класса

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
                    class Local {} // ok deeper nested classes 
                    // are not considered a part of the outer scope
                }
            }
        }
        class Local {} // ok, not in scope of prior Local
    }
}
```


## Ещё моменты:

>[!note] **static members**
> В локальных классах **~={red}могут быть=~** static поля, методы, static initializer block (короче static members) - ~={red}**начиная с версии Java 16 SE**=~

----
#### [[Local classes Java - Flashcards|Link to flashcards]]



---
### References:

- [[Member Classes Java]]
- [[Inner Classes Java]]
- [[Anonymous classes Java]]
