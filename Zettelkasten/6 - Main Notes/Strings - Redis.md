
09-10-2025 14:04

Status: #child - no flashcards needed

Tags: [[Redis + Jedis]] [[Java+]]

---
# Strings - Redis

В Redis~={orange} нет «числовых»=~ типов данных вроде `int` или `float` в классическом понимании.  
Все значения в ключах типа `String` хранятся как **строки байтов**.

Например:
```SQL
SET mykey 100
```
Значение `100` хранится как строка `"100"`.


> [!note]  **Числовые операции работают поверх строк**
Когда вы вызываете числовую команду, например:
>```sql
>INCR mykey
>```
>Redis:
>
>1. Проверяет, что строка в `mykey` может быть распознана как целое число (в 64-битном диапазоне).
>
>   
>1. Парсит строку в число.
  > 
>2. Выполняет операцию (`+1`).
 >  
>3. Конвертирует результат обратно в строку и сохраняет.

---

### Операции над String:

- `SET <key> <value>`
	
- `GET <key>`
	
```java
String status = jedis.set("k-1", "v-1");
if(status.equals("OK")){
	System.out.println("Successfully saved");
}
String value = jedis.get("k-1");
if(value != null){
	System.out.println("Successfully fetched value = " + value);
}
```

- `SETNX <key> <value>` - stores a string value only if the key doesn't already exist.
	
- `MSET <key> <value> [<key> <value>]` - sets multiple string values in a single operation.
	
- `MGET <key> [<key>]` - retreives multiple string values in a single operation.


#### Удаление строк из Redis

- `DEL <key> [<key>]` - удаляет значение по ключу, ~={orange}если ключа не существует - игнорирует его.=~

```java
long linesDeleted = jedis.del("key-1", "key-2"); 
```



---
#### Операции над числами (хранящимися в String):

- `INCR <key>` - увеличивает значение по ключу на 1
	
- `INCRBY <key> <integer_increment>` - увеличивает значение по ключу на `<integer_increment>` 
	
- `INCRBYFLOAT <key> <float_increment>` - увеличивает значение по ключу на `<float_increment>` 


```java
String incrResult1 = jedis.set("myKey", "10");
long incrResult2 = jedis.incr("myKey"); //11
String incrResult3 = jedis.get("myKey"); // "11"
```




----
#### [[Strings - Redis - Flashcards|Link to flashcards]]



---
### References:

