
20-08-2026 10:18

Status:

Tags: [[Kafka]] [[Java+]]

---
# Kafka vs RabbitMQ


## Kafka vs RabbitMQ — шпаргалка перед собеседованием

### 1. Главная идея


| |**Kafka**|**RabbitMQ**|
| --- | --- | --- |
|Основная модель|Distributed log|Message broker / queue|
|Главная абстракция|**Topic → Partition**|**Exchange → Queue**|
|Сообщение|Хранится в log|Обычно удаляется после ACK|
|Чтение|Consumer сам отслеживает offset|Broker доставляет сообщение consumer'у|
|Повторное чтение|**Да**, просто переместить offset|Обычно нужно заново отправить сообщение|
|Масштабирование|Через partitions|Через queues / consumers|
|Основное применение|Event streaming, большие потоки данных|Task queue, routing, RPC, асинхронные команды|


**Мнемоника:**

> **Kafka = "журнал событий, который можно читать сколько угодно раз"**  
> **RabbitMQ = "брокер, который раздаёт сообщения потребителям"**

---

# 2. Пример

### Kafka

Есть событие:

```text
OrderCreated(orderId=123)
```

Оно попадает в:

```text
orders topic
    partition 0
        offset 100 → OrderCreated(123)
        offset 101 → OrderCreated(124)
```

Его могут независимо прочитать:

```text
billing-service
analytics-service
notification-service
```

Каждый consumer group имеет **свой offset**.

То есть одно событие фактически может быть обработано множеством независимых систем.

---

### RabbitMQ

```text
Producer
   ↓
Exchange
   ↓
 ┌─────────────┐
 │   Queue     │
 └─────────────┘
      ↓
 Consumer
```

Сообщение попало в queue → consumer обработал → `ACK` → сообщение удалено.

Если нужно, чтобы два сервиса получили сообщение, обычно создаются **две очереди**:

```text
             ┌─ Queue A → Service A
Exchange ────┤
             └─ Queue B → Service B
```

---

# 3. Routing

### Kafka

Routing в основном определяется:

```text
topic + partition
```

Например:

```java
key = customerId
```

Тогда события одного customer попадут в одну partition:

```text
customer 42 → partition 3
customer 42 → partition 3
customer 42 → partition 3
```

→ сохраняется порядок **внутри partition**.

---

### RabbitMQ

Routing — одна из сильных сторон RabbitMQ.

Есть:

```text
Exchange
```

и разные типы:

- **Direct** — exact routing key
    
- **Topic** — pattern matching
    
- **Fanout** — всем queues
    
- **Headers** — routing по headers
    

Например:

```text
order.created.eu
order.created.us
order.cancelled.eu
```

Topic exchange может маршрутизировать:

```text
order.created.*
```

---

# 4. Delivery model

### Kafka

Consumer **сам читает** сообщения:

```text
Consumer → fetch → Kafka
```

Kafka не удаляет сообщение после чтения.

```text
offset 100
offset 101
offset 102
offset 103
```

Consumer хранит:

```text
current offset = 103
```

Можно сделать:

```text
seek(100)
```

и перечитать события.

---

### RabbitMQ

Broker **push'ит** сообщения consumer'у:

```text
RabbitMQ → Consumer
```

Consumer отвечает:

```text
ACK
```

После ACK сообщение обычно удаляется из queue.

При:

```text
NACK / connection failure
```

сообщение может быть redelivered.

---

# 5. Replay

Это **одно из главных различий**.

### Kafka

```text
10 11 12 13 14 15
       ↑
     offset
```

Можно сказать:

```text
"Давай снова прочитаем с offset 10"
```

И получить старые события.

Очень удобно для:

- rebuilding state
    
- analytics
    
- event sourcing
    
- исправления consumer'а
    
- новых consumer'ов
    

### RabbitMQ

Такого native replay-механизма как у Kafka нет.

После ACK сообщение исчезло.

Если нужно повторно обработать его — обычно нужно:

- republish
    
- DLQ/retry mechanism
    
- хранить сообщения отдельно
    

---

# 6. Persistence

### Kafka

Сообщения сохраняются в log согласно:

```text
retention
```

Например:

```text
retention = 7 days
```

Даже если consumer всё прочитал, Kafka продолжает хранить данные.

---

### RabbitMQ

Очередь в первую очередь представляет **набор сообщений, ожидающих обработки**.

После:

```text
ACK
```

сообщение обычно исчезает.

Есть durable queues/messages, но это не превращает RabbitMQ в Kafka-style persistent event log.

---

# 7. Consumer groups

### Kafka

Очень важное понятие.

```text
Topic: orders
Partitions: 3

Consumer Group A:
    Consumer 1 → P0
    Consumer 2 → P1
    Consumer 3 → P2
```

Внутри одной consumer group:

> **одна partition → максимум один consumer**

Поэтому максимальный параллелизм группы ограничен количеством partitions.

---

### RabbitMQ

Прямого аналога Kafka consumer group нет.

Можно просто иметь:

```text
Queue
 ↓
Consumer 1
Consumer 2
Consumer 3
```

RabbitMQ распределяет сообщения между consumers.

Это больше похоже на **work queue**.

---

# 8. Ordering

### Kafka

Гарантирует порядок **внутри одной partition**:

```text
P0:
A → B → C → D
```

Но не гарантирует глобальный порядок между partitions:

```text
P0: A C E
P1: B D F
```

---

### RabbitMQ

Очередь также обычно доставляет сообщения в порядке очереди, но гарантии порядка становятся сложнее при:

- нескольких consumers
    
- redelivery
    
- priorities
    
- retries
    

Поэтому Kafka обычно удобнее, если **ordering является фундаментальным требованием**.

---

# 9. Масштабирование

### Kafka

Основной механизм:

```text
Topic
 ↓
Partitions
 ↓
Consumers
```

Например:

```text
Topic = 10 partitions
```

Можно иметь до ~10 параллельно работающих consumers **в одной consumer group**.

Kafka хорошо масштабируется на очень большие throughput.

---

### RabbitMQ

Основной механизм:

```text
Queue
 ↓
Consumers
```

Можно добавлять consumers для обработки сообщений.

Но Kafka изначально архитектурно заточен под **distributed high-throughput streaming**.

---

# 10. Backpressure

### Kafka

Consumer может отставать:

```text
Kafka:
100000 messages

Consumer:
прочитал только до offset 80000
```

Разница:

```text
consumer lag = 20000
```

Сообщения остаются в Kafka.

---

### RabbitMQ

Если consumer не успевает:

```text
Queue:
████████████████████
```

растёт:

```text
queue depth
```

RabbitMQ имеет механизмы вроде:

- prefetch
    
- acknowledgements
    
- flow control
    

---

# 11. Retry

### Kafka

Обычно retry реализуется приложением/архитектурой:

```text
Topic
 ↓
Consumer
 ↓ failure
Retry Topic
 ↓
Consumer
```

или:

```text
DLQ
```

Kafka сам по себе не является retry broker'ом.

---

### RabbitMQ

Retry — более естественный сценарий:

```text
Queue
 ↓
Consumer
 ↓ failure
NACK
 ↓
requeue / DLX
```

Можно использовать:

```text
Dead Letter Exchange
TTL
Delayed retry patterns
```

---

# 12. Dead Letter

### Kafka

Обычно создают отдельный topic:

```text
orders
   ↓
failed
   ↓
orders.DLT
```

DLT — фактически обычный Kafka topic.

---

### RabbitMQ

Есть встроенная концепция:

```text
Dead Letter Exchange (DLX)
```

Сообщение может быть направлено через exchange в DLQ.

---

# 13. Transactions

### Kafka

Есть сильная поддержка producer transactions:

```text
DB
 ↓
Outbox
 ↓
Kafka
```

и Kafka transactions:

```text
Producer
   ↓
Topic A
Topic B
```

можно атомарно записывать несколько Kafka records.

Также Kafka поддерживает **exactly-once semantics** в определённых сценариях.

---

### RabbitMQ

Есть publisher confirms и acknowledgements.

Можно добиться надёжной доставки, но модель Kafka transactions/EOS отличается и особенно хорошо интегрирована с Kafka Streams.

---

# 14. At-most / At-least / Exactly once

### Kafka

Часто встречаются:

```text
at-most-once
at-least-once
exactly-once
```

Но важно:

> **Exactly-once ≠ магическое exactly-once для всей системы.**

Например, Kafka может обеспечить EOS внутри Kafka pipeline, но:

```text
Kafka → HTTP → external DB
```

не становится автоматически exactly-once.

---

### RabbitMQ

Типичный production-подход:

```text
at-least-once
+
idempotent consumer
```

То есть consumer должен спокойно переживать повторную доставку.

---

# 15. Throughput

Упрощённо:

**Kafka:**

> очень большой поток сообщений + последовательная запись + batching.

**RabbitMQ:**

> очень гибкая маршрутизация + низкие latency + очереди.

Поэтому:

```text
Massive event streaming → Kafka
Task/message processing → RabbitMQ
```

Но это **не абсолютное правило**.

---

# 16. Latency

RabbitMQ часто выбирают, когда важна:

> **низкая latency доставки отдельного сообщения**

Kafka тоже может иметь очень низкую latency, но его архитектура больше оптимизирована под:

> **высокий throughput + persistent log**

---

# 17. Типичный use case

### Kafka

```text
UserCreated
OrderCreated
PaymentCompleted
ProductViewed
```

То есть:

> **события, которые интересно хранить и перечитывать.**

---

### RabbitMQ

```text
GeneratePdf
SendEmail
ResizeImage
ProcessPayment
```

То есть:

> **команды / задачи, которые нужно выполнить.**

---

# 18. Kafka vs RabbitMQ — самое важное

|Вопрос|Kafka|RabbitMQ|
|---|---|---|
|Основная модель|Distributed log|Message broker|
|Хранение|Retention|До ACK|
|Replay|**Да**|Нет как основная модель|
|Routing|Partition/key|**Exchange/routing key**|
|Consumer model|Pull|Push|
|Offset|**Да**|Нет Kafka-style offset|
|Consumer group|**Да**|Нет прямого аналога|
|Ordering|**В partition**|В queue, но зависит от consumers/redelivery|
|Retry|Обычно application-level|**DLX/requeue/TTL**|
|DLQ|Topic|**DLQ + DLX**|
|Massive throughput|**Сильная сторона**|Хороший, но не основная цель|
|Task queues|Возможно|**Сильная сторона**|
|Event streaming|**Сильная сторона**|Не основной сценарий|
|Replay / analytics|**Отлично**|Не основной сценарий|
|Complex routing|Ограниченно|**Отлично**|

---

## 🔥 Что сказать на собеседовании

Если спросят **«Kafka или RabbitMQ?»**, хороший короткий ответ:

> **Kafka — это распределённый persistent log, ориентированный на высокий throughput, partitioning, event streaming и возможность replay событий. RabbitMQ — классический message broker, ориентированный на очереди, acknowledgements и гибкую маршрутизацию через exchanges. В Kafka consumer сам управляет offset и может перечитывать историю, а в RabbitMQ сообщение обычно живёт в queue до ACK. Поэтому Kafka чаще выбирают для event-driven архитектуры и потоков событий, а RabbitMQ — для task queues, command processing и сложной маршрутизации.**

### Самые важные 5 различий для запоминания

```text
Kafka                         RabbitMQ
────────────────────────────────────────────
Log                           Queue
Pull                          Push
Offset                        ACK
Replay                        Redelivery
Partition                     Exchange
```

И ещё одна очень полезная формула:

> **Kafka отвечает на вопрос «какие события произошли?»**  
> **RabbitMQ — «кому и какую работу нужно выполнить?»**

----
#### [[Kafka vs RabbitMQ - Flashcards|Link to flashcards]]



---
### References:

