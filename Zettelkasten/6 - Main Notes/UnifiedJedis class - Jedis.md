
09-10-2025 14:46

Status: #child - no flashcards needed

Tags: [[Redis + Jedis]] [[Java+]]

---
# UnifiedJedis class - Jedis

Нужен для работы с доп. модулями Jedis

Расширяет функционал класса Jedis:

extends [[Jedis class - Jedis|Jedis]]

---

### ~={pink}Thread unsafe=~

если создаётся на основе обычного Jedis без пула потоков


### ~={green}Thread safe=~

one `UnififedJedis` for all the threads

```java
JedisPool jedisPool = new JedsiPool("localhost", 6379);

PooledConnectionProvider provider = new PooledConnectionProvider(jedisPool);

UnifiedJedis uJedis = new UnifiedJedis(provider);
```


---
### References:

- [[Jedis class - Jedis]]