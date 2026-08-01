
22-07-2026 11:05

Status:

Tags: [[Spring Boot Actuator]] [[Spring]]

---
# Основные эндпоинты - Spring Boot Actuator



![[Pasted image 20260722110514.png]]


**Additional:**

| ID           | Description                                                                                                                        | Enabled by default |
| ------------ | ---------------------------------------------------------------------------------------------------------------------------------- | ------------------ |
| `prometheus` | Exposes metrics in a format that can be scraped by a Prometheus server. Requires a dependency on `micrometer-registry-prometheus`. | No                 |


Большинство из них включены.
Но мало какие **exposed:**


### Exposing Actuator Endpoints

Expose all:

```properties
# так не надо делать
management.endpoints.web.exposure.include=*
```

Explicitly exposing:

```properties
management.endpoints.web.exposure.include=health, env, metrics, loggers
```

### + enable them in the Spring Security Config:

```java
@Bean
public SecurityFilterChain securityWebFilterChain(HttpSecurity http) throws Exception {
    return http
      .authorizeHttpRequests(auth -> auth
        .requestMatchers("/actuator/**").permitAll()
        .anyRequest().authenticated())
      .build();
}
```



---
## Notables on each endpoint



### Env

```properties
management.endpoint.env.show-value=ALWAYS
```

По дефолту, всё, что exposed in the endpoints is starred out (заблюрено), это включит explicit отображение.

НЕ БЕЗОПАСНО


### Health

By default call to `localhost:8080/actuator/health` вернёт просто:

```json
{"groups":["liveness","readiness"],"status":"UP"}
```


Additional setings:

```yaml
management:
	endpoint:
		health:
		show-compontents: ALWAYS
		show-details: always
		probes:
			enabled: true
```


### Loggers

`localhost:8080/actuator/loggers`


Вернёт список логгеров и их уровни логирования


### Beans

`localhost:8080/actuator/beans`

Вернёт описание всех бинов, зарегистрированных в контексте.


### Conditions

`localhost:8080/actuator/conditions`

Вернёт списов всех conditions (evaluation result of `@ConditionalOnProperty`, `@ConditionalOnWebApplication`, `@ConditionalonMissingBean`)

![[Pasted image 20260722112746.png]]



### ScheduledTasks

`localhost:8080/actuator/scheduledtasks`

вернет информацию о scheduled tasks:

```json
{"cron":[], "fixedDelay": [], "fixedRate": [], "custom": [] }
```


### ThreadDump

`localhost:8080/actuator/threaddump`

Вернёт информацию о всех существующих потоках

![[Pasted image 20260722113244.png]]



### Shutdown

Останавливает приложения. 

**Не включен по умолчанию**

```properties
management.endpoint.shutdown.enabled=true
```

> Вызывать этот endpoint нужно с `POST` 



### Metrics


Тоже нужно explicitly включать:

```properties
management.endpoint.metrics.enabled=true
```


`localhost:8080/actuator/metrics` - вернёт сразу все метрики


Чтобы получить информацию об одной конкретной метрике:

`localhost:8080/actuator/metrics/systme.cpu.count`

----
#### [[Основные эндпоинты - Spring Boot Actuator - Flashcards|Link to flashcards]]



---
### References:

