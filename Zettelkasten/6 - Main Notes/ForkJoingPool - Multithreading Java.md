
15-02-2026 10:26

Status:

Tags: [[Multithreading]]

---
# ForkJoingPool - Multithreading Java


Идея:
Task разбиваем на более мелкие Subtasks (можно даже рекурсивно, т.е Subtasks тоже разбивать на под-таски). Каждый Task выполняется в своём потоке. 

![[Pasted image 20260215102950.png]]

И если даже эти потоки выполняются на одном ядре процессора, this execution way will still be beneficial, as they may encounter blocking IO. Пока один поток блокируется, ожидая IO, выполняется другой поток.


#### Fork & Join phases

![[Pasted image 20260215103127.png]]


---
### Task stealing

![[Pasted image 20260215102617.png]]

Тут **~={red}red task=~** занимает очень много времени. За это время Thread 2 выполнит все свои таски. И чтобы не простаивать Thread 2 "steals" Task from Thread 1.

> [!note] 
> ForkJoinPool не учитывает некоторые ньюансы hardware:
> - performance boostable CPUs - ядра, которые временно могут увеличивать произволительность
> - efficient CPUs - ядра, которые чуть медленнее, но потребляют намного меньше энергии
>
> ForkJoinPool - doesn't account for that -> его оптимизации ("work stealing") иногда могут быть не самыми эффективными


### How it works in Java

```java
//commonPool - пул, общий на все потоки (singleton)
//tries to adapt performance to the number of CPUs you have on your computer
ForkJoinPool forkJoinPool1 = ForkJoinPool.commonPool(); 

ForkJoinPool forkJoinPool2 = new ForkJoinPool(4);
//parallelism = 4 - amt of threads it will attemp to have running at the same time
//Это ориентир, if we submit too much tasks -> it will automatically increase the amt of threads

//doesn't return result
MyRecursiveAction myRecursiveAction = new MyRecursiveAction(123);
forkJoinPool1.invoke(myRecursiveAction);

//returns result
MyRecursiveTask myRecursiveTask = new MyRecursiveTask(123);
long result = forkJoinPool1.invoke(myRecursiveTask);
```


###### Доп методы ForkJoinPool

```java
//blocks the current thread
long result = forkJoinPool1.invoke(myRecursiveTask);

//doesn't block the current thread
ForkJoinTask<Long> forkJoinTask = forkJoinPool1.submit(myRecursiveTask);
/*
* ForkJoinTask<Long> - ссылка на выполняющийся таск
*/
try {
	//если таск выполнен получим результат
	//если нет - текущи поток блокируется и ждёт завершения таска
	Long result = forkJoinTask.get();
} catch(InterruptedException | ExecutionException e){
	...
}




```





### `RecursiveAction` - класс из `java.util.concurrent`

MyRecursiveAction extends `RecusiveAction`:

```java 
public class MyRecursiveAction extends RecursiveAction {
	
	private long workLoad = 0;
	
	public MyRecursiveACtion(long workLoad){
		this.workLoad = workLoad;
	}
	
	@Override
	protected void compute(){
		
		if(this.workLoad > 16){
			//if work is above threshold, break tasks up into smaller tasks
			System.out.println("Splitting workLoad: " + this.workLoad);
			
			long workLoad1 = this.workLoad / 2;
			long workLoad2 = this.workLoad - workLoad1;
			
			MyRecursiveAction subtask1 = new MyRecursiveAction(workLoad1);
			MyRecursiveAction subtask2 = new MyRecursiveAction(workLoad2);
			
			subtask1.fork();
			subtask2.fork();
			
			//no join phase
			//"fire and forget" - we don't even check whether it have finished
			
		} else {
			System.out.println("Doing work myself: " + this.workLoad);	
		}
	}
}
```


### `RecursiveTask` - класс из пакета `java.util.concurrent`

```java 
public class MyRecursiveTask extends RecursiveTask<Long> {
	
	private long workLoad = 0;
	
	public MyRecursiveTask(long workLoad){
		this.workLoad = workLoad;
	}
	
	@Override
	protected Long compute(){
		
		if(this.workLoad > 16){
			//if work is above threshold, break tasks up into smaller tasks
			System.out.println("Splitting workLoad: " + this.workLoad);
			
			long workLoad1 = this.workLoad / 2;
			long workLoad2 = this.workLoad - workLoad1;
			
			MyRecursiveTask subtask1 = new MyRecursiveTask(workLoad1);
			MyRecursiveTask subtask2 = new MyRecursiveTask(workLoad2);
			
			subtask1.fork();
			subtask2.fork();
			
			//join phase
			long result = 0;
			result += subtask1.join();
			result += subtask2.join();
			//the initial compute() call returns result here (if it forked itself)
			return result;
		} else {
			System.out.println("Doing work myself: " + this.workLoad);	
			return workLoad * 3;
		}
	}
}
```


### Use Cases

Популярен в сценариях с рекурсивно разбиваемыми задачами благодаря механизму work-stealing, который оптимизирует использование потоков.

- Сложная обработка элементов какой-то коллекции

- Рекурсивная обработка массивов или коллекций: например, параллельное суммирование элементов, сортировка (как QuickSort) или поиск в больших данных, где задача делится на подзадачи до порога и затем агрегируется.​​
    
- Параллельные стримы в Java 8+: ForkJoinPool.commonPool() лежит в основе parallelStream(), что делает его косвенно широко используемым для операций над коллекциями без явного кода.​​
    
- Обработка задач с деревьями или графами: рекурсивный обход, где fork() создает подзадачи, а join() собирает результаты.

----
#### [[ForkJoingPool - Multithreading Java - Flashcards|Link to flashcards]]



---
### References:

