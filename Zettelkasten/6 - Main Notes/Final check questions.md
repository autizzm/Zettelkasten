
Index: [[Java+]]

  
 

- What is the difference between Inversion of Control (IoC) and Dependency Injection (DI)? 

- How Singletons are stored? Which map implementation 

- Are Spring Singleton beans thread-safe? 

- How to inject prototype into singleton (@Lookup, @Scope(proxyMode = TARGET_CLASS, objectProvider) 

- BeanFactory vs. ApplicationContext: What is the difference? 

- BeanPostProcessor vs BeanFactoryPostProcessor 

- What is a BeanPostProcessor? How many of the do we have in a context 

- Why do we need @PostConstruct 

- What are the different Spring Bean Scopes? Refresh scope, Custom scope 

- What is the difference between @Component, @Repository, @Service, and @Controller? 

-  @Repository why do we need exception wrapping? more readable error messages

- How does Spring resolve Circular Dependencies? 

- @Lazy how is it working? 

- @Transactional under the hood (proxy, propagation, rollback rules) Self-invocation problem with @Transactional 

- Spring proxies: JDK dynamic vs CGLIB 

- How many design patterns are used under the hood of Spring framework? Which patterns did you use?

- Spring Boot auto-configuration mechanism. @SpringBootApplication 

- What are the primary differences between REST and gRPC regarding transport protocols and data serialization? 

- When should a developer choose SOAP over REST for an enterprise application? 

- Explain the ""stateless"" principle of RESTful services. 

- HTTP vs HTTPS 

- Explain the concept of ""Idempotency"" in HTTP methods. 

- what is the conceptual difference between Authentication and Authorization? 

- What are the three components of a JSON Web Token (JWT)? 

- Oath and OIDC 

- Explain the TDD (Test-Driven Development) workflow shortly 

- Testing pyramide 

- CI vs CD

### Message Brokers (Kafka & RabbitMQ) 

- How does RabbitMQ handle a message after it is consumed? How we can persist it?

- How does Apache Kafka handle a message after it is consumed? 

- What does the "At-least-once" delivery guarantee mean in Kafka? 

- acks 

- why do we need offsets? 

### Kubernetes & Continuous Delivery (CI/CD) 

- why do we need probes? how they working 

- tell me about CI-CD deployment strategies K8S structure 

- how many nodes you gonna have for your MiniKube config? 

- paymentService deployment or statefulSet 

- K8S node vs pod

- CI vs CD during the course

### Microservices, Containerization & Testing 

- Compare microservice and monolith 
- Microservice patterns 

### NoSQL Databases 

- ACID vs BASE 
- how are we scaling SQL vs noSQL DBs 
- CAP 
- partition tolerance 
- eventual consistency