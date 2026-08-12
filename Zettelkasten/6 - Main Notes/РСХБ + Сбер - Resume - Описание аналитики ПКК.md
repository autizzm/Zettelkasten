
Tags: [[РСХБ + Сбер - Resume]] [[BrandNew - Legend]] [[HR параша]]


# Analytical metrics aggregator service for the platform of corporate lending platform


## Goal

Build a horizontally scalable analytical metrics aggregation service based on **Kafka Streams**. The service is completely separated from the business services and is responsible only for **event retrieval, normalization, deduplication, aggregation, and persistence of metrics into the analytical database**.

The lending platform consists of a large number of Kafka topics produced by different microservices. Each topic contains at least **3 partitions**, and producers may partition events using keys unrelated to the analytical dimensions (for example, `eventId`, `loanId`, etc.).

The service must calculate metrics across multiple **aggregation dimensions**, such as:

- client,
    
- product,
    
- region,
    

and some metrics may use **composite dimensions** (for example, `product + region`).

Each incoming event contains at most **three dimensions**.

Example:

`RepaymentDue(eventId, clientId, productId, regionId, amount, timestamp)`

---

# High-level architecture

The processing pipeline is divided into **two stages**:

1. **Normalization and deduplication**
    
2. **Dimension-specific aggregation**
    

The critical architectural decision is that **aggregation is performed only after repartitioning by the required aggregation key**. Kafka Streams creates **internal repartition topics** (real Kafka topics managed automatically by Streams), which allows all events belonging to the same aggregation key to be routed to the same processing task and therefore preserves horizontal scalability.

```
Input topics
(partitioned by producer key)
        |
        v
Normalization / ACL
        |
        v
Deduplication (eventId state store)
        |
        +-----------------------------+
        |             |               |
        |             |               |
    key=client    key=product    key=region
        |             |               |
   repartition    repartition    repartition
        |             |               |
   aggregate      aggregate      aggregate
        |             |               |
        +-------------+---------------+
                      |
                Metric persistence
```

---

# Event processing pipeline

## 1. Event ingestion

An event is consumed from the original Kafka topic partition and mapped into an internal DTO.

If the producer already follows the internal event contract, it is mapped directly into `InternalEvent`.

All internal events implement a common interface:

```java
interface InternalEvent {
    String getEventId();
    BigDecimal getMetricNumeric();
    Object getMetricAdditional();

    String getDimensionOne();
    String getDimensionTwo();
    String getDimensionThree();

    Instant getEventTime();
}
```

---

## 2. Anti-Corruption Layer (ACL)

Some legacy services emit denormalized events (for example, region names instead of region identifiers) or do not provide a globally unique event identifier.

The ACL performs:

- normalization of dimension values,
    
- mapping into the canonical `InternalEvent`,
    
- construction of a deterministic **composite `eventId`** for legacy events.
    

After this stage, all events are guaranteed to conform to the internal analytical contract.

---

## 3. Deduplication

Deduplication is performed **before any repartitioning**, so each event is checked exactly once regardless of how many aggregation branches it will later enter.

The `eventId` is stored in a Kafka Streams **timestamped key-value state store**.

Processing logic:

- if `eventId` exists → drop the event,
    
- otherwise store it and continue processing.
    

The deduplication store retention is **not tied directly to the aggregation window**.

Recommended retention:

```
largest aggregation window
+ maximum accepted lateness
+ replay / retry margin
+ safety buffer
```

For example, with a 24-hour window and 30-minute grace period, a retention of **36-48 hours** is appropriate.

---

## 4. Metric routing and aggregation branching

After deduplication, the event is routed to one or more **aggregation branches** depending on which dimensions are required by each metric.

Examples:

- client branch
    
- product branch
    
- region branch
    
- product-region branch
    

Each branch performs:

```java
selectKey(...)
```

which causes Kafka Streams to create an **internal repartition topic** keyed by the aggregation dimension.

This ensures that **all events with the same aggregation key are processed by the same Kafka Streams task**, allowing correct distributed aggregation while preserving horizontal scalability.

Examples:

```
clientId
productId
regionId
productId|regionId
clientId|productId|regionId
```

---

## 5. Metric calculation

Each branch forwards events into a **Metric Processing Manager**, which selects the appropriate aggregation strategy.

Supported metric types include:

- Counter
    
- Sum
    
- Average
    
- Minimum
    
- Maximum
    
- Ratio
    
- Other custom aggregations
    

A generic processor abstraction can be used:

```java
MetricAggregator<E, State, Result>
```

where:

- `State` is the intermediate window state,
    
- `Result` is the value written to the database.
    

---

## 6. Windowed aggregation

Metrics are calculated for multiple windows simultaneously:

- 1 minute
    
- 5 minutes
    
- 10 minutes
    
- 15 minutes
    
- 20 minutes
    
- 60 minutes
    
- 24 hours
    

Kafka Streams **windowed state stores** are used for intermediate aggregation.

Example:

```java
TimeWindows.ofSizeAndGrace(windowSize, gracePeriod)
```

The **grace period** represents the waiting window for late events.

Window lifecycle:

1. events are accumulated during the window;
    
2. when the window closes, a **preliminary metric** is emitted;
    
3. during the grace period late events may still update the window;
    
4. after the grace period expires, the **final metric** is emitted.
    

The database layer performs **idempotent UPSERTs** using a key such as:

```
(metric_type,
 aggregation_key,
 window_start,
 window_end)
```

This makes replay and recovery deterministic.

---

# Multi-event metrics

Some metrics require combining multiple event types.

Example:

```
RepaymentCompleted / RepaymentDue
```

Requirements:

- events may arrive out of order,
    
- events may be late,
    
- both streams must contribute to the same time window.
    

Recommended approach:

1. normalize both event types,
    
2. repartition them using the same aggregation key,
    
3. perform independent windowed aggregations,
    
4. join the resulting windowed tables,
    
5. compute the derived metric.
    

Because both aggregations use the same window definition and grace period, Kafka Streams automatically handles:

- out-of-order arrival,
    
- late updates,
    
- window recomputation before finalization.
    

The result follows the same **preliminary → updated → final** persistence lifecycle as all other metrics.