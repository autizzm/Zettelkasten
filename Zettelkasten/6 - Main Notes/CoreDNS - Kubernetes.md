
05-02-2026 10:11

Status:

Tags: [[Kubernetes]] [[CI & CD]]

---
# CoreDNS - Kubernetes

CoreDNS — это DNS-сервер по умолчанию в Kubernetes (заменил kube-dns), развернутый как Deployment в namespace `kube-system`. Он использует плагин `kubernetes` для разрешения имен сервисов и Pod'ов в кластере

Резолвит имена сервисов в их Ip.

```yaml
...
name: example-service # резолвит имя сервиса в internal service ip
...
```




Когда сервис создаётся, он загружается в IPTable, где его имя и ip мапятся with IPs of Pods which it manages. 

???? И при использовании LoadBalancer service используется специальный алгоритм для определения, когда IP какого Pod-а выдавать. ????




CoreDNS обрабатывает запросы от Pod'ов (через ClusterIP, обычно `10.96.0.10`) по зонам:

- **Service**: `my-svc.my-namespace.svc.cluster.local` → ClusterIP сервиса (виртуальный IP для балансировки трафика к Endpoint'ам/Pod'ам).
    
- **Pod**: `my-pod.my-namespace.pod.cluster.local` → IP Pod'а (если `pods verified` или `insecure` в Corefile).
    
- Логика: Плагин `kubernetes` читает API Kubernetes (Endpoints, Services), кэширует ответы; внешние домены форвардит в upstream (например, `/etc/resolv.conf` ноды).


### IP Pod'а при Service типа LoadBalancer

LoadBalancer не меняет резолвинг **внутри кластера**:

- Запрос `my-svc.my-ns.svc.cluster.local` всегда возвращает **ClusterIP** сервиса (стабильный, независимо от типа).
    
- CoreDNS смотрит на `spec.clusterIP` сервиса, затем Endpoint'ы (IP Pod'ов с matching selector). Правила: `ports verified`, `pods insecure` (разрешает резолвинг Pod IP без строгой проверки портов).
    
- **Внешне** (через LB IP): kube-proxy/ingress балансирует на ноды/Pod'ы, но CoreDNS этим не занимается — резолвинг LB IP происходит внешними DNS или провайдером (AWS ELB, GCP, etc.).


### Путь запроса: Ingress → Service → Pod

1. **Клиент → Ingress**: Запрос попадает на Ingress Controller (NGINX/Envoy Pod) по домену/пути (например, `app.com/api` → backend Service).
    
2. **Ingress → Service**: Controller использует правила `spec.rules` для выбора Service (по имени/порту), отправляя трафик на ClusterIP Service.​
    
3. **Service → Endpoint**: kube-proxy (iptables/IPVS) смотрит селектор Service, выбирает Endpoint (IP:port Pod'а с matching labels) из `Endpoints` объекта.​
    
4. **Endpoint → Pod**: Трафик маршрутизируется по CNI (Calico/Flannel) на ноду с Pod'ом; внутри ноды — на containerPort контейнера.​
    
5. **Pod**: Контейнер (app) обрабатывает запрос и отвечает по обратному пути.

----
#### [[CoreDNS - Kubernetes - Flashcards|Link to flashcards]]



---
### References:

