
02-08-2026 10:08

Status:

Tags: [[Kafka Streams Java Lib]] [[Kafka Streams]] [[Kafka]]

---
# Serdes - Kafka Streams Java Lib


Serdes - сериализатор и десериализатор.

> [!tip] Note
> Serdes для ключей и Serdes для значений - разные


```java
...
final Map<String, String> serdeConfig = Collections.singletonMap(AbstractKafkaAvroSerDeConfig.SCHEMA_REGISTRY_URL_CONFIG, SCHEMA_REGISTRY_URL);

final SpecificAvroSerde<PlayEvent> playEventServe = new SpecificAvfroSerde<>();

playEventSerde.configure(serdeConfig, false);
```


----
#### [[Serdes - Kafka Streams Java Lib - Flashcards|Link to flashcards]]



---
### References:

