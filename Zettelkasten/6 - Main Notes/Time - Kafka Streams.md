
11-08-2026 13:21

Status:

Tags: [[Kafka Streams]] [[Kafka]] [[Java+]]

---
# Time - Kafka Streams


Common notions of time in streams are:

- **Event time** - Время, когда случился сам ивент. 
**Example:** If the event is a geo-location change reported by a GPS sensor in a car, then the associated event-time would be the time when the GPS sensor captured the location change.

- **Processing time** - Время, когда наш обработчик закончил обработку ивента.
**Example:** Imagine an analytics application that reads and processes the geo-location data reported from car sensors to present it to a fleet management dashboard. Here, processing-time in the analytics application might be milliseconds or seconds (e.g. for real-time pipelines based on Apache Kafka and Kafka Streams) or hours (e.g. for batch pipelines based on Apache Hadoop or Apache Spark) after event-time.

- **Ingestion time** - Timestamp записи ивента в брокер (после того, как producer отправил ивент брокеру)
**For example,** if a record is never processed, there is no notion of processing time for it, but it still has an ingestion time.

The choice between event-time and ingestion-time is actually done through the configuration of Kafka (not Kafka Streams): From Kafka 0.10.x onwards, timestamps are automatically embedded into Kafka messages. Depending on Kafka’s configuration these timestamps represent event-time or ingestion-time. The respective Kafka configuration setting can be specified on the broker level or per topic. The default timestamp extractor in Kafka Streams will retrieve these embedded timestamps as-is. Hence, the effective time semantics of your application depend on the effective 
Kafka configuration for these embedded timestamps.


#### Пример

```java
KStream<String, InternalEvent> repartitioned = source
		// .filter - stateless
        .filter((key, event) -> 
	        selector.apply(event) != null && !selector.apply(event).isBlank())
	    //.selectKey - stateless
        .selectKey((key, event) -> selector.apply(event));

for (Duration window : WINDOWS) {
    KTable<Windowed<String>, MetricState> table = repartitioned
			// .groupBy - still stateless
            .groupByKey(Grouped.with(Serdes.String(), eventSerde))
            // .windowedBy - stateful
            .windowedBy(TimeWindows.ofSizeAndGrace(window, Duration.ofMinutes(30)))
            .aggregate(MetricState::empty,
                    (key, event, state) -> 
	                    MetricAggregator.forType(MetricType.SUM).add(state, event),
                    Materialized.with(
	                    Serdes.String(), new JsonSerde<>(MetricState.class))
					);
					
    table.toStream().foreach(
	    (windowedKey, state) -> 
		    persist(repository, dimension, windowedKey, state, false)
	);
	table.suppress(
		Suppressed
			.untilWindowCloses(Suppressed.BufferConfig.unbounded()))
            .toStream()
            .foreach(
	            (windowedKey, state) -> 
		            persist(repository, dimension, windowedKey, state, true)
			);
			
    addCompletionRatio(repartitioned, dimension, window, eventSerde, repository);
}
```

`groupBy` - репартиционирует по `input time`. Если в Kafka настроено, что timestamp сообщения это `event time` - будет сохранен `event time`, а если настроено, что `ingetstion` - сохранится `ingestion`.
 
> Да, серьёзно, а ты не верил...

> [!note]
> Тут `groupBy` в самом теле цикла, потому что widowedBy - изменяет сам Stream, а не копирует из него сообщения и создаёт новый


### Stream time

**stream time** - время последней обработанной записи.

Kafka Streams assigns a **timestamp** to every data record via the `TimestampExtractor` interface. These per-record timestamps describe the progress of a stream with regards to time and are leveraged by time-dependent operations such as window operations. As a result, this time will only advance when a new record arrives at the processor. We call this data-driven time the **stream time** of the application to differentiate with the **wall-clock time** when this application is actually executing. Concrete implementations of the `TimestampExtractor` interface will then provide different semantics to the stream time definition. For example retrieving or computing timestamps based on the actual contents of data records such as an embedded timestamp field to provide event time semantics, and returning the current wall-clock time thereby yield processing time semantics to stream time. Developers can thus enforce different notions of time depending on their business needs.

### Time of the output messages of a stream processor

В Kafka Streams при записи новых записей в Kafka временные метки назначаются так:

- При обработке входной записи (например, через `context.forward()`) —  время наследуются от входной записи (input event).

- При периодических вызовах (`Punctuator#punctuate()`) — берётся текущее внутреннее время задачи (`context.timestamp()`).

- Для агрегаций — максимальная временная метка среди всех записей, которые внесли вклад в результат.

Дополнительные правила:

- **Joins** (stream-stream, table-table): `max(left.ts, right.ts)`.

- **Stream-table joins**: временная метка берётся из stream-записи.

- **Агрегации**: максимум по ключу (глобально или по окну).

- ~={orange}**Stateless-операции** (включая `flatMap`): временная метка входной записи передаётся дальше.=~


В Processor API (низкоуровневый API работы с Kafka Streams) можно явно задать временную метку при вызове `#forward()`.




----
#### [[Time - Kafka Streams - Flashcards|Link to flashcards]]



---
### References:

