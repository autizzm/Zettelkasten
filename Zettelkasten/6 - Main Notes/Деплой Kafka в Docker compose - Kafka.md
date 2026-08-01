
01-07-2026 20:41

Status:

Tags: [[Kafka]] [[Java+]]

---
# Деплой Kafka в Docker compose - Kafka
 
### Какие существуют способы запуска Kafka

Наиболее популярные Docker-образы:

|Image|Плюсы|Минусы|
|---|---|---|
|Apache kafka|официальный|чуть менее удобные переменные окружения|
|Bitnami Kafka|очень удобный|отличается именами переменных|
|Confluent cp-kafka|много дополнительных компонентов|большой размер|


---
### Минимальный compose

Самый простой вариант.

```yaml
services:

  kafka:
    image: apache/kafka:4.0.0
	
    ports:
      - "9092:9092"
	
    environment:
	
      KAFKA_NODE_ID: 1
	
      KAFKA_PROCESS_ROLES: broker,controller
	
      KAFKA_LISTENERS: PLAINTEXT://:9092,CONTROLLER://:9093
	
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://localhost:9092
	
      KAFKA_CONTROLLER_LISTENER_NAMES: CONTROLLER
	
      KAFKA_CONTROLLER_QUORUM_VOTERS: 1@kafka:9093
	
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT
	
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
	
      KAFKA_TRANSACTION_STATE_LOG_REPLICATION_FACTOR: 1
	
      KAFKA_TRANSACTION_STATE_LOG_MIN_ISR: 1
```

Этого уже достаточно для локальной разработки.

---

### Основные параметры


#### KAFKA_NODE_ID

Идентификатор брокера.

```yaml
KAFKA_NODE_ID=1
```

При нескольких брокерах:

```
1
2
3
```

Он должен быть уникальным.


---
#### KAFKA_PROCESS_ROLES

Какие роли выполняет процесс.

```
broker
controller
```

Можно `broker`, или `controller`, или `broker,controller`.


На production обычно контроллеры выделяют отдельно.

Для локальной разработки почти всегда

```
broker,controller
```


---
#### KAFKA_CONTROLLER_QUORUM_VOTERS

Очень важный параметр.

Он описывает кластер контроллеров.

Пример

```
1@kafka1:9093,
2@kafka2:9093,
3@kafka3:9093
```

Расшифровка

```
nodeId@host:port
```

---

#### KAFKA_LISTENERS

На каких портах Kafka слушает.

Например

```
PLAINTEXT://:9092
CONTROLLER://:9093
```

или

```
INTERNAL://:9092
EXTERNAL://:29092
CONTROLLER://:9093
```

---

#### KAFKA_ADVERTISED_LISTENERS

Самая частая причина проблем.

Kafka не сообщает клиенту адрес сокета, через который клиент уже подключился.

Она сообщает клиенту адрес именно из

```
advertised.listeners
```

Например

```
PLAINTEXT://localhost:9092
```

или

```
PLAINTEXT://kafka:9092
```

или

```
EXTERNAL://192.168.1.10:9092
```

Именно этот адрес будет использовать Producer/Consumer после получения metadata.


---
## Почему LISTENERS и ADVERTISED_LISTENERS разные?

Допустим

```
     Docker network

Spring Boot ---- kafka
```

Внутри сети:

```
kafka:9092
```

Но снаружи

```
localhost:9092
```

Поэтому слушать можно

```
:9092
```

а рекламировать

```
localhost:9092
```

или наоборот.

---

#### KAFKA_LISTENER_SECURITY_PROTOCOL_MAP

Соответствие listener → протокол.

Например

```
INTERNAL:PLAINTEXT
EXTERNAL:SASL_SSL
CONTROLLER:PLAINTEXT
```

или

```
PLAINTEXT:PLAINTEXT
```


---
#### KAFKA_CONTROLLER_LISTENER_NAMES

Какой listener используется контроллером.

Например

```
CONTROLLER
```


---
## KAFKA_INTER_BROKER_LISTENER_NAME

Через какой listener брокеры общаются друг с другом.

Например

```
INTERNAL
```

---

# 4. Несколько listeners

Очень часто используют

```
INTERNAL
```

для Docker

и

```
EXTERNAL
```

для IDE.

Например

```
KAFKA_LISTENERS=
 INTERNAL://:9092,
 EXTERNAL://:29092,
 CONTROLLER://:9093
```

```
KAFKA_ADVERTISED_LISTENERS=
 INTERNAL://kafka:9092,
 EXTERNAL://localhost:29092
```

Тогда

Spring внутри compose подключается

```
kafka:9092
```

IDE

```
localhost:29092
```

Это одна из самых полезных конфигураций.

---

# 5. Cluster ID

В KRaft есть Cluster ID.

Обычно

```
KAFKA_CLUSTER_ID
```

или

```
CLUSTER_ID
```

Он одинаковый для всего кластера.

Пример

```
MkU3OEVBNTcwNTJENDM2Qk
```

---

# 6. Где хранятся данные

По умолчанию внутри контейнера.

Лучше использовать volume.

```yaml
volumes:

  kafka-data:
```

```
services:

 kafka:

   volumes:

     - kafka-data:/var/lib/kafka/data
```

Иначе после удаления контейнера всё исчезнет.

---

# 7. Автоматическое создание топиков

```
KAFKA_AUTO_CREATE_TOPICS_ENABLE=true
```

Полезно для разработки.

На production обычно

```
false
```

---

# 8. Количество разделов по умолчанию

```
KAFKA_NUM_PARTITIONS=3
```

Все автоматически создаваемые топики будут иметь 3 partition.

---

# 9. Replication Factor по умолчанию

```
KAFKA_DEFAULT_REPLICATION_FACTOR=3
```

Имеет смысл только если брокеров ≥3.

---

# 10. Минимальное количество ISR

```
KAFKA_MIN_INSYNC_REPLICAS=2
```

Используется совместно с

```
acks=all
```

Это одна из важнейших настроек отказоустойчивости.

---

# 11. Размер сообщения

По умолчанию

```
1 MB
```

Можно увеличить

```
KAFKA_MESSAGE_MAX_BYTES=10485760
```

10 MB.

Но лучше не злоупотреблять.

---

# 12. Настройки логов

Хранение сообщений.

Например

```
KAFKA_LOG_RETENTION_HOURS=168
```

7 дней.

---

Можно хранить по размеру

```
KAFKA_LOG_RETENTION_BYTES
```

---

Удаление старых сегментов

```
KAFKA_LOG_SEGMENT_BYTES
```

---

# 13. Compression

Можно включить

```
compression.type=lz4
```

или

```
zstd
```

или

```
snappy
```

---

# 14. Transaction Log

Для exactly once.

```
KAFKA_TRANSACTION_STATE_LOG_REPLICATION_FACTOR
```

Обычно

```
3
```

---

```
KAFKA_TRANSACTION_STATE_LOG_MIN_ISR
```

Обычно

```
2
```

---

# 15. Offsets Topic

Kafka сама хранит offset'ы.

Настройка

```
KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR
```

---

# 16. Compose для 3 брокеров

Схема

```
                Controller quorum

Broker1
Broker2
Broker3
```

Все являются и broker, и controller.

```yaml
version: "3.9"

services:

  kafka1:
    image: apache/kafka:4.0.0
    hostname: kafka1

    ports:
      - "9092:9092"

    environment:

      KAFKA_NODE_ID: 1

      KAFKA_PROCESS_ROLES: broker,controller

      KAFKA_CONTROLLER_LISTENER_NAMES: CONTROLLER

      KAFKA_CONTROLLER_QUORUM_VOTERS: >
        1@kafka1:9093,
        2@kafka2:9093,
        3@kafka3:9093

      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: >
        CONTROLLER:PLAINTEXT,
        INTERNAL:PLAINTEXT,
        EXTERNAL:PLAINTEXT

      KAFKA_LISTENERS: >
        INTERNAL://:9092,
        CONTROLLER://:9093,
        EXTERNAL://:29092

      KAFKA_ADVERTISED_LISTENERS: >
        INTERNAL://kafka1:9092,
        EXTERNAL://localhost:9092

      KAFKA_INTER_BROKER_LISTENER_NAME: INTERNAL

      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 3

      KAFKA_DEFAULT_REPLICATION_FACTOR: 3

      KAFKA_MIN_INSYNC_REPLICAS: 2
```

Для kafka2 и kafka3 меняются:

```
NODE_ID

advertised.listeners

ports
```

Например

```
9094

9096
```

и

```
localhost:9094

localhost:9096
```

---

# 17. Какие volume нужны

Для каждого брокера отдельно.

```
volumes:

 kafka1-data:

 kafka2-data:

 kafka3-data:
```

Иначе брокеры будут писать в одну директорию.

---

# 18. Наиболее полезные настройки для разработки

Практически всегда использую именно их:

```yaml
KAFKA_AUTO_CREATE_TOPICS_ENABLE=true

KAFKA_NUM_PARTITIONS=3

KAFKA_DEFAULT_REPLICATION_FACTOR=1

KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR=1

KAFKA_MESSAGE_MAX_BYTES=10485760

KAFKA_LOG_RETENTION_HOURS=24
```

---

# 19. Наиболее полезные настройки для production

```yaml
KAFKA_DEFAULT_REPLICATION_FACTOR=3

KAFKA_MIN_INSYNC_REPLICAS=2

KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR=3

KAFKA_TRANSACTION_STATE_LOG_REPLICATION_FACTOR=3

KAFKA_TRANSACTION_STATE_LOG_MIN_ISR=2

KAFKA_AUTO_CREATE_TOPICS_ENABLE=false

KAFKA_COMPRESSION_TYPE=zstd

KAFKA_NUM_PARTITIONS=6..24
```

Конкретные значения зависят от числа брокеров, требований к отказоустойчивости и ожидаемой нагрузки.

---

# 20. Практические рекомендации

При работе с Kafka в Docker Compose стоит придерживаться нескольких правил:

1. **Разделяйте внутренние и внешние подключения.** Используйте отдельные `INTERNAL` и `EXTERNAL` listeners, чтобы контейнеры обращались к Kafka по имени сервиса (`kafka:9092`), а приложения на хосте — через `localhost`.
    
2. **Всегда подключайте отдельный volume для каждого брокера.** Это сохраняет данные между перезапусками и предотвращает конфликты.
    
3. **Не включайте автоматическое создание топиков в production.** Создавайте топики явно с нужным числом партиций и фактором репликации.
    
4. **Используйте не менее трёх брокеров** для отказоустойчивого кластера с репликацией.
    
5. **Настройте `min.insync.replicas` совместно с `acks=all`.** Это одна из самых важных комбинаций для обеспечения надёжной записи сообщений.
    
6. **Планируйте число партиций заранее.** Увеличить их позже можно, а уменьшить — нельзя.
    
7. **Следите за `advertised.listeners`.** Неверная настройка этого параметра является самой распространённой причиной ошибок вида `Connection to node ... could not be established`.
    
8. **Используйте отдельные controller-порты.** Даже если брокер совмещает роли broker и controller, для контроллера должен использоваться отдельный listener.
    

---

## Что стоит знать для собеседования

Хороший уровень подготовки предполагает понимание следующих тем:

- различие между `listeners` и `advertised.listeners`;
    
- назначение `controller.quorum.voters` в режиме KRaft;
    
- причины использования нескольких listeners (`INTERNAL`/`EXTERNAL`);
    
- выбор `replication.factor` и `min.insync.replicas`;
    
- роль `offsets` и `transaction` внутренних топиков;
    
- почему каждому брокеру необходим собственный volume;
    
- почему кластер из трёх брокеров является минимальной рекомендуемой конфигурацией для production;
    
- как Docker Compose влияет на сетевые имена и почему сервисы обращаются друг к другу по DNS-именам (`kafka1`, `kafka2`, ...), а не по `localhost`.
    

Если вы уверенно объясняете эти настройки и умеете собрать Compose-файл для кластера из трёх брокеров с внутренними и внешними listeners, этого обычно достаточно для большинства Java/Spring Backend собеседований.

----
#### [[Деплой Kafka в Docker compose - Kafka - Flashcards|Link to flashcards]]



---
### References:

