
23-09-2025 09:59

Status: #child 

Tags: [[Java Core]] [[Java Important Classes]]

---
# AutoClosable Interface - Java

```java
public interface AutoCloseable {
    void close() throws Exception;
}
```

Класс, реализующий AutoCloseable, обязан определить метод close().


---
#### Для чего нужен

Главное применение — это конструкция try-with-resources (добавленная в Java 7).
Она гарантирует, что ресурс будет закрыт автоматически, даже если внутри блока возникнет исключение.
	
Пример
```java
Copy code
try (BufferedReader br = new BufferedReader(new FileReader("file.txt"))) {
    System.out.println(br.readLine());
} catch (IOException e) {
    e.printStackTrace();
}
```

Здесь:
	
- BufferedReader реализует AutoCloseable.
	
- После завершения блока try (нормально или при ошибке) вызовется br.close() автоматически


----
#### [[AutoClosable Interface - Java - Flashcards|Link to flashcards]]



---
### References:

