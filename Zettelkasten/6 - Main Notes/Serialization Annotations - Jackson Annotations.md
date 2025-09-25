
25-09-2025 20:07

Status: #baby

Tags: [[Java+]] [[Jackson Annotations]]

---
# Serialization Annotations - Jackson Annotations

#### Приоритет

1. @JsonValue - т.к. возвращает готовый json
2. остальные аннотации методов


---



~={green}@JsonValue=~ - ставится перед методом, который сериализует весь класс
- этот метод может возвращать:
	- Map 
	- List - отформатируется как список: `[ a, b, ...]`
	- String - нужно самому форматировать
 ```java
 @JsonValue 
 public String toString(){ 
	 return "{ name : " + name + " }"; 
 }
 ```



~={green}@JsonAnyGetter=~ - ставится перед методом, возращающим Map, чтобы этот Map был развёрнут как обычные поля.

~={purple}Only one per class=~

~={red}!!!=~ Не заменяет сериализацию всего класса ~={red}!!!=~

```java
class User {
    private String name;
    private Map<String, Object> other = new HashMap<>();

    public User(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    @JsonAnyGetter
    public Map<String, Object> getOther() {
        return other;
    }
}

...

User u = new User("Alice");
u.getOther().put("age", 30);
u.getOther().put("city", "Paris");

System.out.println(new ObjectMapper().writeValueAsString(u));


//result json:
{
  "name": "Alice",
  "age": 30,
  "city": "Paris"
}
```
	
	
	
~={green}@JsonGetter=~ - явно указывает, что метод - является геттером
	+ можно указать имя поля в json:
```java
...
@JsonGetter(value = "msg")
public String getMessage(){...}

//result json:
{
	"msg" : "my message"
}
```


~={green}@JsonRawValue=~ - указывает, что метод возвращает ~={orange}часть=~ json строки -> не надо её дополнительно приводить в json:

Без @JsonRawValue:
```java
{ 
   "name" : "Mark", 
   "rollNo" : 1, 
   "json" : "{\"attr\":false}" 
}
```

С @JsonRawValue:
```java
{ 
   "name" : "Mark", 
   "rollNo" : 1, 
   "json" : {"attr":false} 
}
```

----
#### [[Serialization Annotations - Jackson Annotations - Flashcards|Link to flashcards]]



---
### References:

