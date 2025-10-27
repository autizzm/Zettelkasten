
27-10-2025 19:45

Status: #baby 

Tags: [[Hibernate & JPA]]

---
# Hibernate set up

Зависимости:
```xml
<!-- JDBC driver for Postgresql-->
<dependency>  
  <groupId>org.postgresql</groupId>  
  <artifactId>postgresql</artifactId>  
  <version>42.7.8</version>  
</dependency>  
<dependency>  
  <groupId>org.hibernate.orm</groupId>  
  <artifactId>hibernate-core</artifactId>  
  <version>7.0.0.Final</version>  
</dependency>
```



### Автоматическая подставновка настроек через hibernate.properties file:

hibernate.properties
```properties
hibernate.driver.class=org.postgresq.Driver  
hibernate.connection.url=jdbc:postgresql://localhost:5432/people_database_2  
hibernate.connection.user=postgres  
hibernate.connection.password=root  
  
hibernate.dialect=org.hibernate.dialect.PostgreSQLDialect  
hibernate.show_sql=true  
hibernate.current_session_context_class=thread
```


Файл hibernate.properties автоматически читается при создании Configuration класса:

```java
Configuration configuration = new Configuration().addAnnotatedClass(Person.class);  
  
SessionFactory sessionFactory = configuration.buildSessionFactory();  
  
Session session = sessionFactory.openSession();  
  
try {  
  
    //сохраняем значения  
    session.beginTransaction();  
    for (int i = 0; i < 5; i++) {  
        session.persist(new Person(i, "name-" + i, i, "some email"));  
    }  
    session.getTransaction().commit();  
  
    // performing the search  
    session.getTransaction().begin();  
    Person person = session.find(Person.class, 1); //looking for person with id = 1  
    System.out.println(person);  
    session.getTransaction().commit();  
} finally {  
    session.close();// закрываем сессию вручную, т.к. получили её через openSession() (управление сессией вручную)  
    sessionFactory.close();  
}
```


----
#### [[Hibernate set up - Flashcards|Link to flashcards]]



---
### References:

