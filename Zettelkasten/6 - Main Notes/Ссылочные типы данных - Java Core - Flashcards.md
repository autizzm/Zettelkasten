
Theory for the cards: [[Ссылочные типы данных - Java Core]]

FILE TAGS: java java_core

Q: Являются ли массивы в Java наследниками Object?
A: любой массив в Java — это полноценный объект, который наследует `Object` и реализует `Cloneable` и `Serializable`; массивы ссылочных типов ковариантны (`String[]` можно присвоить `Object[]`), а массивы примитивов — нет.
<!--ID: 1785857946652-->
