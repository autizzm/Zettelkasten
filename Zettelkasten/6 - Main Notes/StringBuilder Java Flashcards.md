
Theory for the cards: [[StringBuilder Java Flashcards]]

FILE TAGS: java_core

Q: Является ли последовательность символов, хранимая в StringBuilder, изменяемой?
A: Да
<!--ID: 1756738211438-->


Q: Какая коллекция под капотом StringBuilder?
A: `char[]`
<!--ID: 1756738211451-->


Q: Как проще всего разернуть строку (str)?
A: `String reversedStr = new StringBuilder(str).reverse().toString();`
<!--ID: 1756738211459-->


Q: Что произойдет при попытке использования объекта StringBuilder после того как из него была получена строка методом toString()?
A: Ничего особенного
<!--ID: 1756738211466-->


Q: Изменится ли строка 'str' здесь: `String str = myStrBuilder.toString(); myStrBuilder.append('O');` Добавится ли к строке 'str' символ 'O'?
A: Нет
<!--ID: 1756738211473-->
