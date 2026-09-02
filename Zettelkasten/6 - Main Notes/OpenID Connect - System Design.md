
04-04-2026 12:01

Status:

Tags: [[System Design]] [[Keycloak]]

---
# OpenID Connect - System Design

Надстройка над [[OAuth 2.0 - System Design|OAuth 2.0]], которая добавляет авторизацию пользователя.

OpenID Connect расширяет OAuth, чтобы приложение могло не только получить доступ к ресурсам, но и узнать, кто такой пользователь.

Добавляет понятие **ID Token** - это отдельный токен (в формате JWT), который содержит информацию о пользователе (имя, email и т.д.).

Пример:
- Приложение хочет узнать, кто вошел в систему через Google, и получить его email.


### ID token

Содержит информацию о самом пользователе.

В Keycloak существует endpoint, который может предоставить данные по пользователю 


----
#### [[OpenID Connect - System Design - Flashcards|Link to flashcards]]



---
### References:

- [[OIDC для межсервисного взаимодействия - System Design]]
