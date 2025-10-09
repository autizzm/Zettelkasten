
09-10-2025 20:55

Status: #baby 

Tags: [[Patterns]]

---
# Factory method - pattern

Static метод, который содержит в себе код создания объекта класса

> [!note] **Factory method обычно создаёт не себя, а объекты других классов**


![[Pasted image 20251009210037.png]]

1) **Product** - общий интерфейс для классов, возвращаемых фабричным методом
	
2) **Concrete Products** - реализации **Product**
	
3) **Creator** - интерфейс, определяющий сигнатуру фабричного метода. Этот метод возвращает созданный объект типа **Product** (родительский интерфейс)
	
4) **Concrete Creators** - классы, которые реализуют **Creator** -> имеют фабричный метод нужной сигнатуры (возвращает созданный объект типа **Product**)

---

## Пример реализации

```java
interface Transport {
    void deliver();
}

class Truck implements Transport {
    public void deliver() {
        System.out.println("Deliver by land in a box");
    }
}

class Ship implements Transport {
    public void deliver() {
        System.out.println("Deliver by sea in a container");
    }
}



abstract class Logistics {
    public void planDelivery() {
        Transport t = createTransport(); // factory method
        t.deliver();
    }

    protected abstract Transport createTransport();
}

class RoadLogistics extends Logistics {
    @Override
    protected Transport createTransport() {
        return new Truck(); // создаём объект другого класса
    }
}

class SeaLogistics extends Logistics {
    @Override
    protected Transport createTransport() {
        return new Ship();
    }
}

```


----
#### [[Factory method - pattern - Flashcards|Link to flashcards]]



---
### References:

