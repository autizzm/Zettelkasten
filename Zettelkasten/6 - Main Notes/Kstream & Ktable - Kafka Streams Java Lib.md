
02-08-2026 10:14

Status:

Tags: [[Kafka Streams Java Lib]] [[Kafka Streams]] [[Kafka]]
 
---
# Kstream & Ktable - Kafka Streams Java Lib


![[Pasted image 20260802101657.png]]


---
### Kstream

Kstream - абстракция над stream.

Stream - unbounded sequence of structured data (events).



```java
final Map<String, String> serdeConfig = Collections.singletonMap(AbstractKafkaAvroSerDeConfig.SCHEMA_REGISTRY_URL_CONFIG, SCHEMA_REGISTRY_URL);

final SpecificAvroSerde<PlayEvent> playEventServe = new SpecificAvfroSerde<>();

playEventSerde.configure(serdeConfig, false);
```


```java
final KStreamBuilder builder = new KStreamBuilder();
final KStream<String, PlayEvent> playEvents = 
	builder.stream(
		Serdes.String(), //key serde
		playEventSerde,  //value serde
		"play-events");
```


---
### Ktable

Ktable - содержит одно постоянно обновляемое значение для каждого ключа.

То есть типа:


| Key  | Value  |
| ---- | ------ |
| 0233 | "Jack" |
| 0234 | "Dave" |
| 0111 | "Lain" |
Только самое последнее значение обновляется:

Если в топик приходит факт: "0233" - "John",  то таблица обновится:

| Key  | Value  |
| ---- | ------ |
| 0233 | "John" |
| 0234 | "Dave" |
| 0111 | "Lain" |



----
#### [[Kstream - Kafka Streams Java Lib - Flashcards|Link to flashcards]]



---
### References:

