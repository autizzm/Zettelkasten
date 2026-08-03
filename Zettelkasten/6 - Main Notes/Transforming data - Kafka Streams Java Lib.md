
02-08-2026 10:31

Status:

Tags: [[Kafka Streams Java Lib]] [[Kafka Streams]] [[Kafka]]

---
# Transforming data - Kafka Streams Java Lib


![[Pasted image 20260802105506.png]]

> [!tip] **How Stateful operations' state is stored?**
>State в Kafka Streams для stateful operations хранится **локально на инстансе приложения** в state store, а не на broker’е. Обычно это **RocksDB на диске** внутри процесса/хоста приложения, а для отказоустойчивости состояние дублируется в **changelog topic** в Kafka, из которого оно восстанавливается после рестарта или падения инстанса. 



#### `map` & `filter`


**Example:**

```java
final KStream<Long, PlayEvent> playsBySongId = playEvents
	.filter(
		(region, event) -> event.getDuration() >= MIN_CHARTABLE_DURATION
	)
	.map(
		(key, value) -> KeyValue.pair(value.getSongId(), value)
	);
```

- Фильтрует records по длительности 

- Repartitions based on the new key and value

> [!tip] Note
> Если логика обработки потока становится довольно сложной, что её неудобно пихать в лямбду -> 
> желательно вынести в отдельный метод 
> `+` будет возможность сделать тесты на логику обработки стрима


---
#### `groupBy` & `count`

```java
final KGroupedTable<Long, Long> groupedBySongId = 
	songPlays.groupBy(
		(songId, song) -> //pass in the initial key - value
			songId,  //by what we want to group
			Serdes.Long(), // serde of the result key
			Serdes.Long()  //serde of the result value
	);
groupedBySongId.count("song-play-count"); //name of the state in the internal state storage
```

- `groupBy` - re-partitions the data based on a new key

- `count` - counts the number of occurences of a song, **~={yellow}requires the application to keep state=~**


---
#### `aggregate` - custom aggregation - **~={yellow}stateful=~**

Тут мы в качестве результата используем `TopFiveSongs` - класс содержащий топ 5 песен, то есть сагрегировав все песни, получим не 5 топовых отдельных значений `Song`, а одно значение `TopFiveSongs`.

`TopFiveSongs` имеет методы `add` и `remove`.


```java
final KTable<Song, Long> songPlayCounts = 
	songPlaysKGroupedTable.aggregate(
		TopFiveSongs::new, //constructor of the resulting value
		(aggKey, value, aggregate) -> { //lambda for adding a value to an aggregate
			aggregate.add(value); 
			return aggregate
		},
		(aggKey, value, aggregate) -> { //lambda for removing a value from an aggregate USED MOSTLY FOR WINDOWING. not used here
			aggregate.remove(value);
			return aggregate;
		},
		topFiveSerde, // serde of the resulting value
		"top-five-songs-by-genre" //name of the state in the internal state storage
	);
```

- `aggKey`, `value` - ключ-значение нового элемиента из Stream, который обрабатываем.
- `aggregate` - то, во что собираем. Здесь это instance of the `TopFiveSongs`.



---
### `JOIN` - Enriching a stream

![[Pasted image 20260802104448.png]]


Это leftJoin со стороны KStream, то есть, если не нашли `playsBySongId.key == songTable.key` - используем `null`

```java
final KStream<Long, Song> songPlays = playsBySongId.leftJoin(
	songTable, //name of the Ktable we want to join
	(playEvent, song) -> 
		song, 
		Serdes.Long(), //serde of the result key
		playEventSerde //serde of the result value
);
```

Там, где `playsBySongId.key == songTable.key` - используем табличное значение `song`.

- пришло событие `playEvent` с `songId = 42`;
    
- Kafka Streams смотрит в `songTable` по ключу `42`;
    
- если там есть `Song`, она попадёт в `songPlays`;
    
- если нет, в результат попадёт `null`.

----
#### [[Transforming data - Kafka Streams Java Lib - Flashcards|Link to flashcards]]



---
### References:

