
01-07-2026 13:10

Status:

Tags: [[Spring Data MongoDB]] [[Spring]]


---
# Repository Fragment (кастомные методы) - Spring Data MongoDB

Это **самый популярный подход для сложной фильтрации в крупных проектах**.

Репозиторий с нашим кастомным методом фильтрации:

```java
public interface UserRepositoryCustom {

    List<User> findAll(UserFilter filter);

}
```

Реализация:

```java
class UserRepositoryImpl
        implements UserRepositoryCustom {

    private final MongoTemplate mongoTemplate;

    @Override
    public List<User> findAll(UserFilter filter) {

        Query query = ...

        return mongoTemplate.find(query, User.class);
    }
}
```

> [!note]
> Если `UserRepositoryImpl` является **реализацией Repository Fragment**, то Spring Data найдет и подключит ее автоматически по соглашению об именовании, и аннотация `@Repository` не требуется.

> [!warning]
> `UserRepositoryImpl` должна находиться в том же пакете или ниже, что и `UserRepositoryCustom`:
> ```
>domain
>└── port
>    PaymentRepository
>
>infrastructure
>└── repository
>    SpringPaymentRepository
>    PaymentFilteringRepository
>    PaymentFilteringRepositoryImpl
>    PaymentAggregationRepository
>    PaymentAggregationRepositoryImpl
> ```


Основной репозиторий:

```java
public interface UserRepository extends
        MongoRepository<User, String>,
        UserRepositoryCustom {
}
```


Снаружи выглядит как обычный репозиторий:

```java
public interface UserRepository extends
        MongoRepository<User, String>,
        UserRepositoryCustom {
}
```

----
#### [[Repository Fragment (кастомные методы) - Spring Data MongoDB - Flashcards|Link to flashcards]]



---
### References:

- [[Criteria + Query - динамическая фильтрация - Spring Data MongoDB]]