
25-09-2025 20:37

Status:

Tags: [[Java+]] [[Jackson Annotations]]

---
# Deserialization Annotations - Jackson Annotations

#### Приоритет

1. @JsonCreator
2. @JsonSetter & @JsonAnySetter


---

~={green}@JsonCreator=~ - применяется к конструктору или фабричному методу
	- Используется вместе с ~={green}@JsonProperty=~ дляуказания какие поля json связывать с какими полями класса:
```java
@JsonCreator 
public Student(
@JsonProperty("theName") String name, 
@JsonProperty("id") int rollNo){
	this.name = name; 
	this.rollNo = rollNo; 
}
```
	
	
~={green}@JsonAnySetter=~ - применяется к setter методу, который принимает Map
	(Чтобы вложенный объект десериализовался в Map):
 ```java
 ObjectMapper mapper = new ObjectMapper();
 String json = "{\"name\": \"Jake\", grades : {\"algebra\": 5, \"geometry\": 3}}"
 Student stud = mapper.readValue(json, Student.class)
 ```
 
> [!note] **Нельзя конкретно определить имя поля в json, которое маппим**
> Для ~={green}@JsonAnySetter=~ - **нельзя конкретно определить имя поля в json, которое маппим**:
> 
> ~={green}@JsonAnySetter=~ применяется глобально ко всем “лишним” полям, и указывать имя для него **нельзя**.


~={green}@JsonSetter=~ - явно указывает, что метод является сеттером
	+ можно указать имя поля с которым маппим json:
```java
class Student { 
private int rollNo; 
private String name; 

@JsonSetter("name") 
public void setTheName(String name) { this.name = name; } public void setTheRollNo(int rollNo) {this.rollNo = rollNo;}
}
```


~={green}@JsonEnumDefaultValue=~ - используется для указания **fallback** значения в Enum,
на случай, если при десериализации, значения, указанного в json - нет в Enum
(без такого указания - выкидывается InvalidFormatException)
```java
enum Grade {
    A, B, C,
    @JsonEnumDefaultValue 
    UNKNOWN
}

class Student {
    public String name;
    public Grade grade;
}
```

```java
String json = "{ \"name\": \"Jake\", \"grade\": \"X\" }";

ObjectMapper mapper = new ObjectMapper();
Student s = mapper.readValue(json, Student.class);

System.out.println(s.grade); // UNKNOWN

```


----
#### [[Deserialization Annotations - Jackson Annotations - Flashcards|Link to flashcards]]



---
### References:

