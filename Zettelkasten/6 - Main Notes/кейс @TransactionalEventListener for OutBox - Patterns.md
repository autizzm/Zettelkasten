
19-04-2026 15:26

Status:

Tags: [[System Design]] [[Patterns]]

---
# кейс @TransactionalEventListener for OutBox - Patterns


### Domain event 

```java
public record OrderCreatedEvent(UUID orderId, String customerId) {}
```
---
### Outbox entity

```java
@Entity
@Table(name = "outbox")
public class OutboxMessage {
    @Id
    private UUID id;

    private String aggregateType;
    private UUID aggregateId;
    private String eventType;

    @Column(columnDefinition = "text")
    private String payload;

    private Instant createdAt;
    private String status;

    protected OutboxMessage() {}

    public OutboxMessage(UUID id, String aggregateType, UUID aggregateId,
                         String eventType, String payload, Instant createdAt, String status) {
        this.id = id;
        this.aggregateType = aggregateType;
        this.aggregateId = aggregateId;
        this.eventType = eventType;
        this.payload = payload;
        this.createdAt = createdAt;
        this.status = status;
    }
}
```

---
### Outbox repository

```java
public interface OutboxRepository extends JpaRepository<OutboxMessage, UUID> {}
```

---
### Service that changes business state and publishes event

```java
@Service
@RequiredArgsConstructor
public class OrderService {
    private final ApplicationEventPublisher eventPublisher;
    private final OrderRepository orderRepository;

    @Transactional
    public Order createOrder(String customerId) {
        Order order = orderRepository.save(new Order(customerId));
        eventPublisher.publishEvent(new OrderCreatedEvent(order.getId(), customerId));
        return order;
    }
}
```

---
### Transactional listener that writes to outbox AFTER COMMIT

```java
@Component
@RequiredArgsConstructor
public class OutboxListener {
    private final OutboxRepository outboxRepository;
    private final ObjectMapper objectMapper;

    @TransactionalEventListener(phase = TransactionPhase.BEFORE_COMMIT)
    public void on(OrderCreatedEvent event) throws JsonProcessingException {
        OutboxMessage msg = new OutboxMessage(
                UUID.randomUUID(),
                "Order",
                event.orderId(),
                "OrderCreatedEvent",
                objectMapper.writeValueAsString(event),
                Instant.now(),
                "NEW"
        );
        outboxRepository.save(msg);
    }
}
```

---

#### Что это даёт

Такой подход удобно разделяет ответственность: бизнес-метод отвечает за доменные изменения, а listener — за фиксацию события в outbox.  
Потом отдельный poller читает `outbox` и публикует сообщение в брокер, помечая запись как `SENT`.


----
#### [[кейс @TransactionalEventListener for OutBox - Patterns - Flashcards|Link to flashcards]]



---
### References:

