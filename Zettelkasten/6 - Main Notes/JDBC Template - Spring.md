
21-10-2025 12:26

Status: #not_even_born

Tags: [[JDBC]] [[Spring]] [[Java+]]

---
# JDBC Template - Spring

Импорт:
```xml
<dependency>  
  <groupId>org.springframework</groupId>  
  <artifactId>spring-jdbc</artifactId>  
  <version>${spring.version}</version>  
</dependency>
```


Конфигурация в файле ApplicationContext (его [[Configuration via Java code - Spring|Java class альтернативе]]):

```java
@Bean  
public DataSource dataSource(){  
    DriverManagerDataSource dataSource = new DriverManagerDataSource();  
  
    dataSource.setDriverClassName("org.postgresql.Driver");  
    dataSource.setUrl(env.getProperty("db.url"));  
    dataSource.setUsername(env.getProperty("db.user"));  
    dataSource.setPassword(env.getProperty("db.password"));  
  
    return dataSource;  
}  
  
@Bean  
public JdbcTemplate jdbcTemplate(){  
    return new JdbcTemplate(dataSource());  
}
```


### Отправка запросов к БД с помощью JdbcTemplate:

С JdbcTemplate:
```java
public List<Person> index(){  
    return jdbcTemplate.query("SELECT * FROM people", personMapper);  
}
```

Без JdbcTemplate:
```java
public List<Person> index(){  
    List<Person> people = new ArrayList<>();  
    try {  
        Statement statement = connection.createStatement();  
        ResultSet resultSet = statement.executeQuery("SELECT * FROM people;");  
		  
        while(resultSet.next()){  
            Person person = new Person();  
            person.setId(resultSet.getInt("id"));  
            person.setName(resultSet.getString("name"));  
            person.setAge(resultSet.getInt("age"));  
            person.setEmail(resultSet.getString("email"));  
            people.add(person);  
        }  
    } catch (SQLException e){  
        throw new RuntimeException(e);  
    }  
	  
    return people;  
}
```

> [!note]
> В JdbcTemplate всегда по умолчанию используется [[Запросы к БД - JDBC#PreparedStatement|PreparedStatement]]

#### Вставка значений в Statement:

```java
public Person findPerson(int id){  
    return jdbcTemplate.query("SELECT * FROM people WHERE id=?", new Object[] {id}, personMapper).getFirst();  
}
```


#### UPDATE & DELETE:

```java
public void update(int id, Person person){  
    jdbcTemplate.update("UPDATE person SET name=?, age=?, email=? WHERE id=?", person.getId(), person.getAge(), person.getEmail(), person.getId());  
}  
  
public void delete(int id){  
    jdbcTemplate.update("DELETE FROM people WHERE id = ?", id);  
}
```



> [!note] **Другой синтаксис вставки аргументов в statement**
> - в query - через `Object[]`
> - в update & delete - через varargs

----
#### [[JDBC Template - Spring - Flashcards|Link to flashcards]]



---
### References:

- [[JDBC Template - RowMapper - Spring]]