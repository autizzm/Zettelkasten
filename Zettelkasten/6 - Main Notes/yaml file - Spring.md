
10-10-2025 08:58

Status: #not_even_born 

Tags: [[Spring]]

---
# yaml file - Spring


> [!note] **Может иметь несколько ключей верхнего уровня**
> Пример:
> ```yaml
> request:
  timeout-seconds: 15
  prompt: "<some_prompt>"
  gemini-api-key: "<my_api>"
>
redis:
  url: "localhost"
  port: 6379
>
processors:
  amt-to-be-created: 30000
> ```


> [!warning] **В чистом Spring не работает**
> Если использовать `YamlFactory...` - он считывает в непонятном порядке и, если приходится инжектить константы из этого файла -> часто инжектит просто null, т.к. не успевает обработать значения из `yaml` файла до инжекта в бины.
> 
 
~={pink}Мб и есть какое-то решение, но у меня пока нет времени на это=~

----
#### [[yaml file - Spring - Flashcards|Link to flashcards]]



---
### References:

