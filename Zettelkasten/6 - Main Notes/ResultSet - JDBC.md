
06-10-2025 12:49

Status: #not_even_born 

Tags: [[Java+]] [[JDBC]]

---
# ResultSet - JDBC


~={cyan}Default ResultSet is not updatable.=~
+
~={cyan}Has a cursor that moves forward only=~

### Перемещение по ResultSet

~={red}!!!=~ Начальное положение - ~={yellow}перед первым элементом=~ ~={red}!!!=~

- boolean `next()` - перемещает на следующую строку, если больше строк нет - ~={cyan}returns false=~
- boolean `previous()` - перемещает предыдущую строку, если больше строк нет - ~={cyan}returns false=~
- boolean `first()` - moves to the first row of this `ResultSet` object.
- boolean `last()`
- boolean `beforeLast()`
- boolean `afterLast()`

- boolean `absolute(int rowNum)` - Moves the cursor to the given row number in this `ResultSet` object.
- boolean `relative(int rowNum)` - Двигает вперюд или назад (если `rowNum < 0`) на определённое число строк

## Извлечение содержимого строк

```java
ResultSet rs = statement.executeQuery("SELECT * FROM books");

while(rs.next()){
	String isbn = rs.getString(1); //1 - номер столбца
	Double price = rs.getDouble("Price"); // "Price" - имя столбца
	//можно и имя столбца и номер
}
```

### Настройка ResultSet

ResultSet можно настроить:
- чтобы он обновлялся
- чтобы через изменения в нём можно было менять данные в БД


Всё это чекай в [документации]()

----
#### [[ResultSet - JDBC - Flashcards|Link to flashcards]]



---
### References:

- [[JDBC setup]]