
Theory for the cards: [[AutoClosable Interface - Java]]

FILE TAGS: java_important_classes

Q: Что за интерфейс AutoClosable и для чего он нужен? Какие поля и методы он имеет?
A: AutoClosable - интерфейс, гарантирующий, что класс имеет метод close(), который вызывается в try-with-resources.
```java
public interface AutoCloseable {
    void close() throws Exception;
}
```
Нужен - ТОЛЬКО для try-with-resources.
	
Пример использования:
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
<!--ID: 1758611255827-->
