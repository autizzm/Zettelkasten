
01-09-2025 19:57

Status:

Tags: [[Java Core]]

---
# Array

### Инициализация
```java
int[] array = new int[10];

int[] array3 = new int[]{1, 2, 3};
```

#### Инициализация многомерных массивов
```java
int[][] arr = new int[3][3]; // три строки по три элемента, все = 0

int[][] arr = new int[3][];  // подмассивы инициализируются как null
```


> [!note]
> В Java многомерные массивы - это массив массивов. => Каждый внутренний массив может иметь свою длину.

```java
int[][] arr = new int[3][];

arr[0] = new int[]{1, 2, 3};
arr[1] = new int[]{3, 8};
arr[2] = new int[]{1, 1, 0};

```

- `arr[1].length == 2`
    
- `arr[1][2]` не существует → `ArrayIndexOutOfBoundsException`.

### Useful methods:

> [!warning]
> Чтобы получить длину массива надо получить значение public-поля length. НЕ МЕТОД

- ~={red}check Arrays class=~


----
#### [[Array Flashcards|Link to flashcards]]



---
### References:

[[Arrays class]]