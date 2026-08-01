
07-07-2026 08:53

Status:

Tags: [[Kubernetes]] [[CI & CD]]

---
# Service - Kubernetes


![[Pasted image 20260707085517.png]]


**Service — это декларативная абстракция, а kube-proxy — один из компонентов, который реализует ее поведение**.

![[Pasted image 20260707085548.png]]

Однако Service — это **не абстракция именно над kube-proxy**, а абстракция над механизмом сетевой доступности Pod'ов. В зависимости от сетевого стека эту абстракцию могут реализовывать разные компоненты.





### В классическом Kubernetes

Когда ты создаешь Service:

```
apiVersion: v1
kind: Service
spec:
  selector:
    app: payment
  ports:
  - port: 80
    targetPort: 8080
```

происходит следующее:

1. API Server сохраняет объект Service.
2. Контроллер создает соответствующий объект EndpointSlice с IP всех подходящих Pod'ов.
3. **kube-proxy на каждой Node** наблюдает за изменениями Service и EndpointSlice.
4. kube-proxy настраивает правила iptables/IPVS (или другой механизм), чтобы трафик на виртуальный IP Service перенаправлялся на один из Pod'ов.

Схематично:

```
Service
    |
EndpointSlice
    |
kube-proxy (на каждой Node)
    |
iptables / IPVS
    |
Pods
```


----
#### [[Service - Kubernetes - Flashcards|Link to flashcards]]



---
### References:

