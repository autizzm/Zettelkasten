
Theory for the cards:  [[String Java]]

FILE TAGS: java_core

Q: Изменяемы ли строки в Java?
A: Нет. Любые операции, изменяющие строку, создают новый объект String, а не изменяют существующий.
<!--ID: 1756732182288-->


Q: Каким методом можно получить длину строки String
A: .length()
<!--ID: 1756732182295-->

Q: Какой метод String позволяет отформатировать строку аналогично `System.out.println()` и `PrintStream.format()`?
A: `String.format(String str, Object args )`
<!--ID: 1756732752055-->

Q: Получим ли мы копию строки, если вызовем её метод .toString()?
A: Нет, someStr.toString() вернет ссылку на самого себя. БЕСПОЛЕЗНЫЙ МЕТОД для объектов String.
<!--ID: 1756736143035-->


Q: Является ли метод .valueOf() статическим?
A: Да, это статический метод классов String и подклассов Number
<!--ID: 1756736143046-->


Q: Как получить подстроку объекта String?
A: `.substring( int beginIndex, int endIndex)` 
<!--ID: 1756736143054-->


Q: Как проще всего разделить строку str = "word1&word2&word3" на массив строк `[word1, word2, word3]`?
A: используя функцию `str.split( String regEx);`
<!--ID: 1756736143063-->


Q: Как проще всего соединить список myListOfStrings (`Iterable<? extends CHarSequence>`) в строку через символ "&"?
A: `String newStr = String.join ( ",", myListOfStrings)`
<!--ID: 1756736143072-->


Q: При вызове `String newStr = String.join ( ",", List.of("word1", "word2", "word3"));` будет ли полученный newStr заканчиваться симолом ","?
A: Нет
<!--ID: 1756736143081-->


Q: Какие есть методы класса String для приведения строки к верхнему и нижнему регистру?
A: `toLowerCase()`, `toUpperCase()`
<!--ID: 1756736143088-->


Q: Как проверить наличие подстроки "ab" в строке `String str = "dsgabs";`?
A: `str.contains("ab"); //returns boolean`
<!--ID: 1756736143096-->


Q: как получить индекс первого вхождения символа/подстроки (`searchedSubString`) в строку str? 
A: `str.indexOf( searchedSubString ) //returns int index` 
<!--ID: 1756736143104-->


Q: как получить индекс последнего вхождения символа/подстроки (`searchedSubString`) в строку str? 
A: `str.lastIndexOf( searchedSubString ) //returns int index` 
<!--ID: 1756736143111-->

Q:  каким образом проще всего обрезать предшествующий пробел и пробел в конце строки (которые могут быть в строке, а может и не быть) Превратить строки типа " push", "close", "stop " в "push", "close", "stop"?
A: используя метод `myString.trim()`
<!--ID: 1756738211481-->
