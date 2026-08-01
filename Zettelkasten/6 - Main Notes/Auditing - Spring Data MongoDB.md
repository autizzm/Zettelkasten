
29-06-2026 19:23

Status:

Tags: [[Spring Data MongoDB]] [[Spring Data Access]] [[Spring]]

---
# Auditing - Spring Data MongoDB


### Вариант 1. Наследоваться от `AbstractMongoEventListener`

```java
@Component
public class UserMongoListener extends AbstractMongoEventListener<User> {

    @Override
    public void onBeforeConvert(BeforeConvertEvent<User> event) {
        User user = event.getSource();
        // например, заполнить audit-поля
    }

    @Override
    public void onBeforeSave(BeforeSaveEvent<User> event) {
        User user = event.getSource();
        Document document = event.getDocument();
    }

    @Override
    public void onAfterSave(AfterSaveEvent<User> event) {
        // логирование
    }

    @Override
    public void onAfterLoad(AfterLoadEvent<User> event) {
        // документ загружен из MongoDB
    }

    @Override
    public void onAfterConvert(AfterConvertEvent<User> event) {
        // Document -> POJO
    }
}
```

Основные события:

|Событие|Когда вызывается|
|---|---|
|`onBeforeConvert`|До преобразования объекта в `Document`|
|`onBeforeSave`|Перед сохранением в MongoDB|
|`onAfterSave`|После сохранения|
|`onAfterLoad`|После чтения `Document` из MongoDB|
|`onAfterConvert`|После преобразования `Document` в объект|

---

### Вариант 2. Реализовать `EntityCallback` (современный способ)

Начиная с новых версий Spring Data рекомендуется использовать callback API.

```java
@Component
public class UserBeforeConvertCallback
        implements BeforeConvertCallback<User> {

    @Override
    public User onBeforeConvert(
            User entity,
            String collection) {

        entity.setUpdatedAt(Instant.now());
        return entity;
    }
}
```

Другие callback'и:

|Callback|Когда вызывается|Что доступно|Типичный use case|
|---|---|---|---|
|`BeforeConvertCallback<T>`|**Перед преобразованием Entity → BSON Document**|Java-объект (`T`)|Заполнение audit-полей, генерация ID, вычисление derived-полей|
|`BeforeSaveCallback<T>`|**После преобразования Entity → Document, но до записи в MongoDB**|Java-объект (`T`) + BSON `Document`|Модификация BSON перед сохранением, добавление служебных полей|
|`AfterSaveCallback<T>`|**После успешной записи документа в MongoDB**|Java-объект (`T`) + BSON `Document`|Логирование, публикация событий, постобработка|
|`AfterLoadCallback<T>`|**Сразу после чтения BSON из MongoDB, до преобразования в Entity**|BSON `Document`|Изменение сырого BSON, миграция старого формата документов|
|`AfterConvertCallback<T>`|**После преобразования BSON Document → Entity**|Java-объект (`T`) + BSON `Document`|Инициализация transient-полей, вычисление derived-полей после загрузки|

Этот механизм лучше интегрируется с реактивным стеком и считается предпочтительным.

```
Entity
   │
   ▼
BeforeConvertCallback
   │
   ▼
Entity → Document (MappingMongoConverter)
   │
   ▼
BeforeSaveCallback
   │
   ▼
MongoDB save
   │
   ▼
AfterSaveCallback
```

| Callback                | Значение `id` для нового объекта без явно заданного ID                     |
| ----------------------- | -------------------------------------------------------------------------- |
| `BeforeConvertCallback` | Обычно **ещё `null`**                                                      |
| `BeforeSaveCallback`    | Обычно **уже заполнено** (если используется автоматически генерируемый ID) |


---

### Аудит

Если задача — автоматически заполнять поля вроде:

```java
@CreatedDate
private Instant createdAt;

@LastModifiedDate
private Instant updatedAt;
```

то достаточно включить аудит:

```java
@EnableMongoAuditing
```

`@EnableMongoAuditing` включает инфраструктуру аудита в Spring Data MongoDB и регистрирует необходимые компоненты (callbacks/listeners), которые автоматически обрабатывают специальные аннотации вроде `@CreatedDate` и `@LastModifiedDate`.

---

### Итог

|Механизм|Spring Data JPA|Spring Data MongoDB|
|---|---|---|
|`@EntityListeners`|✅|❌|
|`@PrePersist`, `@PostLoad`|✅|❌|
|`AbstractMongoEventListener`|❌|✅|
|`EntityCallback`|❌|✅|
|`@CreatedDate`, `@LastModifiedDate`|✅|✅|

То есть если вы переходите с JPA на MongoDB, то вместо:

```java
@EntityListeners(MyListener.class)
public class User { ... }
```

нужно использовать либо `AbstractMongoEventListener`, либо (предпочтительно) `EntityCallback`.


----
#### [[Auditing - Spring Data MongoDB - Flashcards|Link to flashcards]]



---
### References:

