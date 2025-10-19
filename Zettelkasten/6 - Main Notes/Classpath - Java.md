
09-10-2025 18:45

Status: #not_even_born 

Tags: [[Java Core]]

---
# Classpath

**Classpath** — это путь (или набор путей), по которому **Java Virtual Machine (JVM)** и **компилятор `javac`** ищут:

- скомпилированные `.class`-файлы,
    
- пакеты и классы библиотек,
    
- сторонние JAR-файлы.
    

📌 Проще говоря:

> **Classpath = “где искать классы и ресурсы”** во время компиляции и выполнения программы.

---

## 📂 Что может входить в classpath

Classpath может включать:

- директории с `.class` файлами (например `out/production/classes`),
    
- JAR-файлы (`lib/spring-context.jar`),
    
- пути к сторонним библиотекам,
    
- wildcard (`lib/*` — подключает все JAR-ы в папке),
    
- ресурсы (например, `application.properties` в Spring).
    

Пример структуры проекта:

```
project/
 ├─ src/
 ├─ out/production/classes/    ← сюда компилируются классы
 ├─ lib/
 │   ├─ spring-core.jar
 │   └─ spring-context.jar
 └─ application.properties
```

Пример classpath:
```
out/production/classes;lib/*
```

(на Windows `;`, на Linux/macOS — `:`)

> [!note]
> Можно указывать путь таким образом:
> ```java
> "classpath:musicPlayer.properties"
> ```


---

## 🧱 На каком этапе определяется classpath

Classpath участвует **на двух этапах**:

### 1. 🧑‍💻 **На этапе компиляции (`javac`)**

- Компилятор ищет классы, на которые ты ссылаешься в коде.
    
- Если нужный класс не найден в classpath — получишь `cannot find symbol` или `package does not exist`.
    

`javac -cp lib/some-lib.jar;. MyApp.java`


---

### Папка `resources` — это **не часть пути** внутри classpath.  

При сборке проекта Maven/Gradle всё содержимое `src/main/resources` попадает **на корень classpath**.

📁 Пример структуры проекта:

```css
project/
 ├─ src/
 │   └─ main/
 │       └─ resources/
 │           ├─ processors.csv
 │           └─ someFolder/
 │               └─ data.csv
 └─ target/
     └─ classes/
         ├─ processors.csv
         └─ someFolder/
             └─ data.csv

```

После сборки в classpath будет:

```bash 
processors.csv 
someFolder/data.csv
```



----
#### [[Что такое classpath - Flashcards|Link to flashcards]]



---
### References:

