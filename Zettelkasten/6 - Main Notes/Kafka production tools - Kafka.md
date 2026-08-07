
06-08-2026 09:24

Status:

Tags: [[Kafka]] [[Java+]]

---
# Kafka production tools - Kafka


### CLI-инструменты Kafka (то, что используют почти каждый день)

Это базовый набор, который должен знать любой senior.

| Инструмент                     | Для чего используется                 |
| ------------------------------ | ------------------------------------- |
| `kafka-topics.sh`              | Создание, удаление, описание топиков  |
| `kafka-console-consumer.sh`    | Чтение сообщений напрямую из топика   |
| `kafka-console-producer.sh`    | Отправка тестовых сообщений           |
| `kafka-consumer-groups.sh      | Просмотр lag, offsets, reset offsets  |
| `kafka-configs.sh`             | Изменение конфигурации broker/topic   |
| `kafka-acls.sh`                | Диагностика ACL                       |
| `kafka-reassign-partitions.sh` | Перемещение partition между брокерами |
| `kafka-dump-log.sh`            | Низкоуровневый просмотр log segments  |
| `kafka-metadata-quorum.sh`     | KRaft quorum диагностика              |

Особенно важны:

- `kafka-consumer-groups.sh` — практически главный инструмент при расследовании инцидентов.
    
- `kafka-console-consumer.sh` — быстро посмотреть DLQ или конкретный partition.

### [[ACL (Access Control List) - Kafka]]


---
### UI-инструменты

В production почти никто не работает только через CLI.

### AKHQ

Очень популярен в Kubernetes окружении.

Позволяет:

- просматривать сообщения;
- фильтровать по ключу;
- смотреть headers;
- смотреть offsets;
- consumer groups;
- lag;
- партиции;
- конфигурацию топиков;
- ACL.

Часто используется именно для просмотра DLQ.

### Kafka UI (Provectus)

Open-source аналог.

Очень распространен.

Поддерживает:

- просмотр сообщений;
- consumer groups;
- offsets;
- schema registry;
- brokers;
- partitions.


### Conduktor

Коммерческий продукт.

Используется в крупных компаниях.

Дополнительно умеет:

- replay сообщений;
- producer прямо из UI;
- SQL-подобные запросы к Kafka;
- управление ACL;
- audit.


---
### Мониторинг Kafka

В production Kafka почти всегда мониторится через JMX + Prometheus + Grafana.

#### JMX Metrics

Kafka brokers публикуют огромное количество MBeans.

Основные категории:

##### Broker

- `BytesInPerSec`
- `BytesOutPerSec`
- `MessagesInPerSec`
- `RequestHandlerAvgIdlePercent`
- `NetworkProcessorAvgIdlePercent`

##### Topic

- Produce requests
- Fetch requests
- `Throughput`


##### Replica

- `UnderReplicatedPartitions`
- `OfflinePartitionsCount`
- ISR changes

| Метрика                     | Что означает                                                       |
| --------------------------- | ------------------------------------------------------------------ |
| `UnderReplicatedPartitions` | Партиция доступна, но не все реплики успевают за лидером           |
| `OfflinePartitionsCount`    | Партиция недоступна для чтения/записи, потому что у нее нет лидера |

##### Controller / KRaft

- `ActiveControllerCount`
- Metadata propagation
- Leader elections


---
#### Инструменты


#### Prometheus

Обычно используется JMX Exporter.

Схема:
```
Kafka Broker (JMX)
	|
	V
JMX Exporter
	|
	V
Prometheus
	|
	V
Grafana
```


#### Grafana Dashboards

Стандартные дашборды обычно содержат:

##### Cluster Health

- brokers online
- controllers
- ISR
- partitions
- replication


##### Throughput

- incoming bytes/sec  
- outgoing bytes/sec
- messages/sec

### Consumers

- lag
- commits/sec
- fetch latency
- rebalance count


### Producers

- produce latency
- request rate
- error rate
- retries


---
### Consumer Lag Monitoring

Обычно lag мониторят не самим приложением, а отдельным экспортером.

Самые популярные:

### kafka-exporter

Экспортирует:

- consumer lag;
    
- current offset;
    
- end offset;
    
- partition status.
    

Метрики:

- `kafka_consumergroup_lag`
    
- `kafka_consumergroup_current_offset`
    
- `kafka_topic_partition_current_offset`
    

### Burrow (LinkedIn)

Очень интересный инструмент.

Он оценивает состояние consumer group, а не просто lag.

Например:

- lag растет;
    
- lag уменьшается;
    
- consumer стоит;
    
- offsets не двигаются.
    

Burrow способен определять ситуации, когда lag большой, но consumer нормально догоняет поток.

Во многих компаниях именно Burrow является источником алертов.

### Алертинг

Типичная связка:

- Prometheus
    
- Alertmanager
    
- PagerDuty / Opsgenie / Slack
    

Типичные алерты:

### Consumer Lag

Например:

- lag > 1000
    
- более 10 минут
    

### DLQ Growth

Например:

- в DLQ пришло > 10 сообщений за минуту
    
- размер DLQ увеличивается
    

### Broker

- UnderReplicatedPartitions > 0
    
- OfflinePartitions > 0
    
- Disk usage > 85%
    
- Controller election occurred
    

### Consumer

- rebalance слишком часто
    
- commit latency
    
- poll timeout
    
- heartbeat failures
    

### DLQ (Dead Letter Queue) инфраструктура

Практически всегда используется отдельный топик.

Типичная схема:

Failure flow

DLQ pipeline

Main topic

Consumer

Retry topics

DLQ topic

Часто существует несколько уровней:

- retry-1
- retry-5m
- retry-1h
- DLQ


Spring Kafka предоставляет готовые механизмы:

- `DeadLetterPublishingRecoverer`
- `DefaultErrorHandler`
- `BackOff`
- RetryTopic


### Retry Topics

Очень распространенная практика.

Вместо немедленной DLQ:

```
orders

orders.retry.1m

orders.retry.5m

orders.retry.30m

orders.dlq
```

Это позволяет автоматически переживать временные ошибки.


### Replay / Reprocessing инфраструктура

Для production обычно существует отдельный механизм.

Варианты:

### Replay Consumer

Отдельный сервис читает DLQ и публикует обратно в основной топик.

### Kafka Streams

Используется для преобразования сообщений перед повторной отправкой.

### Kafka Connect

Может переносить данные между топиками.

### Batch Replay Job

Часто это отдельный Job в Kubernetes.

Он:

- читает диапазон offsets;
    
- читает временной диапазон;
    
- фильтрует сообщения;
    
- публикует заново.
    

### Offset Management Tools

Очень важная категория.

### kafka-consumer-groups.sh

Позволяет:

- посмотреть offsets;
    
- посмотреть lag;
    
- сбросить offsets;
    
- перемотать consumer.
    

### AdminClient API

Программное управление offsets.

Используется в replay-инструментах.

Позволяет:

- alterConsumerGroupOffsets;
    
- listConsumerGroupOffsets.
    

### Schema Registry

Если используется Avro / Protobuf / JSON Schema.

Инструменты:

### Confluent Schema Registry

Позволяет:

- хранить версии схем;
    
- проверять совместимость;
    
- получать schema ID;
    
- эволюционировать схемы.
    

Очень важен при расследовании serialization/deserialization проблем.

### Kafka Connect

Используется для интеграции Kafka с внешними системами.

Инструменты:

- Connect REST API
    
- Connect UI
    
- Connector metrics
    

Позволяет:

- перезапуск connector;
    
- replay через connector;
    
- мониторинг connector lag.
    

### Логи приложений

При расследовании инцидентов Kafka почти всегда используется корреляция:

- Kafka offset
    
- partition
    
- topic
    
- key
    
- traceId
    
- spanId
    

Типичный лог:

```
topic=orders
partition=3
offset=145672
key=order-123
traceId=abcd
```

Без этого расследование практически невозможно.

### Tracing

Через OpenTelemetry / Jaeger.

Позволяет увидеть:

- producer отправил сообщение;
    
- broker принял;
    
- consumer получил;
    
- downstream сервисы.
    

Особенно полезно при «сообщение было обработано, но эффект не произошел».

### Типичный production стек

Вот то, что реально встречается очень часто.

|Задача|Инструмент|
|---|---|
|Просмотр сообщений|AKHQ / Kafka UI|
|Просмотр DLQ|AKHQ / Kafka UI / console-consumer|
|Consumer lag|kafka-exporter + Prometheus + Grafana|
|Алерты по lag|Alertmanager / Burrow|
|Сброс offsets|kafka-consumer-groups.sh|
|Replay сообщений|Replay job / Kafka Streams / custom service|
|Serialization проблемы|Schema Registry + DLQ + logs|
|Мониторинг брокеров|JMX Exporter + Grafana|
|Диагностика consumer|logs + offsets + lag + tracing|
|Массовый репроцессинг|Batch replay job|

Именно этот набор инструментов лежит в основе ответов на твои следующие вопросы: как смотрят DLQ, как повторно обрабатывают сообщения, как мониторят lag и DLQ, и как репроцессить уже закоммиченные сообщения после исправления сервиса.

----
#### [[Kafka production tools - Kafka - Flashcards|Link to flashcards]]



---
### References:

