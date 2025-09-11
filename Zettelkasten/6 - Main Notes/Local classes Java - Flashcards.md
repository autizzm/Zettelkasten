
Theory for the cards: [[Local classes Java - Flashcards]]

FILE TAGS: java_core

Q: Что будет, если к effectively final переменной при инициализации добавить модификатор final?
A: Ничего, она станет явно final.
<!--ID: 1757520744101-->


Q: Что произойдет, если у final переменной убрать модификатор final? Можно ли будет использовать такую переменную там, где можно получать доступ только к final переменным (например, в local классе или лямбда выражениях)?
A: Эта переменная станет effectively final и всё так же её можно будет использовать там, где можно использовать только final.
<!--ID: 1757520744107-->

Q: Каков scope определения Local класса? К каким переменным он имеет доступ?
A: Область видимости Local класса - блок в котором он определен (метод, конструктор,if-else, for, while,...). 
Local класс имеет доступ к полям внешнего класса (даже private) (ЕСЛИ ОПРЕДЕЛЕН В не СТАТИЧНОМ КОНТЕКСТЕ) и к effectively final переменным блока, где он определен. 
ЕСЛИ определен в статичном контексте (например, static метод), то имеет доступ только к effectively final и final переменным scope, а также к static переменным внешнего класса (даже private)
<!--ID: 1757576364169-->


Q: Может ли Local класс быть static?
A: Нет, не может.
<!--ID: 1757576364178-->


Q: Какие модификаторы доступа есть у Local классов?
A: Никаких, это бессмысленно, ведь они видны исключительно в блоке кода, где они определены (метод, конструктор, if-else, for, while,...).
<!--ID: 1757576505648-->



Q: Что будет, если создать в одном блоке кода два локальных класса с одинаковым именем?
A: compile-time error
<!--ID: 1757576364184-->


Q: Какие ошибки тут возникнут?
```java
class Global {
    void foo() {
		class Local {}
		{
			class Local {} 
		}
		class Local {} 
		class AnotherLocal {
			void bar() {
				class Local {} 
			}
		}
    }
}
```
A:  
```java
class Global {
    void foo() {
        class SomeLocal{
            class Local {} // scope этого Local класса - вcё тело SomeLocal
            {
                class Local {} // compile-time error, это блок кода,
                // не часть тела предыдущей инициализации Local,
                // но она находится внутри scope, где Local уже есть
            }
            class Local {} // compile-time error
            class AnotherLocal {
                void bar() {
                    class Local {} // ok, deeper nested classes 
                    // are not considered a part of the outer scope
                }
            }
        }
        class Local {} // ok, not in scope of prior Local
    }
}
```
<!--ID: 1757576364190-->


Q: Может ли Local класс иметь static методы?
A: Нет, не может. (А static поля - может).
<!--ID: 1757576364195-->


Q: Может ли Local класс иметь static поля?
A: Да, может. (А static методы - нет).
<!--ID: 1757576364201-->


Q: Если Enum объявлен как локальный класс, может ли он иметь доступ к переменным Enclosing класса?
A: Может, но только к статическим. Локальные Enum, record classes and interfaces - неявно static -> могут обращаться только к static переменным Enclosing класса. 
<!--ID: 1757576364206-->


Q: Может ли локальный интерфейс обращаться к переменным Enclosing класса?
A: Может, но только к статическим. Локальные Enum, record classes and interfaces - неявно static -> могут обращаться только к static переменным Enclosing класса. 
<!--ID: 1757576364212-->

Q: Имеет ли локальный класс конструктор?
A: Да, имеет. Пример:
```java
class Outer {
    void method() {
        class Local {
            int x;
            Local(int x) {   // явный конструктор
                this.x = x;
            }
            void print() {
                System.out.println(x);
            }
        }
        
        Local l = new Local(42); // вызов конструктора
        l.print(); // 42
    }
}
```
<!--ID: 1757576791855-->
