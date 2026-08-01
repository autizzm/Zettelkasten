
10-04-2026 19:41

Status:

Tags: [[Kafka]]

---
# Конфигурация - Kafka


**server.properties для Kafka KRaft (комбинированный узел)**

```properties
node.id=1 

# узел выполняет обе роли 
process.roles=broker,controller 

# список контроллеров-кворума 
controller.quorum.voters=1@kafka1:9093,2@kafka2:9093,3@kafka3:9093 

# имя listener'а, используемого для обмена между контроллерами 
controller.listener.names=CONTROLLER 

# слушать 9092 для клиентов, 9093 для контроллеров 
listeners=PLAINTEXT://0.0.0.0:9092,CONTROLLER://0.0.0.0:9093 

# как этот брокер будет виден клиентам (по hostname) 
advertised.listeners=PLAINTEXT://kafka1:9092 

# директория логов (в т.ч. для журнала метаданных контроллера)
log.dirs=/var/lib/kafka/data 


# Параметры репликации внутренних топиков (например, для Kafka Connect, Consumer Offsets): 
offsets.topic.replication.factor=3 

transaction.state.log.replication.factor=3 
```


> [!warning]
> В конфигурации каждого узла должны быть: уникальный `node.id` (целое число, можно нумеровать с 1 или с 0 по желанию, но он **не должен совпадать** с никаким старым broker.id, если вы мигрируете – ID-контроллеры и брокеры делят одно пространство имен), одинаковый `controller.quorum.voters` (список всех узлов-контроллеров кластера). 
> 
> Заметим формат `voters`: каждая запись – это `nodeId@host:port`. Здесь `port` – порт контроллер-листенера на узле. Мы для простоты используем контроллер-порт 9093 на каждом узле.


Если контроллеры вынесены на отдельные узлы (dedicated controllers), тогда на **контроллерских узлах** `process.roles=controller`, а `listeners` обычно содержит только контроллерный интерфейс (например, `CONTROLLER://0.0.0.0:9093`). На **брокерских узлах** – `process.roles=broker` и им тоже нужен знать список контроллеров (`controller.quorum.voters`) и настроить, как они будут подключаться к контроллерам. В этом случае брокеры не слушают порт CONTROLLER, а только используют его для исходящих подключений к контроллерам. В комбинированном режиме, как в примере выше, каждый узел и брокер, и контроллер одновременно.


```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kafka-broker-config
data:
  # KRaft
  KAFKA_PROCESS_ROLES: "broker"
  KAFKA_CONTROLLER_LISTENER_NAMES: "CONTROLLER"
  KAFKA_CONTROLLER_QUORUM_VOTERS: "0@kafka-controller-0.kafka-controller-headless:9093,1@kafka-controller-1.kafka-controller-headless:9093,2@kafka-controller-2.kafka-controller-headless:9093"

  # Networking
  KAFKA_LISTENERS: "PLAINTEXT://:9092"
  KAFKA_ADVERTISED_LISTENERS: "PLAINTEXT://$(POD_NAME).kafka-broker-headless:9092"
  KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: "PLAINTEXT:PLAINTEXT"
  KAFKA_INTER_BROKER_LISTENER_NAME: "PLAINTEXT"

  # Replication
  KAFKA_DEFAULT_REPLICATION_FACTOR: "3"
  KAFKA_MIN_INSYNC_REPLICAS: "2"
  KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: "3"

  # Transactions
  KAFKA_TRANSACTION_STATE_LOG_REPLICATION_FACTOR: "1"
  KAFKA_TRANSACTION_STATE_LOG_MIN_ISR: "1"

  # Topic defaults
  KAFKA_NUM_PARTITIONS: "3"
  KAFKA_AUTO_CREATE_TOPICS_ENABLE: "false"
  KAFKA_DELETE_TOPIC_ENABLE: "true"

  # Storage
  KAFKA_LOG_DIRS: "/var/lib/kafka/data"

  # Reliability
  KAFKA_UNCLEAN_LEADER_ELECTION_ENABLE: "false"

  # Compression
  KAFKA_COMPRESSION_TYPE: "producer"

  # Performance
  KAFKA_NUM_NETWORK_THREADS: "3"
  KAFKA_NUM_IO_THREADS: "8"
  KAFKA_SOCKET_SEND_BUFFER_BYTES: "102400"
  KAFKA_SOCKET_RECEIVE_BUFFER_BYTES: "102400"
  KAFKA_SOCKET_REQUEST_MAX_BYTES: "104857600"
```


---
### Дрочь с кластером


**Инициализация кластера KRaft (cluster ID):** Перед первым запуском кластера в KRaft-режиме необходимо сгенерировать уникальный **идентификатор кластера** и проставить его в метаданных хранилища. Делается это с помощью утилиты `kafka-storage.sh`. Шаги:

1. Генерация UUID для кластера: `$ KAFKA_CLUSTER_ID=$(./bin/kafka-storage.sh random-uuid)` Эта команда выводит случайный UUID, например: `XyZ123AbcDeFgHiJkLmNoPq`. Его нужно сохранить – он должен быть одинаковым для всех узлов кластера.
2. Форматирование логовых директорий на **каждом узле** с указанием cluster ID: `$ ./bin/kafka-storage.sh format -t $KAFKA_CLUSTER_ID -c config/server.properties` Опция `-t` задает cluster ID, `-c` указывает на конфиг-файл данного узла. Эта команда инициирует структуру данных в `log.dirs`, в частности создаёт файл `meta.properties`, где сохраняет cluster ID и `node.id` узла. _Важно:_ cluster ID должен быть один и тот же на всех узлах (поэтому мы генерируем один UUID и используем его повсюду), а node.id у каждого – свой. Если случайно запустить два узла с одинаковым node.id или с разными cluster ID – кластер не соберется (каждый узел проверяет соответствие cluster ID при подключении).

После форматирования, можно запускать Kafka-брокеры. Они прочитают из `meta.properties` свой cluster ID и node.id и начнут обмениваться сообщениями Raft. Один из контроллеров станет лидером и инициализирует пустой журнал метаданных. Кластер готов к работе.


**Пример `meta.properties`:** (создается автоматически утилитой форматирования)

```
node.id=1
version=1
cluster.id=XyZ123AbcDeFgHiJkLmNoPq
```

Для каждой комбинации broker/controller конфигов будет свой файл meta.properties в соответствующем log.dir.

----
#### [[Конфигурация - Kafka - Flashcards|Link to flashcards]]



---
### References:

