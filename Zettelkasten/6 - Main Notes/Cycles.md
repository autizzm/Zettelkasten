
01-09-2025 19:32

Status: #child 

Tags: [[Java Core]]

---
# Cycles


### for-each

Цикл типа `for(Object o : someCollection)` ~={red}является синтаксическим сахаром=~ над:
```java
for (Iterator<Object> it = someCollection.iterator(); it.hasNext();) {
    Object o = it.next();
    ...
}
```


Цикл do-while выполняется минимум единожды:
```java
do {
//statement(s)
}while(expression)
```


#### Labeled for-break (for-continue)
```java
search: //outer loop name
	for(int i = 0; i < 10; i++){
		for(int j = 0; j < 10; j++){
			if(condition){
			...
			break search; //(continue search)
			}
		}	
	}
```


>[!note]
>по массиву тоже можно итерировать с помощью for-each

```java
public boolean hasDuplicate(int[] nums) {
	Set<Integer> encountered = new HashSet<>();
	for(int element: nums){
		if(!encountered.add(element))
			return true;
	}
	return false;
}
```


----
#### [[Cycles Flashcards|Link to flashcards]]




---
### References:

