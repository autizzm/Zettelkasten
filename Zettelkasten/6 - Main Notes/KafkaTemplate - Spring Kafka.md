
03-02-2026 15:20

Status:

Tags: [[Spring Kafka]] [[Kafka]] [[Spring]]

---
# KafkaTemplate - Spring Kafka

A template for executing high-level operations. When used with a [`DefaultKafkaProducerFactory`](https://docs.spring.io/spring-kafka/docs/4.0.x/api/org/springframework/kafka/core/DefaultKafkaProducerFactory.html "class in org.springframework.kafka.core"), the template is thread-safe. The producer factory and `KafkaProducer` ensure this; refer to their respective javadocs.


Auto-configured if we use Spring Boot. -> we can straight up inject it.


When not using [Transactions](https://docs.spring.io/spring-kafka/reference/kafka/transactions.html), by default, the `DefaultKafkaProducerFactory` creates a ~={cyan}singleton producer used by all clients=~, as recommended in the `KafkaProducer` JavaDocs. ~={orange}However, if you call `flush()` on the template, this can cause delays for other threads using the same producer=~. Starting with version 2.3, the `DefaultKafkaProducerFactory` has a new property `producerPerThread`. When set to `true`, the factory will create (and cache) a separate producer for each thread, to avoid this issue.

> [!warning]
> Если устанавливаем `producerPerThread` = `true`, необходимо вызвать `closeThreadBoundProducer()` на объекте `DefaultKafkaProducerFactory`?, когда Producer больше не нужен. 
> 
> Это уничтожит Producer и уберет его из`ThreadLocal`.
> Тут подробнее: https://docs.spring.io/spring-kafka/reference/kafka/sending-messages.html#producer-factory


### `KafkaTemplate` methods

```java
CompletableFuture<SendResult<K, V>> sendDefault(V data);

CompletableFuture<SendResult<K, V>> sendDefault(K key, V data);

CompletableFuture<SendResult<K, V>> sendDefault(Integer partition, K key, V data);

CompletableFuture<SendResult<K, V>> sendDefault(Integer partition, Long timestamp, K key, V data);

CompletableFuture<SendResult<K, V>> send(String topic, V data);

CompletableFuture<SendResult<K, V>> send(String topic, K key, V data);

CompletableFuture<SendResult<K, V>> send(String topic, Integer partition, K key, V data);

CompletableFuture<SendResult<K, V>> send(String topic, Integer partition, Long timestamp, K key, V data);

CompletableFuture<SendResult<K, V>> send(ProducerRecord<K, V> record);

CompletableFuture<SendResult<K, V>> send(Message<?> message);

Map<MetricName, ? extends Metric> metrics();

List<PartitionInfo> partitionsFor(String topic);

<T> T execute(ProducerCallback<K, V, T> callback);

<T> T executeInTransaction(OperationsCallback<K, V, T> callback);

// Flush the producer.
void flush();

interface ProducerCallback<K, V, T> {

    T doInKafka(Producer<K, V> producer);

}

interface OperationsCallback<K, V, T> {

    T doInOperations(KafkaOperations<K, V> operations);

}
```





### Подробно про настройку `KafkaTemplate`


> [!note] 
> В `Producerfactory<K,V>`:
> `K` - key
> `V` - value

Т.е., [[Schema Registry (стандартизация DTO) - Kafka#Protobuf + KafkaProtobufSerializer|если используем свои классы, например с protobuf, то POJO классы, созданные protoc компилятором]], будут идти в качестве value, а для их сериализации используем `io.confluent.kafka.serializers.protobuf.KafkaProtobufSerializer`


Вся настройка происходит через `Map` аргументов, передавайемых в `DefaultKafkaProducerFactory`, на основе которой создаётся `KafkaTemplate`:


```java
@Configuration  
public class KafkaProducerConfig {  
  
    @Value("${application.kafka.bootstrap-servers}")  
    private String bootstrapServers;  
  
  
    public Map<String, Object> producerConfig() {  
        Map<String, Object> props = new HashMap<>();  
        props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapServers);  
        props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);  
        props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class);  
        // See https://kafka.apache.org/41/documentation/#producerconfigs for more properties
        return props;  
    }  
  
    @Bean  
    public ProducerFactory<String, String> producerFactory() {  
        return new DefaultKafkaProducerFactory<>(producerConfig());  
    }  
  
    @Bean  
    public KafkaTemplate<String, String> kafkaTemplate(  
            ProducerFactory<String, String> producerFactory  
    ) {  
        return new KafkaTemplate<>(producerFactory);  
    }  
}
```


### Отправка объектов и их сериализация

Типы `KafkaTemplate<K, V>` должны соответствовать тому, что умеют сериализовывать указанные `Serializer`'ы. -> Если используем StringSerializers, то перед тем, как передать сообщение в метод send, его придетсся вручную сериализовать с помощью `ObjectMapper`.

##### Вариант 2. Использовать JsonSerializer

Spring Kafka предоставляет

```java
org.springframework.kafka.support.serializer.JsonSerializer
```

Тогда

```java
spring.kafka.producer.value-serializer=
org.springframework.kafka.support.serializer.JsonSerializer
```

и можно писать

```java
KafkaTemplate<String, PaymentCreatedEvent>
```

без ручной сериализации.

```java
kafkaTemplate.send(
    "payments",
    event
);
```

Spring сам сделает:
```
PaymentCreatedEvent
↓

Jackson

↓

JSON

↓

byte[]
```


#### Какие Serializer существуют

Apache Kafka поставляется с набором стандартных сериализаторов.

|Serializer|Java тип|
|---|---|
|`StringSerializer`|`String`|
|`IntegerSerializer`|`Integer`|
|`LongSerializer`|`Long`|
|`ShortSerializer`|`Short`|
|`DoubleSerializer`|`Double`|
|`FloatSerializer`|`Float`|
|`ByteArraySerializer`|`byte[]`|
|`BytesSerializer`|`org.apache.kafka.common.utils.Bytes`|
|`UUIDSerializer`|`UUID`|
|`VoidSerializer`|`Void`|



### Отправка сообщения


```java
@SpringBootApplication
public class Sender {
	public static void main(String[] args){
		SpringApplication.run(KafkaApplication.class, args);
	}
	
	@Bean
	CommandLineRunner commandLineRunner(KafkaTemplate<String, String> kafkaTemplate) {
		return args -> {
			kafkaTemplate.send("mytopic", "hello kafka").get(10, TimeUnit.SECONDS); //synchronously waiting for the result
			
			CompletableFuture<SendResult<String, String>> future = template.send(record); 
			future.whenComplete((result, ex) -> {
				if (ex == null) { 
					handleSuccess(data); 
				} else { 
					handleFailure(data, record, ex); 
				} 
			});
		}
	}
}

```

[[CommandLineRunner и ApplicationRunner- Spring Boot|Что такое CommandLineRunner]] 



----
#### [[KafkaTemplate - Spring Kafka - Flashcards|Link to flashcards]]



---
### References:

