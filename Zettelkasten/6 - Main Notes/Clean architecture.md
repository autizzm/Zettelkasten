
08-02-2026 11:40

Status:

Tags: [[Patterns]]

---
# Clean architecture


![[Pasted image 20260208114051.png]]

- **Объекты**: это самый стабильный код приложения, который не должен подвергаться внешним изменениям. Объектами могут быть методы и структуры данных.

- **Сценарии использования**: здесь происходит инкапсуляция и внедряется бизнес-логика.

- **Адаптеры интерфейса**: здесь располагаются сущности, происходит преобразование и представление данных в сценарии использования.

- **Фреймворки и драйверы**: эта область содержит инструменты и фреймворки для запуска приложения.



#### Границы уровней

> [!note]
> Модули не обязательны! Это просто такоей пример. Все нормальные люди делают без них.
> 
> Главный принцип - разделение по зонам ответственности:
> ![[Pasted image 20260208145703.png]]
> domain ~ entities + their exceptions

Для обеспечения границ пакеты могут использовать модули. Они позволяют не раскрывать детали реализации. Например для пакета usecase нет смысла раскрывать Application Services, поскольку они используются исключительно внутри этого пакета.

```java
module slalom.example.domain {
	exports com.slalom.example.domain.entity;
	exports com.slalom.example.domain.port;
	exports com.slalom.example.domain.exception;
}

module slalom.example.usecase {
	exports com.slalom.example.usecase;
	requires slalom.example.domain;
	requires org.apache.commons.lang3;
}
```

Ещё раз о роли внутренних уровней:

- Внутренние уровни содержат объекты и бизнес-логику. Это самая стабильная часть приложения.
- Любое взаимодействие с внешним миром (например, с базой данных или внешним сервисом) не реализовывается во внутренних уровнях.
- Не используются фреймворки и минимальные зависимости.
- Модули  скрывают детали реализации.



---
### Внутренние уровни (Application Core)

Наши объекты и сценарии разделены на два проекта: «domain» и «usecase».

```
clean-app/
├── pom.xml (root aggregator)
│
├── domain/                    # Чистый домен (независимый jar)
│   ├── pom.xml
│   ├── src/main/java/
│   │   ├── com.example.domain/
│   │   │   ├── entity/          # Account.java, User.java
│   │   │   ├── service/         # UserTransferService.java, UserConstraints.java
│   │   │   ├── port/
│   │   │   │   └── out/         # AccountRepository.java (интерфейсы)
│   │   │   └── valueobject/     # Money.java
│   │   └── src/test/java/       # Unit тесты домена
│
├── application/                # Use Cases (зависит от domain)
│   ├── pom.xml
│   ├── src/main/java/
│   │   └── com.example.application/
│   │       ├── usecase/         # TransferMoneyUseCase.java
│   │       ├── dto/             # TransferRequest.java (input/output)
│   │       └── port/
│   │           └── in/          # MoneyController.java
│   │   
│   └── src/test/java/
│
├── infrastructure/             # Адаптеры, фреймворки (зависит от domain+application)
│   ├── pom.xml
│   ├── src/main/java/
│   │   └── com.example.infrastructure/
│   │       ├── adapter/out/     # JpaAccountRepository.java (implementations)
│   │       ├── persistence/     # JPA config, entities @Entity
│   │       └── web/             # REST контроллеры (Spring @RestController)
│   └── src/test/java/
│
└── presentation/ или web/      # UI/CLI/API (опционально, зависит от application)
    ├── pom.xml
    └── src/main/java/
        └── com.example.web/
            └── controller/       # @RestController, вызывает UseCase
```

> [!warning]
> `port.in` (интерфейсы контроллеров) - принадлежит к application layer, поскольку использует DTO, которые принадлежат Application layer.




![[Pasted image 20260208172904.png]]

#### **Domain Layer:**
- **entities**
- **Enums**
- **Value Objects** - неизменяемые (immutable) объекты, представляющие концепции из предметной области без уникального идентификатора (Money - поля: amount, currency).
- **entities' exceptions**
- **Domain Events** (~={red}not the same as Application Events=~) - **When an entity changes, a Domain Event is triggered** and it carries the changed properties new values. These events are perfect, for example, to be used in [[Event Sourcing - System Design Patterns|Event Sourcing]].
- **port** - контракты для работы с этими entites. Port - спецификация того, как Driving Adapters будут использовать Application Core или как Application Core будет использовать Driven Adapters.

#### **Domain Services:**
- Включает в себя логику работы с entities (одного или разных типов), которая вроде не принадлежит самим entities (не их responsibility). Не выносим в App. Services, чтобы была возможность переиспользовать эту логику в разных Use Cases. 
  **Пример:**
```java
UserConstraints.validateEmailUnique(email); // Бизнес-правило
```
Тут `UserConstraints` относится к Domain Services, т.к. добавляет доп логику, определённую бизнес правилом (entities так-то тоже субъекты бизнес правил), а не бизнес логику
	
  Domain Services могут использовать:
	- **Domain Model objects (entites)**
	- **другие Domain Services**


#### **Use Cases: (App. Services / application)**
- **business logic** 
	- **Services** (как в Spring)    могут существовать и без CommandHandlers
	- **Command Handlers** (тут это Use Case классы)
	 **NB!** ~={cyan}CommandHandlers могут существовать и вместе с Services. Есть два варианта их использования:=~
	1. They can contain the actual logic to perform the use case; (тогда существуют только CommandHandlers)
	2. They can be used as mere wiring pieces in our architecture, receiving a Command and simply triggering logic that exists in an Application Service.
	
- **классы-помощники бизнес логики** (тут - validator, синтаксическая (формальная) валидация).
- ~={cyan}иногда **ports** относят к App. Sevices=~.

App. Services также может триггерить Application Events, которые представляют собой результат use case-а. Эти events вызывают вызывают логику (side effect of the use case) like sending emails, notifying a 3rd party API, sending a push notification, or even starting another use case that belongs to a different component of the application.

> [!warning]
> Крайне важно, чтобы ports были созданы для удовлетворения потребностей Application Core, а не просто имитировали инструменты API.



В чистой архитектуре достаточно взглянуть на пакет **usecase**, чтобы увидеть список поддерживаемых операций.

Пример реализации usecase:

```java
package com.slalom.example.usecase;

import com.slalom.example.domain.entity.User;
import com.slalom.example.domain.port.UserRepository;
import java.util.List;
import java.util.Optional;

public final class FindUser {

	private final UserRepository repository;

	public Optional<User> findById(final String id) {
		return repository.findById(id);
	}

	public List<User> findAllUsers() {
		return repository.findAllUsers();
	}
}
```

У нас есть две операции, которые извлекают пользователей из хранилища, что стандартно для сервис-ориентированной архитектуры.

**UserRepository** − ~={cyan}это интерфейс, который не реализован в текущем проекте. Это деталь нашей архитектуры, а детали реализовываются на внешних уровнях.=~ 
Мы реализуем **UserRepository** при создании сценария использования (например, с помощью внедрения зависимости). Это даёт нам следующие преимущества:

- Бизнес-логика не изменяется в зависимости от реализации.

- Любое изменение в реализации не затрагивает бизнес-логику.

- Очень легко вносить серьезные изменения в реализацию.



---
### Внешние уровни

Adapter package объединяет Driving Adapters (Код взаимодействия с тем, через что пользователи используют Application Core (Внутренние уровни)) и Driven Adapters (Код взаимодействия с тем, что используется нашим приложением).

![[Pasted image 20260208151133.png]]


![[Pasted image 20260208115717.png]]

Репозитории и адаптеры хранятся в отдельных папках и являются реализациями интерфейсов:

```java
package com.slalom.example.jug;

import com.fasterxml.uuid.EthernetAddress;
import com.fasterxml.uuid.Generators;
import com.fasterxml.uuid.NoArgGenerator;
import com.slalom.example.usecase.port.IdGenerator;

public class JugIdGenerator implements IdGenerator {

	@Override
	public String generate() {
		return generator().generate().toString().replaceAll("-", "");
	}

	private static NoArgGenerator generator() {
		return Generators.timeBasedGenerator(EthernetAddress.fromInterface());
	}
}
```


### Пример (с ипользованим [[CQRS - System Design Patterns|CQRS]])

Если используем разделение Commands and Queries, то в Application Services выносим общую логику каких-то команд, Command-specific business logic shoukd stay inside the Command itself.

Пример такой системы:

![[Pasted image 20260208173500.png]]


> [!example] Note
> В CQRS Command обычно не возвращает данные о состоянии в виде полного DTO (чисто статус), но это зависит от контекста и требований. Поэтому тут DTO для Command и не изображён, но в целом, он может присутствовать


> [!note] 
> Тут отдельные интерфейсы:
> -  Persistence - даёт возможность легко сменить ORM framework
> -  Repository - abstraction on the persistence engine itself. Let’s say we want to switch from MySQL to MongoDB. Repository interface stays the same, but the implementation changes (we no longer use the ORM).

CommandQueryBus - то, что по мере принятия запросов в Controller, создаёт Queries и Commands, передавая их в Application Core.

- Про Domain Event - when an entity changes, a Domain Event is triggered and it carries the changed properties new values. These events are perfect, for example, to be used in [[Event Sourcing - System Design Patterns|Event Sourcing]].

----
#### [[Clean architecture - Flashcards|Link to flashcards]]



---
### References:

https://herbertograca.com/2017/11/16/explicit-architecture-01-ddd-hexagonal-onion-clean-cqrs-how-i-put-it-all-together/