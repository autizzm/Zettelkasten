
09-10-2025 10:59

Status: #child - no flashcards needed

Tags: [[Tablesaw]]

---
# Column - Tablesaw

Tablesaw supports columns for Strings, floats, doubles, ints, shorts, longs, booleans, LocalDates, LocalTimes, Instants, and LocalDateTimes. The date and time columns are compatible with the java.time classes introduced in Java 8.

### Создание объекта Column

```java
double[] numbers = {1, 2, 3, 4};
DoubleColumn nc = DoubleColumn.create("nc", numbers);
System.out.println(nc.print());

//Output:
Column: nc
1
2
3
4

```

> [!note] Для колонки int (и всех числовых колонок)
> ```java
> IntColumn.create("id", 234); //создаст колонку размером 234
> IntColumn.create("id", int[] {234}); //создаст колонку и сложит туда 234
> ```

### Получение значения из колонки по индексу:

~={red}!!!=~ Индексация начинается с 0 ~={red}!!!=~

```java
double three = nc.get(2);
```



#### Изменение имени колонки
```java
myColumn.setName(aString);
```

---
### References:

