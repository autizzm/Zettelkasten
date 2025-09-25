
25-09-2025 12:42

Status: #adult 

Tags: [[Java+]]

---
# curl tool

Отправка запроса:


**POST**:
```java
curl --request POST
--verbose //подробный output
-H "key: value"
-H "key1: value1"
--data "{\"msg\": \"value\"}"
-L "https://example.com/ping"
```

**GET**:
```java
curl --request GET
--verbose //подробный output
-H "key: value"
-H "key1: value1"
//символ ? - означает конец uri и начало данных
// символ & - разделитель данных:
-L "https://example.com/calculate/poll?CIK=0000320193&calcType=CALC_EBITDA"
```



----
#### [[curl tool - Flashcards|Link to flashcards]]



---
### References:

- [[HTTP]]
- [[Виды HTTP запросов]]
