
30-06-2026 15:33

Status:

Tags: [[Spring WebFlux]] [[Spring Web]] [[Spring]]

---
# WebClient - Spring WebFlux



Современный HTTP-клиент Spring для выполнения REST-запросов.

* пришёл на замену `RestTemplate`;
* является частью **Spring WebFlux**;
* поддерживает как **неблокирующие**, так и **блокирующие** вызовы;
* работает поверх Reactor (`Mono`/`Flux`).


---
# Зависимости

### Spring Boot 3.x

Самый распространённый вариант:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-webflux</artifactId>
</dependency>
```

Он подтянет:

* `spring-webflux`
* `reactor-core`
* `reactor-netty` (HTTP-клиент по умолчанию).

---

## Обязательно ли подключать весь `spring-boot-starter-webflux`?

**Нет.**

Если нужен **только WebClient**, можно подключить лишь модуль:

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webflux</artifactId>
</dependency>
```

Но тогда придётся самостоятельно позаботиться о реализации HTTP-клиента (например, Reactor Netty или JDK HttpClient), а также вы потеряете часть удобной автоконфигурации Spring Boot. На практике в большинстве Boot-проектов используют именно `spring-boot-starter-webflux`. 

> **Начиная со Spring Boot 4** появился отдельный стартер `spring-boot-starter-webclient`, предназначенный именно для HTTP-клиентов без полного WebFlux-стека. Но для Spring Boot 3.x его ещё нет. ([Maven Repository][2])

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-webclient</artifactId>
    <version>4.1.0</version>
    <scope>compile</scope>
</dependency>
```


---
# `WebClient` - интерфейс

 Имеет единственную реализацию - DefaultWrbClient`


```java
import org.springframework.web.reactive.function.client.WebClient;
```

Использует еактивные типы:

```java
import reactor.core.publisher.Mono;
import reactor.core.publisher.Flux;
```

---

# Создание клиента


```java
WebClient.create("https://api.example.com");
```

Или:

```java
WebClient client = WebClient.builder() 
		.baseUrl("http://localhost:8080") 
		.defaultCookie("cookieKey", "cookieValue") 
		.defaultHeader(
			HttpHeaders.CONTENT_TYPE, 
			MediaType.APPLICATION_JSON_VALUE) 
		.defaultUriVariables(
			Collections.singletonMap("url", "http://localhost:8080")
		) 
		.build();
```

> [!Note] Default Timeout
> By default `WebClient` has timeout of 30 seconds.

#### Настройка таймаутов

We can:

- **set the connection timeout via the `ChannelOption.CONNECT_TIMEOUT_MILLIS` option**
- **set the read and write timeouts using a `ReadTimeoutHandler` and a `WriteTimeoutHandler`, respectively**
- **configure a response timeout using the `responseTimeout` directive**

```java
HttpClient httpClient = HttpClient.create()
	.option(ChannelOption.CONNECT_TIMEOUT_MILLIS, 5000)
	.responseTimeout(Duration.ofMillis(5000)) 
	.doOnConnected(conn ->
		 conn.addHandlerLast(new ReadTimeoutHandler(5000, TimeUnit.MILLISECONDS)) 
			 .addHandlerLast(
				 new WriteTimeoutHandler(5000, TimeUnit.MILLISECONDS)
				 )
			 ); 
	 
WebClient client = WebClient.builder() 
	.clientConnector(new ReactorClientHttpConnector(httpClient)) 
	.build();
```

> [!note]
> **While we can call _timeout_ on our client request as well, this is a signal timeout, not an HTTP connection, a read/write, or a response timeout; it’s a timeout for the Mono/Flux publisher.**


---

# Типичный запрос

```java
Mono<UserDto> user = client.get()
        .uri("/users/{id}", id)
        .retrieve()
        .bodyToMono(UserDto.class);
```

---

# Основные методы

Получить один объект

```java
bodyToMono(User.class)
```

Получить поток объектов

```java
bodyToFlux(User.class)
```

---

# `retrieve()` vs `exchangeToMono()`

### `retrieve()`

Самый простой вариант.

```java
client.get()
      .retrieve()
      .bodyToMono(User.class);
```

Подходит примерно для 90% случаев.

---

### `exchangeToMono()`

Даёт доступ ко всему HTTP Response:

* статусу;
* заголовкам;
* телу;
* можно самостоятельно обработать ошибки.

```java
client.get()
      .exchangeToMono(response -> {
          if (response.statusCode().is2xxSuccessful()) {
              return response.bodyToMono(User.class);
          }
          return Mono.error(...);
      });
```

---

# Обработка ошибок

```java
.retrieve()
.onStatus(HttpStatusCode::is4xxClientError,
    response -> Mono.error(...))
```

---

# Реактивность

WebClient возвращает

```
Mono<T>
```

или

```
Flux<T>
```

Сам запрос начинает выполняться только после подписки.

Например

```java
.subscribe(...)
```

или

```java
.block()
```

---

# `.block()`

Позволяет использовать WebClient как обычный синхронный клиент.

```java
User user =
    client.get()
          .retrieve()
          .bodyToMono(User.class)
          .block();
```

Работает даже в Spring MVC.

Но в реактивном приложении использовать `block()` крайне нежелательно — он блокирует поток и лишает приложение преимуществ неблокирующей модели. 


---
### Пример клиента + [[Resilience4j]]

```java
@Service
@RequiredArgsConstructor
public class UserServiceClient {

    private final WebClient userServiceWebClient;

    @CircuitBreaker(name = "user-service", fallbackMethod = "getUserFallback")
    @Retry(name = "user-service")
    @TimeLimiter(name = "user-service")
    public CompletableFuture<UserResponse> getUser(long id) {

        return userServiceWebClient
                .get()
                .uri("/users/{id}", id)
                .retrieve()
                .bodyToMono(UserResponse.class)
                .toFuture();
    }

    private CompletableFuture<UserResponse> getUserFallback(
            long id,
            Throwable ex) {

        return CompletableFuture.completedFuture(
                new UserResponse(
                        id,
                        "Unknown",
                        "unavailable@example.com"
                )
        );
    }
}
```

Почему используется `CompletableFuture`?

`TimeLimiter` в Resilience4j работает с асинхронными типами (`CompletionStage`/`CompletableFuture`). Поэтому `Mono` преобразуется:

```java
.bodyToMono(UserResponse.class)
.toFuture();
```

`CompletableFuture` может завершиться двумя способами:

- **успешно** (`completed`)
- **с ошибкой** (`completed exceptionally`)

Resilience4j умеет отслеживать оба варианта.

Предположим, сервер вернул 500. 
`bodyToMono(User.class)` не вернет `Mono<User>` с ошибочным значением.

Он создаст

```java
Mono.error(new WebClientResponseException(...))
```

То есть реактивный поток завершится ошибкой.

Он делает примерно следующее:

```java
CompletableFuture<User> future = originalMethod();

future.whenComplete((result, throwable) -> {

    if (throwable == null) {
        // success
    } else {
        // failure
    }

});
```

Именно через `whenComplete()` (или аналогичный callback) он узнаёт, чем завершился асинхронный вызов.


---

# Runtime

Сам WebClient не открывает сокеты.

Он использует один из HTTP-клиентов:

* Reactor Netty (по умолчанию)
* Jetty
* Apache HttpClient
* JDK HttpClient

Spring Boot автоматически выбирает подходящий `ClientHttpConnector`. 

---

# Настройка клиента

Можно задать

* Base URL

```java
.baseUrl(...)
```

* Default Headers

```java
.defaultHeader(...)
```

* Filters (логирование, JWT, retry)

```java
.filter(...)
```

* Timeouts

через настройку HTTP-клиента (`Reactor Netty` и др.).

---

# Преимущества

✅ неблокирующий I/O

✅ высокая производительность при большом количестве запросов

✅ поддержка Streaming

✅ SSE

✅ HTTP/2

✅ удобная композиция нескольких запросов (`zip`, `flatMap`)

---

# Использование в обычном Spring MVC

Можно.

Для этого **не нужно переводить всё приложение на WebFlux**.

Например:

```java
User user =
    webClient.get()
             .retrieve()
             .bodyToMono(User.class)
             .block();
```

Но если приложение полностью синхронное и не использует реактивность, то в современных версиях Spring для таких сценариев чаще рекомендуют использовать `RestClient`, а не `WebClient`. `WebClient` остаётся предпочтительным выбором для асинхронных, потоковых и реактивных сценариев. ([Home][1])

---

----
#### [[WebClient - Spring WebFlux - Flashcards|Link to flashcards]]



---
### References:

