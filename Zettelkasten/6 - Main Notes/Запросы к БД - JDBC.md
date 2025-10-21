
06-10-2025 12:16

Status: #baby

Tags: [[Java+]] [[JDBC]]

---
# Запросы к БД - JDBC


### Statement

Используется для выполнения простых SQL-запросов, которые задаются в виде строки. Каждый запрос передается в базу данных как есть, без предварительной обработки. Это подходит для одноразовых или динамически формируемых запросов, но менее эффективно при повторяющихся вызовах.

```java
try{
	Statement statement = myConnection.createStatement();
	
	// для SELECT запросов:
	ResultSet resultSet = statement.execuTeQuery("SELECT * FROM myTable");
	
	//UPDATE запрос:
	int amtLinesAffected = statement.executeUpdate("UPDATE myTable SET my_field = 0 WHERE myField IS NULL;");
	
	//универсальный метод для интераактивного создания запросов
	... = statement.execute("my query") 	
} catch(SQLException e){
	...
}
```
##### [[ResultSet - JDBC]]
### PreparedStatement

Предназначен для выполнения параметризованных запросов, где вместо значений используются плейсхолдеры (?). Эти запросы предварительно компилируются на стороне базы данных, что ускоряет их выполнение при многократных вызовах и снижает нагрузку на сервер.

>[!note]
>PreparedStatement - защищён от SQL-инъекций

```java
try{
	PreparedStatement preparedStatement = myConnection.prepareStatement("INSERT INTO students (name, id) VALUES(?, ?);");
	
	preparedStatement.setString(1, "student_name");
	preparedStatement.setInt(2, 85101);
	
	
	int amtLinesAffected =preparedStatement.executeUpdate();
	//или
	ResultSet resultSet = statement.executeQuery(); //если должны возвращаться какие-то строки
	
} catch(SQLException e){
	...
}
```

> [!warning]
> Для `INSERT`, `UPDATE`, `DELETE` нужно использовать **`executeUpdate()`**, потому что они не возвращают `ResultSet`, а только число изменённых строк.

### CallableStatement

нужна для вызова хранимых в БД функций и процедур

```java
CallableStatement cs = myConnection.prepareCall("{call getStudentsTicketNum(?)}");

cs.registerOutParameter(1, java.sql.Types.Integer);

cs.execute();

//получение результата
int studentTicketNum = cs.getInt(1);
```


### Batch commands

Позволяют отправлять для исполнения в БД сразу несколько запросов.

```java
Statement statement = connection.createStatement();
statement.addBatch("INSERT INTO EMPLOYEE(ID, NAME, DESIGNATION) "
 + "VALUES ('1','EmployeeName','Designation')");
statement.addBatch("INSERT INTO EMP_ADDRESS(ID, EMP_ID, ADDRESS) "
 + "VALUES ('10','1','Address')");
statement.executeBatch();
```


> [!warning] **Statements need to be closed**
> ```java
> Statement myStatement = connection.createStatement();
> ...
> myStatement.close();
> ```

----
#### [[Запросы к БД - JDBC - Flashcards|Link to flashcards]]



---
### References:

- [[ResultSet - JDBC]]
