
05-02-2026 10:52

Status:

Tags: [[Kubernetes]] [[CI & CD]]

---
# Ingress Service - Kubernetes

Ingress - smart entry-point to your cluster.

![[Pasted image 20260209082324.png]]



![[Pasted image 20260707085101.png]]


Существует множество реализаций Ingress, каждая предоставляет свои возможности:

- nginx
- Kong
- istio
- ...

Также существует множество плагинов для Ingress. Например Cert, который автоматически обновляет TLS certificate.

### Чем Ingress отличается от [[LoadBalancer Service & its manifest - Kubernetes|LoadBalancer]] 


Ingress и LoadBalancer Service — разные способы внешнего доступа в Kubernetes: LoadBalancer создаёт облачный балансировщик нагрузки для одного сервиса, Ingress управляет HTTP-трафиком к нескольким сервисам через правила маршрутизации.

| Аспект                 | LoadBalancer Service           | Ingress                             |
| ---------------------- | ------------------------------ | ----------------------------------- |
| **Тип**                | `type: LoadBalancer` в Service | Ресурс Ingress + Ingress Controller |
| **Протокол**           | L4 (TCP/UDP, любой трафик)     | L7 (HTTP/HTTPS, host/path-based)    |
| **IP-адреса**          | Один публичный IP на сервис    | Один IP для множества сервисов      |
| **Стоимость**          | Дорогой (LB за каждый сервис)  | Дешевле (один Controller LB)        |
| **Функции**            | Базовая балансировка           | TLS, rewrite, rate-limit, auth      |
| **Когда использовать** | Простые TCP/UDP сервисы        | Веб-приложения, микросервисы ​      |

LoadBalancer подходит для отдельных сервисов (например, базы данных, которая должна быть доступна извне), Ingress — для веб-архитектур с маршрутизацией по домену/пути (`example.com/api → service-a`).


----
#### [[Ingress Service - Kubernetes - Flashcards|Link to flashcards]]



---
### References:

