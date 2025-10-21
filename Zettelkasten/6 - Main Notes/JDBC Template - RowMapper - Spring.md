
21-10-2025 16:23

Status: #baby

Tags: [[JDBC]] [[Spring]] [[Java+]]

---
# JDBC Template - RowMapper - Spring

Это интерфейс с единственным методом. 

Мы реализуем этот метод так как хотим считывать объекты.

```java
public class PersonMapper implements RowMapper<Person> {  
    @Override  
    public Person mapRow(ResultSet rs, int rowNum) throws SQLException {  
        Person person = new Person();  
        person.setId(rs.getInt("id"));  
        person.setName(rs.getString("name"));  
        person.setAge(rs.getInt("age"));  
        person.setEmail(rs.getString("email"));  
        return person;  
    }  
}
```

По сути JdbcTemplate итерирует по ResultSet и, если resultSet.next() == true -> вызывает метод реализованного нами RowMapper, возвращая прочитанный объект.

> [!note] 
> Если имена колонок в таблице полностью соответствуют полям класса -> можем использовать готовую реализацию RowMapper:
> ```java
> new BeanPropertyRowMapper<>(Person.class);
> ```


----
#### [[JDBC Template - RowMapper - Spring - Flashcards|Link to flashcards]]



---
### References:

- [[JDBC Template - Spring]]