
Theory for the cards:  [[Arrays class]]

FILE TAGS: java_core

Q: как можно заполнить массив определенным значением?
A: `Arrays( myArray, "0");`
<!--ID: 1756747340783-->


Q: Как проще всего отсортировать массив?
A: `Arrays.sort(myArray);`
<!--ID: 1756747340795-->


Q: как создать Stream из массива?
A:  `stream( T[] a)`
<!--ID: 1756747340804-->


Q: как создать Stream из части массива?
A: `stream(T[], int from, int to)`
<!--ID: 1756747340812-->


Q: Можно ли добавлять/удалять элементы из List полученного методом Arrays.asList(myArray) или List.of(myArray)?
A: Нет, объекты, полученные этими методами, являются FIX-SIZED.
<!--ID: 1756747898299-->


Q: как получить List из массива?
A: `Arrays.asList(myArray)` или `List.of(myArray)`
<!--ID: 1756747340820-->

