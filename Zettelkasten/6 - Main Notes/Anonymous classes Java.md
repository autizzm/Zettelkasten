
09-09-2025 12:35

Status: #baby 

Tags: [[Java Core]] [[Java Classes]]

---
# Anonymous classes Java

Локальный класс без названия; используется только один раз

Частный случай анонимного класса.

## Конструкторы

>[!warning] **Конструкторы объявлять нельзя.**  
Вместо этого можно вызвать только конструктор _суперкласса_ или _интерфейсной реализации_ через `new SuperType(...) { ... }`.
>
>Но ~={red}можно=~ создать ~={red}instance initializer block=~
~={red}=~
```java
public class LocalClassCheck {  
    private int x = 8;  
    
    public void method(){  
        int nonEffectivelyFinal = 5;  
        nonEffectivelyFinal++;  
        int effectFinal = 9;  
        class Local{  
            Local(){}  
              
            Local(String variable){  
                System.out.println("hey, " + variable);  
            }  
              
            public void print(){  
                System.out.println("x="+x);  
            }  
        }  
        
        // вызов конструктора Local(String variable)
        Local anonymous = new Local("Mambo"){  
              ...
        };  
    }  
}
```

## Доступ к Enclosing class members + переменным scope

1. Если Anonymous class объявлен НЕ в static контексте (он считается inner- классом):
   - может обращаться к переменным Enclosing класса


2. Если объявлен в статическом контексте -> не имеет указателя на Enclosing класс

#### Доступ к переменным из scope

Имеет доступ только к [[Effectively final - Java|effectively final]] (и final) переменным.


## this

>[!warning] **this**
>Внутри анонимного класса **this** ссылается на объект~={cyan} самого анонимного класса=~, а не на объект Enclosing класса.
>
>Чтобы обратиться к Enclosing классу - используем полное имя this Enclosing класса:
>```java
>Outer.this.someMethod();
>```


## Acess modifiers и scope

~={orange}Не имеет модификаторов доступа.=~

Область видимости - блок, где Anonymous класс определён (метод, конструктор,if-else, for, while,...) 




## Ещё моменты:

>[!note] **Наследование**
>Может явно наследовать только один класс или интерфейс:
>```java
>...
>{
>	SomeClass anonymous = new SomeClass(){ 
>		...
>	}
>}
>```
>Здесь анонимный класс ~={red}наследует SomeClass=~


>[!note] **Тип и имя анонимного класса**
>~={green}тип=~ - используем тип наследуемого класса (т.к. нет спооба указать конкретный тип анонимного класса -> указываем тип родителя)
>
>~={green}Имя анонимного класса=~ - генерируется компилятором типа `Outer$1`, `Outer$2` и т.д.


> [!note] **static members**
> В анонимных классах **~={red}могут быть=~** static поля, методы, static initializer block (короче static members) - ~={red}**начиная с версии Java 16 SE**=~

----
#### [[Anonymous classes Java Flashcards|Link to flashcards]]



---
### References:

- [[Local classes Java]]
- [[Member Classes Java]]