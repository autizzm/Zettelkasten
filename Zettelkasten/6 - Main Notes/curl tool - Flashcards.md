
Theory for the cards: [[curl tool]] 

FILE TAGS: java+

Q: Как при отправке сообщения через curl указать header? А если их несколько?
A: Header указывается как `-H "key: value"`, если их несколько, то просто пишем несколько раз:
```java
curl --request POST
--verbose //подробный output
-H "key: value"
-H "key1: value1"
--data "{\"msg\": \"value\"}"
-L "https://example.com/ping"
```
<!--ID: 1758793986922-->


Q: Как при отправке сообщения через curl указать тело запроса?
A: с помощью `--data "the data"`:
```java
curl --request POST
--verbose //подробный output
-H "key: value"
-H "key1: value1"
--data "{\"msg\": \"value\"}"
-L "https://example.com/ping"
```
<!--ID: 1758793986929-->


Q: Как при отправке GET запроса с помощью curl указать аргументы (которые вставляются в url)?
A: Используем разделители url:
- `?` - означает конец url и начало аргументов
- `&` - разделитель аргументов
```java
curl --request GET
--verbose //подробный output
-H "key: value"
-H "key1: value1"
//символ ? - означает конец uri и начало данных
// символ & - разделитель данных:
-L "https://example.com/calculate/poll?CIK=0000320193&calcType=CALC_EBITDA"
```
<!--ID: 1758793986938-->
