
25-09-2025 19:20

Status: #baby

Tags: [[Java+]] [[Jackson Annotations]]

---
# Class-level annotations - Jackson Annotations


## Сериализация

### Нейминг

~={green}@JsonRootName=~ - для определения имени корневого эдемента:
	
```java
@JsonRootName("writer")
class Author{
	...
}

// result json: 
{"writer" : {"field" : "value", ...}}
```
	

### Property ordering

~={green}@JsonPropertyOrder =~- определяет порядок полей в result json

```java
@JsonPropertyOrder(value = {"items", "name", "id" })
public class MyBean {
List<String> items = List.of("it1", "it2");
	public int id; 
	public String name;
}

//result json:
{"items": ["it1", "it2"], "id": 101, "name": "Jake"}
```
	
	

### Property Inclusion Annotations

~={green}@JsonIgnoreProperties=~ — список свойств, которые следует исключить. 
(и при сериализации и при десериализации)

```java
@JsonIgnoreProperties({ "id" })
public class BeanWithIgnore {
    public int id;
    public String name;
}

//проигнорирует поле id при сериализации
//при десериализации также проигнорирует поле "id" в json строке
```
	
	
~={green}@JsonIncludeProperties=~ - устанавливает список полей которые включаются в json (остальные - не включаются):
	
```java
@JsonIncludeProperties({ "name" })
public class BeanWithInclude {
    public int id;
    public String name;
    
    //rewsult json:
    {"name": "Jake"} // - id не сериализуется
}
```
	

~={green}@JsonIgnoreType =~— помечает весь класс как игнорируемый.
	
```java
public class User { // при сериализации User Inner класс Name будет полностью проигнорирован 
    public int id;
    public Name name;

    @JsonIgnoreType
    public static class Name {
        public String firstName;
        public String lastName;
    }
}
```
	

~={green}@JsonIgnore =~- помечает **поле**, **геттер** или **сеттер**


### Автодетекция членов класса


~={green}@JsonAutoDetect=~ -  устанавливает видимость полей для автоматической сериализации:
варианты видимости:
- ANY - видит **любой** элемент (независимо от модификатора доступа)
- NON_PRIVATE - видит всё, кроме **private** членов
- PROTECTED_AND_PUBLIC - видит только **protected** и **public**
- NONE - выключает детекцию для указанного типа членов
- DEFAULT - ???

Параметры этой аннотации:
- `fieldVisibility`
- `getterVisibility`
- `isGetterVisibility`
- `setterVisibility`
- `creatorVisibility` - управляет видимостью конструкторов и фабричных методов
	
Пример:
```java
import com.fasterxml.jackson.annotation.JsonAutoDetect;
import static com.fasterxml.jackson.annotation.JsonAutoDetect.Visibility;

@JsonAutoDetect(fieldVisibility = Visibility.ANY,
                getterVisibility = Visibility.NONE,
                isGetterVisibility = Visibility.NONE,
                setterVisibility = Visibility.NONE,
                creatorVisibility = Visibility.NONE)
class Person {
    private String id;
    private String name;
}

```





> [!note] **Что автодетектится**
> Автоматически детектятся:
> 
> 1. **public getters** (`getXxx()` и `isXxx()` для boolean)
> 2. class members
> 3. Setters + constructors

> [!warning] 
> ***transint*** поля - игнорируются !!!



----
#### [[Class-level annotations - Jackson Annotations - Flashcards|Link to flashcards]]



---
### References:

