
07-07-2026 08:26

Status:

Tags: [[Kubernetes]] [[CI & CD]]

---
# Типы манифестов - Kubernetes



![[Pasted image 20260707082715.png]]


**Deployment** - включает в себя **ReplicaSet**, который в свою очередь включает в себя несколько **Pod**-ов.

На уровне **ReplicaSet** - самовосстановление, масштабируемость, желаемое состояние пода.

На уровне **Deployment** - обновление и откат

**Deployment** - не предназначен для хранения состояния.

---
### StatefulSet

![[Pasted image 20260707091609.png]]

Это специальная абстракция для хранения состояния.


---
### DaemonSet

Специальный сет для поддерживания фоновых задач

![[Pasted image 20260707091801.png]]

Здесь fluentD - DaemonSet - используется для сбора логов.

```yaml
apiVersion: apps/vq
kind: DaemonSet
metadata:
	name: fluentd
	labels:
		app: fluentd
spec:
	selector:
		matchLabels:
			app: fluentd
		
```


----
#### [[Типы манифестов - Kubernetes - Flashcards|Link to flashcards]]



---
### References:

