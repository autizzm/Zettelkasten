
03-07-2026 15:52

Status:

Tags: [[Kafka]] 

---
# Ретраи Producer - Kafka


# 1. `retries`

Количество повторных попыток отправки.

```properties
spring.kafka.producer.retries=10
```

или программно:

```java
props.put(ProducerConfig.RETRIES_CONFIG, 10);
```

### Что считается retry?

Например:

```text
Producer

↓

NetworkException

↓

Retry

↓

Retry

↓

ACK
```

Retry выполняется автоматически.

---

# 2. `delivery.timeout.ms` 

Самый важный таймаут.

```properties
spring.kafka.producer.properties.delivery.timeout.ms=30000
```

или

```java
props.put(
    ProducerConfig.DELIVERY_TIMEOUT_MS_CONFIG,
    30_000
);
```

Это **максимальное время**, в течение которого Producer будет пытаться доставить сообщение.

В него входят:

- retries;
    
- ожидание свободного места в buffer;
    
- ожидание ACK;
    
- сетевые задержки.
    

По умолчанию:

```text
120000 ms
```

---

# 3. `request.timeout.ms`

Максимальное время ожидания ответа от брокера.

```properties
spring.kafka.producer.properties.request.timeout.ms=10000
```

или

```java
props.put(
    ProducerConfig.REQUEST_TIMEOUT_MS_CONFIG,
    10_000
);
```

По умолчанию

```text
30000 ms
```

---

# 4. `linger.ms`

Не таймаут ошибки.

Это задержка перед отправкой.

```properties
spring.kafka.producer.properties.linger.ms=5
```

Producer подождёт

```text
5 ms
```

чтобы собрать batch побольше.

---

# 5. `batch.size`

Размер batch.

```properties
spring.kafka.producer.properties.batch.size=32768
```

32 KB.

---

# 6. `max.block.ms`

Максимальное время блокировки

```java
send()

partitionsFor()

initTransactions()
```

Например

```properties
spring.kafka.producer.properties.max.block.ms=10000
```

---

# 7. `retry.backoff.ms`

Пауза между retry.

```properties
spring.kafka.producer.properties.retry.backoff.ms=500
```

Получится

```text
Attempt

↓

500 ms

↓

Retry

↓

500 ms

↓

Retry
```

---

# 8. `retry.backoff.max.ms`

В новых версиях Kafka можно ограничить максимальный backoff.

---

# 9. `acks`

Очень влияет на retry.

```properties
spring.kafka.producer.acks=all
```

или

```text
1

0

all
```

---

# 10. `enable.idempotence`

Практически всегда

```properties
spring.kafka.producer.properties.enable.idempotence=true
```

Тогда повторные отправки не создадут дублей из-за внутренних retry Producer.

---

# Какие ошибки Producer повторяет?

Не все.

Например

### Retry

```text
TimeoutException

NetworkException

LeaderNotAvailable

NotEnoughReplicas
```

---

### Без Retry

```text
SerializationException

RecordTooLargeException

AuthenticationException

TopicAuthorizationException
```

Они сразу попадут в `CompletableFuture` как ошибка.

---

# Важная особенность современных версий Kafka

Начиная с Kafka 3.x (и особенно при включённой идемпотентности), параметр `retries` фактически перестал быть основным ограничителем. Клиент будет повторять отправку до тех пор, пока не истечёт `delivery.timeout.ms` или не встретится неретраемая ошибка.

Поэтому на практике сейчас важнее настроить:

```properties
spring.kafka.producer.properties.delivery.timeout.ms=30000
spring.kafka.producer.properties.request.timeout.ms=10000
spring.kafka.producer.properties.retry.backoff.ms=500
```

а `retries` можно оставить по умолчанию или задать большое значение.

---

# Рекомендуемая конфигурация для production

Обычно используют примерно такой набор:

```properties
# Надёжность
spring.kafka.producer.acks=all
spring.kafka.producer.properties.enable.idempotence=true

# Таймауты
spring.kafka.producer.properties.delivery.timeout.ms=30000
spring.kafka.producer.properties.request.timeout.ms=10000

# Повторные попытки
spring.kafka.producer.retries=2147483647
spring.kafka.producer.properties.retry.backoff.ms=500

# Производительность
spring.kafka.producer.properties.linger.ms=5
spring.kafka.producer.properties.batch.size=32768
```

### Почему `retries=2147483647`?

При включённой идемпотентности именно такой подход рекомендует клиент Kafka: ограничителем становится не число попыток, а `delivery.timeout.ms`. То есть Producer будет пытаться отправить сообщение столько раз, сколько успеет до истечения общего времени доставки. Это избавляет от необходимости подбирать "магическое" число retry и делает поведение более предсказуемым.


----
#### [[Ретраи Producer - Kafka - Flashcards|Link to flashcards]]



---
### References:

