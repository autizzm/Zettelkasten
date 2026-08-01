
07-07-2026 19:27

Status:

Tags: [[Spring-TX]] [[Spring Core]] [[Spring]]

---
# TransactionalEventListener - Spring-TX


`@TransactionalEventListener` — аннотация Spring, позволяющая обрабатывать события **в привязке к жизненному циклу транзакции**, а не сразу после публикации события.

**Пакет:**

```java
org.springframework.transaction.event.TransactionalEventListener
```


---
### Когда использовать

Если обработчик должен выполниться:

- только после успешного коммита транзакции (например, отправка Kafka-сообщения, email);
    
- при откате транзакции;
    
- перед коммитом;
    
- после завершения транзакции независимо от результата.
    

Пример:

```java
@TransactionalEventListener
public void handle(OrderCreatedEvent event) {
    // Выполнится только после успешного commit
}
```


---

## Основные properties

### `phase`

Определяет, **на каком этапе транзакции** вызвать обработчик.

По умолчанию:

```java
phase = TransactionPhase.AFTER_COMMIT
```

Возможные значения:

| Значение           | Когда вызывается                                   |
| ------------------ | -------------------------------------------------- |
| `BEFORE_COMMIT`    | Перед commit транзакции                            |
| `AFTER_COMMIT`     | После успешного commit (**по умолчанию**)          |
| `AFTER_ROLLBACK`   | После rollback                                     |
| `AFTER_COMPLETION` | После завершения транзакции (и commit, и rollback) |

> [!warning] 
> При использовании `@TransactionalEventListener`  с `BEFORE_COMMIT` добавлять `@Transactional` не имеет смысла. Этот метод и так будет выполнен до коммита. 
>
>Так что это бред:
>```java
>    @TransactionalEventListener(phase = TransactionPhase.BEFORE_COMMIT)
>    @Transactional(propagation = Propagation.REQUIRES_NEW)
>    public void on(OrderCreatedEvent event) throws 
>```
> 


Когда `@Transactional` вообще полезна на `@TransactionalEventListener`?

|Phase|Нужна `@Transactional`?|Причина|
|---|---|---|
|`BEFORE_COMMIT`|Обычно **нет**|Обработчик уже выполняется внутри текущей транзакции.|
|`AFTER_COMMIT`|**Да**, если нужно работать с БД|Исходная транзакция уже завершилась, для записи нужна новая.|
|`AFTER_ROLLBACK`|Аналогично|Если нужно сохранить информацию после отката, требуется новая транзакция.|
|`AFTER_COMPLETION`|Аналогично|Исходная транзакция уже закончилась.|


---

### `fallbackExecution`

По умолчанию:

```java
fallbackExecution = false
```

- `false` — если транзакции нет, обработчик **не будет вызван**.
    
- `true` — если транзакции нет, событие обработается сразу, как обычный `@EventListener`.
    

Пример:

```java
@TransactionalEventListener(fallbackExecution = true)
```

---

### `classes` / `value`

Позволяет указать типы событий, если метод не принимает параметр события.

Пример:

```java
@TransactionalEventListener(classes = OrderCreatedEvent.class)
public void handle() {
    ...
}
```

Обычно не используется — чаще событие передают параметром метода.

---

### `condition`

SpEL-выражение для фильтрации событий.

Пример:

```java
@TransactionalEventListener(condition = "#event.amount > 1000")
public void handle(OrderCreatedEvent event) {
}
```

---

### `id`

Необязательный идентификатор слушателя. Используется редко, например для диагностики или управления слушателями.

```java
@TransactionalEventListener(id = "orderListener")
```

---

## Отличие от `@EventListener`

|`@EventListener`|`@TransactionalEventListener`|
|---|---|
|Вызывается сразу после `publishEvent()`|Вызывается в выбранной фазе транзакции|
|Не зависит от транзакции|Зависит от транзакции|
|Может выполниться до commit|Обычно выполняется после успешного commit (`AFTER_COMMIT`)|

### Когда выбирать

- **`@EventListener`** — если обработка не должна зависеть от транзакции.
    
- **`@TransactionalEventListener`** — если обработчик должен выполняться только после успешного сохранения данных (или в другой конкретной фазе транзакции). Это типичный выбор для публикации сообщений в Kafka, отправки email, обновления внешних систем и других действий, которые не должны происходить при откате транзакции.


----
#### [[TransactionalEventListener - Spring-TX - Flashcards|Link to flashcards]]



---
### References:

