
09-10-2025 11:05

Status: #child - no flashcards needed

Tags: [[Tablesaw]]

---
# Table - Tablesaw


### Создание таблицы в коде

```java
String[] animals = {"bear", "cat", "giraffe"};
double[] cuteness = {90.1, 84.3, 99.7};

Table cuteAnimals =
    Table.create("Cute Animals")
        .addColumns(
            StringColumn.create("Animal types", animals),
            DoubleColumn.create("rating", cuteness));
```



### Импорт таблицы из файла

[Issues with data import](https://jtablesaw.github.io/tablesaw/userguide/importing_data)

```java
Table bushTable = Table.read().csv("../data/bush.csv");
```


### Экспорт таблицы в файл

```java
table.write().csv("employees.csv");
```


---

## Методы Table

- ~={green}.structure()=~
	
```java
System.out.println(bushTable.structure())

//Output:
          Structure of bush.csv          
 Index  |  Column Name  |  Column Type  |
-----------------------------------------
     0  |         date  |   LOCAL_DATE  |
     1  |     approval  |      INTEGER  |
     2  |          who  |       STRING  |
```


- ~={green}.shape()=~
	
```java
System.out.println(bushTable.shape())

//Output:
323 rows X 3 cols
```


- ~={green}.first(`int amt`)=~
	
```java
System.out.println(bushTable.first(3))

//Output:
             bush.csv              
    date     |  approval  |  who  |
-----------------------------------
 2004-02-04  |        53  |  fox  |
 2004-01-21  |        53  |  fox  |
 2004-01-07  |        58  |  fox  |
```


- ~={green} .last(`int amt`)=~
	
```java
System.out.println(bushTable.last(3))

//Output:
              bush.csv               
    date     |  approval  |   who   |
-------------------------------------
 2001-03-27  |        52  |  zogby  |
 2001-02-27  |        53  |  zogby  |
 2001-02-09  |        57  |  zogby  |
```


- ~={green}.printAll() =~ - выведет все строки


- ~={green}.print(`int n`)=~ - выведет n строк


### Работа со строками Table

осуществляется методами Table

```java
Table result = table.dropDuplicateRows();
result = table.dropRowsWithMissingValues();

// drop rows using Selections
result = table.dropWhere(table.numberColumn(0).isLessThan(100));

// add rows
destinationTable.addRow(43, sourceTable); // adds row 43 from sourceTable to the receiver

// sampling
table.sampleN(200); // select 200 rows at random from table
```


#### **Итерация по строкам:**

```java
for (Row row : table) {
  System.out.println("On " + row.getDate("date") + ": " + row.getDouble("approval"));
}
```

```java
table.stream()
    .forEach(
        row -> {
          System.out.println("On " + row.getDate("date") + ": " + row.getDouble("approval"));
        });
```



Также Tablesaw поддерживает [сортировку](https://jtablesaw.github.io/tablesaw/userguide/sorting) и фильтрацию (пока просто не конспектировал) Table



----
#### [[Table - Tablesaw - Flashcards|Link to flashcards]]



---
### References:

- [[Classpath - Java]]