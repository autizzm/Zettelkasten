
Theory for the cards: [[Static Nested Classes Java]]

FILE TAGS: java_core

Q: Чем отличается поведение static nested класса от top-level класса?
A: 1. Static Nested класс имеет доступ к static переменным Enclosing класса (даже private)
2. Вне Enclosing класса для обращения к static nested классу нужно использовать полное имя: `Enclosing.StaticNested`
<!--ID: 1757496289891-->


Q: Нужен ли для создания static nested класса экземпляр Enclosing класса?
A: Нет, не нужен. Static nested класс создается просто по полному имени:
`Enclosing.StaticNested staticNested = Enclosing.StaticNested();`
<!--ID: 1757496289899-->


Q: Зачем нужны static nested классы? 
A: static nested класс - это обычный top-level класс "упакованный" в другой класс по смыслу, ему не требуется ссылка на объект внешнего класса
<!--ID: 1757496289902-->


Q: Чем static nested классы отличаются от inner классов?
A: Static nested классы, в отличие от inner классов, не требуют ссылки на объект Enclosing класса -> не могут вызывать его методы напрямую и не видят его полей.
Static Nested класс не требут экземпляра Enclosing класса для создания.
<!--ID: 1757496289906-->


Q: Может ли static nested класс реализовывать интерфейс?
A: Да, может. Он может все, что могут top-level классы.
<!--ID: 1757496289910-->


Q: Может ли static nested класс расширять top-level класс?
A: Да, может. Он может все, что могут top-level классы.
<!--ID: 1757496289913-->


Q: Может ли top-level класс расширять static nested класс?
A: Да, может. Он может все, что могут top-level классы.
<!--ID: 1757496289917-->


Q: Расскажи об области видимости static nested классов и их конструкторов? Чем она отличается от области видимостей Inner классов? 
A: С видимостью те же приколы, что и у Inner:
>1. **public class** -> **public default constructor** - виден везде
>2. **protected class** -> **protected defaul**t constructor (относительно самого static nested класса) - сам static nested class виден и в пакете и в подклассах Enclosing класса, а вот его конструктор виден и в пакете и в подклассах static nested класса
>3. **no modifier class** -> **no modifier default** constructor - и класс и конструктор видны только в этом пакете
>4. **private class** -> **private default constructor** - видны только внутри класса (конструктор static nested тоже виден только внутри самого static nested класса -> нужен factory method, или не ебать себе мозги и самому определить конструктор)
<!--ID: 1757496289921-->
