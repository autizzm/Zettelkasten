

09-10-2025 14:53

Status: #child - no flashcards needed

Tags: [[Redis + Jedis]] [[Java+]]

---
# Настройка JedisPooled через GenericObjectPoolConfig

`GenericObjectPoolConfig` from [Apache Commons Pool2](https://commons.apache.org/proper/commons-pool/apidocs/org/apache/commons/pool2/impl/GenericObjectPoolConfig.html).

```java
ConnectionPoolConfig poolConfig = new ConnectionPoolConfig();
// maximum active connections in the pool,
// tune this according to your needs and application type
// default is 8
poolConfig.setMaxTotal(8);

// maximum idle connections in the pool, default is 8
poolConfig.setMaxIdle(8);
// minimum idle connections in the pool, default 0
poolConfig.setMinIdle(0);

// Enables waiting for a connection to become available.
poolConfig.setBlockWhenExhausted(true);
// The maximum number of seconds to wait for a connection to become available
poolConfig.setMaxWait(Duration.ofSeconds(1));

// Enables sending a PING command periodically while the connection is idle.
poolConfig.setTestWhileIdle(true);
// controls the period between checks for idle connections in the pool
poolConfig.setTimeBetweenEvictionRuns(Duration.ofSeconds(1));

// JedisPooled does all hard work on fetching and releasing connection to the pool
// to prevent connection starvation
JedisPooled jedis = new JedisPooled(poolConfig, "localhost", 6379);

```


---
### References:

- [[Jedis class - Jedis]]