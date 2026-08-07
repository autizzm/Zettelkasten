
06-08-2026 09:27

Status:

Tags: [[Kafka]] [[Java+]]

---
# ACL (Access Control List) - Kafka

ACL (Access Control List) в Kafka — это механизм авторизации, который определяет, какой пользователь (principal) может выполнять какие операции над какими ресурсами Kafka.

Проще говоря: после того как клиент аутентифицировался (например, через SASL/SCRAM, SASL/PLAIN, SSL и т.д.), Kafka проверяет ACL и решает, разрешить ли операцию.

> [!tip] Note
> В production ACL часто управляются централизованно (Terraform, Ansible, Strimzi, Confluent RBAC и т.д.), а `kafka-acls.sh` используется для диагностики и ручного администрирования.


### Что контролирует ACL

Kafka проверяет права на различные типы ресурсов.

|Ресурс|Примеры|
|---|---|
|Topic|orders, payments|
|Consumer Group|order-service-group|
|Cluster|весь Kafka cluster|
|Transactional ID|tx-order-1|
|Delegation Token|редко используется|

Операции, которые можно разрешить:

- Read — читать сообщения
    
- Write — писать сообщения
    
- Create — создавать topic
    
- Delete — удалять topic
    
- Alter — изменять конфигурацию
    
- Describe — просматривать метаданные
    
- DescribeConfigs
    
- AlterConfigs
    
- IdempotentWrite
    
- All — все операции
    

### Пример

Пользователь `User:order-service` должен читать topic `orders`.

Команда:
```bash
kafka-acls.sh \
  --bootstrap-server localhost:9092 \
  --add \
  --allow-principal User:order-service \
  --operation Read \
  --topic orders
```

Теперь этот пользователь может выполнять Fetch запросы к topic `orders`.


---
### kafka-acls.sh

Это стандартная утилита Kafka для управления ACL.

### Добавить права

Producer может писать в topic `orders`.

```bash
kafka-acls.sh \
  --bootstrap-server localhost:9092 \
  --add \
  --allow-principal User:producer \
  --operation Write \
  --topic orders
```

### Consumer

Consumer должен:

- читать topic
    
- читать offsets своей consumer group
    

Поэтому обычно нужно две ACL:

```bash
kafka-acls.sh \
  --bootstrap-server localhost:9092 \
  --add \
  --allow-principal User:consumer \
  --operation Read \
  --topic orders

kafka-acls.sh \
  --bootstrap-server localhost:9092 \
  --add \
  --allow-principal User:consumer \
  --operation Read \
  --group orders-group
```

Без ACL на group consumer не сможет корректно работать с offsets.

### Посмотреть ACL

Для конкретного topic:

```bash
kafka-acls.sh \
  --bootstrap-server localhost:9092 \
  --list \
  --topic orders
```

Output примерно такой:

```
Current ACLs for resource `ResourcePattern(resourceType=TOPIC, name=orders)`:
  User:producer has Allow permission for operations: Write
  User:consumer has Allow permission for operations: Read
```

### Удалить ACL

```bash
kafka-acls.sh \
  --bootstrap-server localhost:9092 \
  --remove \
  --allow-principal User:producer \
  --operation Write \
  --topic orders
```

### Префиксные ACL

Часто сервис имеет доступ ко всем своим топикам.

Например:

- `orders.created`
    
- `orders.updated`
    
- `orders.deleted`

Можно выдать права сразу на префикс `orders.`


```bash
kafka-acls.sh \
  --bootstrap-server localhost:9092 \
  --add \
  --allow-principal User:order-service \
  --operation Read \
  --topic orders. \
  --resource-pattern-type prefixed
```

Теперь ACL действует на все topics, начинающиеся с `orders.`.


---
### Как Kafka проверяет ACL

При запросе клиента Kafka знает:

- principal (например, `User:consumer`)
    
- тип операции (`Read`)
    
- ресурс (`orders`)
    

И выполняет проверку:

`User:consumer + Read + Topic:orders`

Если найдено правило `Allow` → операция разрешена.

Если нет подходящего правила → TopicAuthorizationException или GroupAuthorizationException.


---
### Типичные ACL для сервиса

Представим микросервис `payment-service`.

Ему нужно:

- читать `orders`
    
- писать `payments`
    
- использовать consumer group `payment-group`
    

Нужны ACL:

```
Topic orders      -> Read
Topic payments    -> Write
Group payment-group -> Read
```

Именно такой набор чаще всего встречается в production.


---
### ACL в ZooKeeper и KRaft

Исторически ACL хранились в ZooKeeper.

В современных Kafka (KRaft) они хранятся во внутреннем metadata log контроллеров.

Команда `kafka-acls.sh` выглядит почти одинаково, но в KRaft используется `--bootstrap-server`, а не `--authorizer-properties zookeeper.connect=...`.


---
### Что важно знать Senior Backend Developer

На собеседовании обычно ожидают понимание следующих нюансов:

- Producer обычно требует `Write` на topic.
    
- Consumer требует `Read` на topic и `Read` на consumer group.
    
- `Describe` часто нужен для получения метаданных topic.
    
- ACL можно задавать как literal, prefixed или wildcard.
    
- ACL проверяются после аутентификации.
    
  

----
#### [[ACL (Access Control List) - Kafka - Flashcards|Link to flashcards]]



---
### References:

