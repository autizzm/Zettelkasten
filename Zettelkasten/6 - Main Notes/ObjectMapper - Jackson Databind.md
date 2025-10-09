
25-09-2025 15:23

Status: #baby

Tags: [[Java+]] [[Jackson Databind]]

---
# ObjectMapper - Jackson Databind


## Методы

### Json -> Java object (экземпляр класса)

- `<T> T` ~={green}readValue( `... jsonSource, Class<T> resultClassLIteral`) =~- несколько перегруженных методов, принимающих в качестве `jsonSource`:
		- String
		- File
		- InputStream
		- ....


---

### Java object -> Json

- `void` ~={cyan}writeValue(~={orange}String=~ `endPoint, Object myCLassInstance`)=~
- `void` ~={cyan}writeValue(~={orange}byte[]=~ `endPoint, Object myCLassInstance`)=~  

- `String` ~={cyan}writeValueAs~={orange}String=~(`Object myClassInstance`)
- `byte[]` ~={cyan}writeValueAs~={orange}Bytes=~(`Object myClassInstance`)=~=~ - то же самое, но возвращает результат, а не записывает в переданный аргумент.


---
## Сериализация & десериализация JsonNode

##### Json -> [[JsonNode - Jackson Databind|JsonNode]]

- `JsonNode` ~={green}readTree(`String jsonStr`)=~
- `JsonNode` ~={green}readValue(`String jsonStr,` ~={orange}JsonNode.class=~)=~

##### Java object -> [[JsonNode - Jackson Databind|JsonNode]]

- `JsonNode` ~={green}valueToTree(`Object myClassInstance`)=~

##### JsonNode -> Java object

- `T` ~={green}treeToValue(`JsonNode node, Class<T> resultClassLiteral`)=~




----
#### [[ObjectMapper - Jackson - Flashcards|Link to flashcards]]



---
### References:

- [[Порядок сериализации - Jackson Annotations]]
- [[JsonNode - Jackson Databind]]
