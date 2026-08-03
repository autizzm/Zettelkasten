
01-08-2026 19:36

Status:

Tags: [[Kafka Streams]] [[Kafka]] [[Java+]]

---
# overview & important notables - Kafka Streams


В Kafka Streams нет батчинга: все запросы обрабатываются как отдельные сообщения -> чуть медленнее чем при отправке сообщения через обычные топики.

Kafka Stream - по сути preprocesses the information inside of the microservice before even sending it to Kafka. Streams don't execute on brokers/ Brokers are busy just being Kafka. They're handling messaging. The stream processing work is done inside of your application.

Kafka Stream Processing - обычно встраивается в само приложение: в producer или consumer:

**Паттерн 1: Встроено в producer**
	
```
[Приложение A]
├── Kafka Producer (отправляет сырые данные)
├── Kafka Streams (обрабатывает, считает что-то)
└── Пишет результаты в output topic
        ↓
    [Kafka Topic]
        ↓
   [Consumer приложение]
```
	
**Паттерн 2: Встроено в consumer**
	
```
[Producer приложение]
        ↓
   [Kafka Topic]
        ↓
[Приложение B]
├── Kafka Consumer (читает)
├── Kafka Streams (обрабатывает)
└── Может писать результаты дальше
```
	
**Паттерн 3: Отдельное приложение (только обработка)**
	
```
[Producer] → [Topic1] → [Streams App] → [Topic2] → [Consumer]
```
	
**Но это всё ещё обычное Java приложение**, просто запущенное на вашей машине/сервере. Не требует отдельного кластера.




----
#### [[overview & important notables - Kafka Streams - Flashcards|Link to flashcards]]



---
### References:

