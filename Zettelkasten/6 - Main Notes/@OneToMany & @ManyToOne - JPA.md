
29-10-2025 07:55

Status:

Tags:

---
# @OneToMany & @ManyToOne - JPA

**Reminder:**
	Владелец отношения - Child, т.к. он имеет FOREIGN KEY, указывающий на Parent запись (из другой таблицы). Пдробнее тут ([[One to Many relationship - БД]])



![[Pasted image 20251029075652.png]]


### Аннотации

#### @ManyToOne

В БД вместо этого поля будет лежать PRIMARY KEY этого объекта. 


#### @JoinColumn(name="person_id", referencedColumnName="id")

тут "person_id" -  имя колонки, содержащей PK объекта Person в таблице Item.

"id" - название колонки в родительской таблице, т.е. он ссылается на колонку "id" в таблице Person.



#### @OneToMany(mappedBy="owner")

Указывается в Parent классе (На который указывает FK).

тут "owner" - имя ПОЛЯ в owner (Child) классе.


---
### Example

```java
import jakarta.persistence.*;
import java.util.List;

@Entity
public class Department {
	
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
	
    private String name;
	
    @OneToMany(mappedBy = "department", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<Employee> employees;
	
    // Getters, setters, toString
}
```


```java
import jakarta.persistence.*;

@Entity
public class Employee {
	
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
	
    private String name;
	
    @ManyToOne
    @JoinColumn(name = "department_id")
    private Department department;
	
    // Getters, setters, toString
}
```


> [!note]
> Вызывать геттер для получения объекта, связанного с merged objectt нужно только внутри транзакции. Т.к. только при вызове геттера выполняется запрос на получение связанных объектов. Т.е. они подтягиваются ==lazily==
> Код транзакции:
> ```java
> session.getTransaction().begin();  
>
>Person person = session.find(Person.class, 15);  
>System.out.println(person);  
>for(Item item : person.getItems()){  
>    System.out.println(item);  
>}  
  >
>session.getTransaction().commit();
> ```
> Output (включено отображение prepared statements by Hibernate):
> ```
> Hibernate: select p1_0.id,p1_0.age,p1_0.email,p1_0.name from Person p1_0 where p1_0.id=?
> 
>Person{id=15, name=Adam, age=35, email=wiccaphase@gmail.com}
>
>Hibernate: select i1_0.person_id,i1_0.id,i1_0.item_name from Item i1_0 where i1_0.person_id=?
>
>Item{id=22, person=Person{id=15, name=Adam, age=35, email=wiccaphase@gmail.com}, name='Guitar'}
>Item{id=23, person=Person{id=15, name=Adam, age=35, email=wiccaphase@gmail.com}, name='Mic'}
>Item{id=24, person=Person{id=15, name=Adam, age=35, email=wiccaphase@gmail.com}, name='Golden record award'}
> ```



----
#### [[@OneToMany & @ManyToOne - JPA - Flashcards|Link to flashcards]]



---
### References:

- [[One to Many relationship - БД]]