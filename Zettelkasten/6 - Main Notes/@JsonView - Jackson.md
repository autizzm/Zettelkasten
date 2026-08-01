
29-06-2026 18:23

Status:

Tags: [[Jackson]]

---
# @JsonView - Jackson

`@JsonView` в Jackson позволяет контролировать, какие поля будут участвовать в сериализации и десериализации JSON в зависимости от контекста. Это удобно, когда один и тот же DTO должен возвращаться в разных вариантах: например, публичное API, внутреннее API, краткое представление, подробное представление и т.д.

## Основная идея

Создаются классы-маркеры (views), а затем каждому полю указывается, в каких представлениях оно должно участвовать.

```java
public class Views {

    // Базовое представление
    public interface Public {}

    // Расширяет Public
    public interface Internal extends Public {}
}
```

DTO:

```java
public class UserDto {

    @JsonView(Views.Public.class)
    private Long id;

    @JsonView(Views.Public.class)
    private String username;

    @JsonView(Views.Internal.class)
    private String email;

    @JsonView(Views.Internal.class)
    private String passwordHash;

    // getters/setters
}
```


***~={pink}Обязательно ли проставлять @JsonView на каждом поле в DTO? Даже если данное поле должно быть видно во всех случаях?=~***
	*Нет, не обязательно, но поведение зависит от настройки `MapperFeature.DEFAULT_VIEW_INCLUSION`:	
	
Если отключить `DEFAULT_VIEW_INCLUSION`, то будут сериализовываться **только поля, явно помеченные `@JsonView`**.
```java
objectMapper.disable(MapperFeature.DEFAULT_VIEW_INCLUSION);
```



---

## Использование в Spring Controller

### Разные эндпоинты — разные представления

```java
@RestController
@RequestMapping("/users")
public class UserController {

    @GetMapping("/{id}")
    @JsonView(Views.Public.class)
    public UserDto getUser() {
        return userService.getUser();
    }

    @GetMapping("/{id}/internal")
    @JsonView(Views.Internal.class)
    public UserDto getInternalUser() {
        return userService.getUser();
    }
}
```

Если вернуть:

```java
{
    "id": 1,
    "username": "john",
    "email": "john@example.com",
    "passwordHash": "secret"
}
```

то:

### `/users/1`

Вернёт:

```json
{
  "id": 1,
  "username": "john"
}
```

### `/users/1/internal`

Вернёт:

```json
{
  "id": 1,
  "username": "john",
  "email": "john@example.com",
  "passwordHash": "secret"
}
```

---

## Иерархия View

Views могут наследоваться друг от друга.

```java
public class Views {

    public interface Summary {}

    public interface Details extends Summary {}

    public interface Admin extends Details {}
}
```

```java
public class ProductDto {

    @JsonView(Views.Summary.class)
    private Long id;

    @JsonView(Views.Summary.class)
    private String name;

    @JsonView(Views.Details.class)
    private String description;

    @JsonView(Views.Admin.class)
    private BigDecimal purchasePrice;
}
```

При использовании `Admin` будут включены все поля:

- `id`
    
- `name`
    
- `description`
    
- `purchasePrice`
    

---

## Десериализация (Request Body)

`@JsonView` работает не только на ответах, но и на запросах.

```java
@PostMapping
@JsonView(Views.Public.class)
public UserDto create(
        @RequestBody
        @JsonView(Views.Public.class)
        UserDto dto) {
    ...
}
```

DTO:

```java
public class UserDto {

    @JsonView(Views.Public.class)
    private String username;

    @JsonView(Views.Internal.class)
    private boolean admin;
}
```

Если запрос:

```json
{
  "username": "john",
  "admin": true
}
```

то поле `admin` будет проигнорировано.

---

## Программное использование без Spring

```java
ObjectMapper mapper = new ObjectMapper();

String json = mapper
        .writerWithView(Views.Public.class)
        .writeValueAsString(user);
```

Для десериализации:

```java
UserDto user = mapper
        .readerWithView(Views.Public.class)
        .forType(UserDto.class)
        .readValue(json);
```

---

## Поведение полей без `@JsonView`

По умолчанию Jackson включает **все поля, у которых нет `@JsonView`**.

Например:

```java
public class UserDto {

    @JsonView(Views.Public.class)
    private Long id;

    private String secret;
}
```

При сериализации с `Public` будут выведены:

```json
{
  "id": 1,
  "secret": "..."
}
```

Если такое поведение нежелательно, можно отключить его:

```java
mapper.disable(
    MapperFeature.DEFAULT_VIEW_INCLUSION
);
```

В Spring Boot:

```java
@Bean
public Jackson2ObjectMapperBuilderCustomizer jsonCustomizer() {
    return builder ->
        builder.featuresToDisable(
            MapperFeature.DEFAULT_VIEW_INCLUSION
        );
}
```

После этого будут сериализовываться только поля, явно помеченные `@JsonView`.


---
### Установка View динамически (ResponseBodyAdvice)

Допустим, нужно возвратить разные DTO пользователям с разными ролями:

```
Controller
    ↓
ResponseBodyAdvice
    ↓
SmartHttpMessageConverter + serialization hints
    ↓
Jackson
```


1. Создадим собственную аннотацию

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface DynamicJsonView {
}
```

2. Используем её на контроллере

```java
@RestController
@RequestMapping("/users")
public class UserController {

    @GetMapping("/{id}")
    @DynamicJsonView
    public UserDto getUser(@PathVariable String id) {
        return userService.findById(id);
    }
}
```

3. Реализуем `ResponseBodyAdvice`:

```java
@ControllerAdvice
public class DynamicJsonViewAdvice
        extends AbstractMappingJacksonResponseBodyAdvice {

    @Override
    public boolean supports(MethodParameter returnType,
                            Class<? extends HttpMessageConverter<?>> converterType) {

        // Advice применяется только к методам,
        // помеченным нашей аннотацией
        return returnType.hasMethodAnnotation(DynamicJsonView.class);
    }

    @Override
    protected void beforeBodyWriteInternal(
            MappingJacksonValue bodyContainer,
            MediaType contentType,
            MethodParameter returnType,
            ServerHttpRequest request,
            ServerHttpResponse response) {

        Authentication authentication =
                SecurityContextHolder.getContext().getAuthentication();

        boolean isAdmin = authentication != null &&
                authentication.getAuthorities()
                        .stream()
                        .map(GrantedAuthority::getAuthority)
                        .anyMatch("ROLE_ADMIN"::equals);

        if (isAdmin) {
            bodyContainer.setSerializationView(Views.Admin.class);
        } else {
            bodyContainer.setSerializationView(Views.Public.class);
        }
    }
}
```


---

## Когда `@JsonView` полезен

- Публичное и внутреннее API.
    
- Краткое и подробное представление сущности.
    
- Разные уровни доступа (user/admin).
    
- Разделение полей для чтения и записи.
    
- Частичное скрытие чувствительных данных.
    

---

## Когда лучше использовать разные DTO

Хотя `@JsonView` удобен, многие команды предпочитают отдельные DTO:

```text
UserResponseDto
UserDetailsResponseDto
AdminUserResponseDto
UserCreateRequestDto
```

потому что:

- DTO становятся явно документированными.
    
- Меньше скрытой магии.
    
- Проще поддерживать и тестировать.
    
- Лучше интегрируется с OpenAPI/Swagger.
    

Обычно `@JsonView` хорошо подходит для небольшого количества вариантов представления. Если вариантов много или API сложное, отдельные DTO чаще оказываются более поддерживаемым решением.

### Типичный пример

```java
public class UserViews {
    public interface Summary {}
    public interface Details extends Summary {}
}

public class UserDto {

    @JsonView(UserViews.Summary.class)
    private Long id;

    @JsonView(UserViews.Summary.class)
    private String username;

    @JsonView(UserViews.Details.class)
    private String email;
}
```

```java
@GetMapping("/users")
@JsonView(UserViews.Summary.class)
public List<UserDto> findAll() { ... }

@GetMapping("/users/{id}")
@JsonView(UserViews.Details.class)
public UserDto findById() { ... }
```

Это позволяет использовать один DTO для нескольких представлений ответа.


----
#### [[@JsonView - Jackson - Flashcards|Link to flashcards]]



---
### References:

