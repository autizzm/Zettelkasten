
09-10-2025 15:18

Status: #child -- no flashcards needed

Tags: [[Redis + Jedis]] [[Java+]]

---
# Sets - Redis


Неупорядоченная коллекция уникальных строк ([[Strings - Redis|Strings]])



## Операции

- `SADD <set_name> <value> [<value>]` - вставляет значение в Set, возвращает количество вставленных:
```java
long res1 = jedis.sadd("bikes:racing:france", "bike:1");
System.out.println(res1);  // >>> 1

long res2 = jedis.sadd("bikes:racing:france", "bike:1");
System.out.println(res2);  // >>> 0
```


- `SREM <set_name> <value>` - удалит значение из Set
	
	
- `SISMEMBER <set_name> <value>` - возвращает boolean результат, есть ли это значение в Set
```java
jedis.sadd("bikes:racing:france", "bike:1", "bike:2", "bike:3");
jedis.sadd("bikes:racing:usa", "bike:1", "bike:4");

boolean res5 = jedis.sismember("bikes:racing:usa", "bike:1");
System.out.println(res5);  // >>> true

boolean res6 = jedis.sismember("bikes:racing:usa", "bike:2");
System.out.println(res6);  // >>> false
```


- `SCARD <set_name>` - возвращает размер этого Set
```java
jedis.sadd("bikes:racing:france", "bike:1", "bike:2", "bike:3");

long res8 = jedis.scard("bikes:racing:france");
System.out.println(res8);  // >>> 3
```


---
### References:

