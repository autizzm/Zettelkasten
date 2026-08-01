
10-07-2026 15:41

Status:

Tags: [[Kubernetes]]

---
# Cluster manifest - Kubernetes



### Наиболее распространённый манифест

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4

name: demo

networking:
  apiServerAddress: "127.0.0.1"
  apiServerPort: 6443
  podSubnet: "10.244.0.0/16"
  serviceSubnet: "10.96.0.0/12"
  disableDefaultCNI: false

nodes:
  - role: control-plane

    image: kindest/node:v1.33.1

    labels:
      ingress-ready: "true"

    extraPortMappings:
      - containerPort: 80
        hostPort: 80
        protocol: TCP

      - containerPort: 443
        hostPort: 443
        protocol: TCP

    extraMounts:
      - hostPath: ./data
        containerPath: /mnt/data

  - role: worker

  - role: worker
```

---

# Основные properties

### 1. networking

Настройка сети.

```yaml
networking:
  apiServerAddress: "127.0.0.1"
  apiServerPort: 6443
```

Указывает адрес и порт Kubernetes API.

Обычно менять не нужно.

---

#### podSubnet

Диапазон IP для Pod.

```yaml
podSubnet: 10.244.0.0/16
```

Используется CNI.

Например:

- Calico
    
- Cilium
    
- Flannel
    

---

#### serviceSubnet

Диапазон IP для Service.

```yaml
serviceSubnet: 10.96.0.0/12
```

Практически никогда не меняется.

---

#### disableDefaultCNI

```yaml
disableDefaultCNI: true
```

Если поставить:

```yaml
true
```

Kind **не установит стандартную сеть**.

Это нужно, если вы хотите самостоятельно установить:

- Calico
    
- Cilium
    
- Weave
    
- Flannel
    

---

### 2. nodes

Самая важная часть.

```yaml
nodes:
```

Например

```yaml
nodes:
- role: control-plane
- role: worker
- role: worker
```

Получим

```
Master

Worker1

Worker2
```

---

## role

Может быть

```yaml
role: control-plane
```

или

```yaml
role: worker
```

---

#### image

Можно выбрать версию Kubernetes.

```yaml
image: kindest/node:v1.33.1
```

Например

```
1.31

1.32

1.33
```

Очень удобно тестировать разные версии Kubernetes.

---

#### labels

Лейблы сразу после создания ноды.

```yaml
labels:
  disk: ssd
  zone: europe
```

Потом можно делать

```yaml
nodeSelector:
```

или

```yaml
nodeAffinity
```

---

#### taints

Можно сразу сделать ноду специализированной.

```yaml
taints:
- key: gpu
  value: "true"
  effect: NoSchedule
```

---

#### extraPortMappings

Самая используемая настройка.

Без неё сервисы внутри Kind недоступны с localhost.

Например

```yaml
extraPortMappings:
- containerPort: 80
  hostPort: 80
```

Получается

```
localhost:80

↓

Docker Container

↓

NodePort

↓

Pod
```

Очень часто используют

```yaml
80 -> 80

443 -> 443
```

для Ingress Controller.

---

#### extraMounts

Проброс директорий.

```yaml
extraMounts:
- hostPath: ./configs
  containerPath: /mnt/configs
```

Можно читать файлы с машины разработчика.

Полезно для:

- сертификатов
    
- данных
    
- логов
    

---

### kubeadmConfigPatches

Очень мощная возможность.

Можно изменить конфигурацию kubeadm.

Например

```yaml
kubeadmConfigPatches:
- |
  kind: ClusterConfiguration
  apiServer:
    extraArgs:
      authorization-mode: Node,RBAC
```

Или

```yaml
controllerManager:
```

Или

```yaml
scheduler:
```

Можно почти полностью изменить настройки Kubernetes.

---

### kubeadmConfigPatchesJSON6902

То же самое, но в формате JSON Patch.

Используется редко.

---

### featureGates

Можно включать экспериментальные функции Kubernetes.

```yaml
kubeadmConfigPatches:
- |
  kind: ClusterConfiguration
  apiServer:
    extraArgs:
      feature-gates: SomeFeature=true
```

---

### containerdConfigPatches

Позволяет менять конфигурацию containerd.

Например

```yaml
containerdConfigPatches:
- |
  [plugins."io.containerd.grpc.v1.cri".registry]
```

Используется для:

- локальных registry;
    
- insecure registry;
    
- mirror registry.
    

---

### runtimeConfig

Можно включать дополнительные API.

Например

```yaml
runtimeConfig:
  api/all: "true"
```

Используется редко.

---

# Пример для локальной разработки

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4

networking:
  apiServerAddress: "127.0.0.1"
  apiServerPort: 6443

nodes:
  - role: control-plane

    image: kindest/node:v1.33.1

    labels:
      ingress-ready: "true"

    extraPortMappings:
      - containerPort: 80
        hostPort: 80

      - containerPort: 443
        hostPort: 443

    extraMounts:
      - hostPath: ./volumes
        containerPath: /mnt/volumes

  - role: worker
  - role: worker
```



----
#### [[Cluster manifest - Kubernetes - Flashcards|Link to flashcards]]



---
### References:

