
04-02-2026 14:50

Status:

Tags: [[Kubernetes]] [[CI & CD]]

---
# Компоненты - Kubernetes


### Pod

Это абстракция над контейнером

- Имеет свой IP
- Минимальный элемент Kubernetes

![[Pasted image 20260204145549.png|281]]

В теории может содержать и больше одного контейнера.

IP адрес Pod-а может меняться. Например, если снесли весь Pod и перезапустили - ему присвоится новый IP.


---
### Service

Задача сервиса - обеспечить постоянный доступ к Pod-у.

![[Pasted image 20260204145524.png|308]]

Имеет постоянный IP.

Бывает 4 типа:
- ClusterIp - пробрасывает порт Pod-а внутри кластера -> виден только внутри кластера -> нужно использовать `kubectl port-forwarding` команду, чтобы получить к ним доступ извне.
- [[NodePort Service & its manifest- Kubernetes|NodePort]] - позволяет прокинуть порт из контейнера наружу (don't use in production)
- [[LoadBalancer Service & its manifest - Kubernetes|LoadBalancer]]
- Ingress

> [!note]
> Чтобы ClusterIp стал виден вне Kubernetes cluster нужно:
> -  вручную пробросить порт `kubectl port-forward`:
> ```bash
> kubectl port-forward service/prometheus-operated -n monitoring 9090:9090
> ```

---
### Node 

Некая виртуальная машина, сервер, который выполняет одну из двух ролей:
- Node (Worker)
- Master Node


![[Pasted image 20260204153305.png]]

- **API Server** - API для подключения с помощью kubectl (CLI tool).
- **Auth** - аутентификация.
- **etcd (KV store)** - хранит информацию о нодах, о том, как распределены нагрузки. (аналог БД ключ-значение), **~={orange}secrets, properties=~**
- **Controller Manager** - процесс внутри master Node, который мониторит нагрузку на ноды и соблюдение текущей конфигурации (под упал -> надо поднять, нода упала -> надо перераспределить поды). Берёт данные из etcd (KV store)
- **Scheduler** - планирует размещение Pod-ов. С помощью Controller Manager определяет уровень нагрузки на каждую ноду и исходя из этого выбирает ноду для развертывания нового образа. 

- **Kubelet** - процесс на обычной Worker Node, который получает информацию (образ и когда его установить) от Scheduler и выполняет действия на самой Node.

##### Процесс запуска Pod-а

1. `kubectl` передаёт container-image + манифест Scheduler-у  - "надо его запустить"
2. **Control Manager** теперь знает, что должен быть запущен этот Pod (видит из **etsd**, что должен быть 1, а запущено 0), ищет Node, где можно запустить -> передаёт Scheduler-у, что надо запустить Pod.
3. **Scheduler** - планирует запуск Pod-а. Передаёт kubelet-у на нужной ноде, что нужно запустить Pod.
4. **Kubelet** - получает image (из локального docker registry), запускает Pod.
- **etcd** - постоянно мониторит состояние 

##### Этапы создания Pod-а

1. Scheduled
2. Pull
3. Create
4. Start

----
#### [[Компоненты - Kubernetes - Flashcards|Link to flashcards]]



---
### References:

