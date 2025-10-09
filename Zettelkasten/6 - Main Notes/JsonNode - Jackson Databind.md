
08-10-2025 12:22

Status: #baby

Tags: [[Java+]] [[Jackson Databind]]

---
# JsonNode - Jackson Databind

~={orange}JsonNode - это интерфейс=~ ~={red}!!!!!=~

```kotlin
JsonNode (interface)
 ↑
BaseJsonNode (abstract class)
 ↑
ContainerNode (abstract class)
 ↑                      ↑
ObjectNode (class)   ArrayNode(class)
```
---

### [[ObjectMapper - Jackson Databind#Сериализация & десериализация JsonNode|Сериализация и десериализация JsonNode]]


---

### Получение значения JsonNode


- `JsonNode` ~={green}get(`String key`)=~ - ключ (из пары ключ значение)
- `JsonNode`~={green} get(`int index`) =~- индекс, если этот JsonNode - массив
	Возвращает ветку по ключу в виде другой JsonNode
	~={red}!!!=~ Если по ключу ничего нет -> возвращает ~={cyan}null ~={red}!!!=~


- `JsonNode`~={green} at(`String path`)=~ 
	~={red}!!!=~ Если по ключу ничего нет ->возвращает ~={cyan}JsonNode для которого isMissingNode() - true=~ ~={red}!!!=~
	
Пример:
```java
JsonNode nameNode = myJsonString.at("/identification/name");

//myJsonString:
{
	"identification": {
		"name": "John",
		"ssn": "ABC1234"
	}	
}

JsonNode nameNode = myJsonString.at("/candidates/0/content/parts/0/text");

//myJsonString
{
	"candidates": [ 
		{
			"content" : {
				"farts": [ 
					{
						"text": "your response"
					} 
				] 
			}  
		} 
	]
}

```

---
## Unwrapping JsonNode

- `String` ~={green}asText()=~
- `String` ~={green}asText(`String defaultValue`)=~


- `Integer`~={green} asInt()=~
- `Integer`~={green} asInt(`Integer defaultValue`)=~

$\dots$
- ~={green}asXXX()=~
- ~={green}asXXX(`XXX defaultValue`)=~

---

### Проверка нулевого JsonNode

- `boolean`~={green} isNull()=~ - распознает JsonNode созданный из пустого json ~={yellow}{ }=~
- `boolean`~={green} isMissingNode()=~ - распознаёт пустую строку ~={yellow}""=~


### Прохождение JsonNode:

```java
public static void traverse(JsonNode root){
	if(root.idObject()){
		Iterator<String> fieldName = root.fieldNames();
		while(fieldName.hasNext()){
			String fieldName = fieldName.next();
			JsonNode fieldValue = root.get(fieldName);
			traverse(fieldValue); //рекурсивный вызов
		}
	} else if(root.isArray()){
		ArrayNode arrayNode = (ArrayNode) root;
		for(int i = 0; i < arrayNode.size(); i++){
			JsonNode arrayElement = arrayNode.get(i);
			traverse(arrayElement); //рекурсивный вызов
		}
	} else {
		//JsonNode root is a single value field 
		//здесь полезная нагрузка, получаем это значение и делаем с ним, что надо
	}
}
```


----
#### [[JsonNode - Jackson Databind - Flashcards|Link to flashcards]]



---
### References:

- [[ObjectMapper - Jackson Databind]]