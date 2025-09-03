
Theory for the cards: [[Cycles]]

FILE TAGS: java_core

Q: какой цикл гарантированно выполняется хоть раз
A: do-while
<!--ID: 1756744840506-->


Q: как остановить (или продолжить) выполнение внешнего цикла из области видимости внутреннего цикла?
A: labeled for-break (for-continue):
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
<!--ID: 1756744840517-->

Q: Что происходит под капотом при выполнении `for(Object o : someCollection)`?
A:  
```java
for (Iterator<Object> it = someCollection.iterator(); it.hasNext();) {
    Object o = it.next();
    ...
}
```
<!--ID: 1756829227864-->
