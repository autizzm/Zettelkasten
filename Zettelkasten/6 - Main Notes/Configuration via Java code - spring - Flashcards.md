
Theory for the cards: 

FILE TAGS: spring spring_core

Q: Добавит ли Spring класс HelloController, если на конфигурационном классе (отмеченном @Configuration) такде установлена аннотация @ComponentScan("path/to/classes")?
```
path
 |--to
     |--classes
        |--SomeBean
        |--AnotherBean
        |--controllers
            |--HelloController
``` 
A: Да, просканирует, т.к. @ComponentScan сканирует все классы и пакеты по указанному пути.
<!--ID: 1760893165128-->
