
09-10-2025 14:33

Status: #child - no flashcards needed

Tags: [[Redis + Jedis]] [[Java+]]

---
# Jedis class - Jedis

Главный класс пакета Jedis 

==not thread safe== => используем JedisPool

Представляет собой соединение с Redis сервером.


implements [[AutoClosable Interface - Java|AutoClosable]]

---

## Создание объекта Jedis

#### **через JedisPool:**

```java
final JedisPoolConfig poolConfig = buildPoolConfig(); //тут задаём параметры для пула
JedisPool jedisPool = new JedisPool(poolConfig, "localhost", 6379);


try(Jedis jedis = jedisPool.getResource()){ //создаём в try-with-resources, т.к. он реализует AutoClosable
	//используем jedis
}
```

#### **через [[JedisPooled class - Jedis|JedisPooled]]:**

```java
JedisPooled jedis = new JedisPooled("localhost", 6379);
jedis.set("foo", "bar");
System.out.println(jedis.get("foo")); // prints "bar"
```

JedisPooled - содержит указанное количество соединений с сервером, слоздаёт новые соединения когда нужно и закрывает их, когда не нужно.


Here is a simplified connection lifecycle in a pool:

1. A connection is requested from the pool.
2. A connection is served:
    - Существуюшее соединение предоставляется, если ещё не все существующие соединения используются
    - A new connection is created when the number of connections is under `maxTotal`.
3. The connection becomes active.
4. The connection is released back to the pool.
5. The connection is marked as stale.
6. The connection is kept idle for `minEvictableIdleTime`.
7. The connection becomes evictable if the number of connections is greater than `minIdle`.
8. The connection is ready to be closed.


### [[Настройка JedisPooled через GenericObjectPoolConfig]]

---
### ~={orange}Warning !!=~ Для работы с доп. модулями Jedis 

нужен [[UnifiedJedis class - Jedis|UnifiedJedis]]



---
### References:

- [[UnifiedJedis class - Jedis]]
- [[Настройка JedisPooled через GenericObjectPoolConfig]]
