
Theory for the cards: [[Array]]

FILE TAGS: java_core

Q: Способы инициализации массива
A: 
```java
int[] array = new int[10];

int[] array3 = new int[]{1, 2, 3};
```

Q: Может ли внутренний массив многомерного массива (`int[1]`)быть короче значения, заданного при инициализации многомерного массива (`int arr[3][]`)?
A: Да, может:
<!--ID: 1756747801681-->


```java
int[][] arr = new int[3][];

arr[0] = new int[]{1, 2, 3};
arr[1] = new int[]{3, 8};
arr[2] = new int[]{1, 1, 0};

//arr[1].length == 2
//arr[1][2] не существует → `ArrayIndexOutOfBoundsException`.
```


Q: как получить длину массива int[] array?
A: array.length
<!--ID: 1756747801690-->
