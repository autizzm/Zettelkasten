
01-08-2026 19:38

Status:

Tags: [[Kafka Streams Java Lib]] [[Kafka Streams]] [[Kafka]] [[Java+]]

---
# StreamsConfig - Kafka Streams


```java
import org.apache.kafka.streams.StreamsConfig;
...

final Properties streamsConfiguration = new Properties();
streamsConfiguration.put(StreamConfig.APPLICATION_ID_CONFIG, "kafka-music-charts");
streamsConfiguration.put(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, "broker1:9092,broker2:9092");
streamsConfiguration.put(..., ...);

```

`APPLICATION_ID_CONFIG` - must be unique in Kafka cluster - это аналог consumer group: если мы задеплоим несколько инстансов одного сервиса, который является stream-processor - Kafka будет считать их как один обработчик



----
#### [[StreamsConfig - Kafka Streams - Flashcards|Link to flashcards]]



---
### References:

