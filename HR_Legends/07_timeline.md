# ROP --- Project Timeline

## 1. Estimated duration

**Total project duration: approximately 14 months.**

The duration assumes that the project includes not only implementation
of the target services, but also staged migration from the Oracle
monolith, parallel run, reconciliation and final cutover.

The timeline is a planning model for the described project, not a
historical project record.

## 2. Milestones

  -------------------------------------------------------------------------
  Milestone                                   Period Result
  --------------------- ---------------------------- ----------------------
  M0 --- Discovery                           Month 1 Domain boundaries,
                                                     migration strategy,
                                                     architecture baseline

  M1 --- Foundation                      Months 2--3 `debt-case-service`,
                                                     Kafka contracts, CI,
                                                     environments

  M2 --- MVP                                 Month 4 Basic DebtCase API +
                                                     first restructuring
                                                     workflow

  M3 --- Saga v1                         Months 5--6 Commands/events,
                                                     Inbox, Outbox,
                                                     compensation

  M4 --- Integration                     Months 7--8 Legal holds
  hardening                                          resilience, audit,
                                                     observability

  M5 --- Migration                       Months 8--9 First risk class
  pilot                                              migrated, parallel run

  M6 --- Reconciliation                 Months 9--11 Automated
  at scale                                           reconciliation,
                                                     ordering, mismatch
                                                     classification

  M7 --- Broad cutover                 Months 11--13 40k+ cases migrated,
                                                     legacy traffic
                                                     progressively reduced

  M8 --- Final                              Month 14 Remaining classes cut
  migration                                          over, temporary
                                                     migration subsystem
                                                     retired
  -------------------------------------------------------------------------

## 3. Phase details

### M0 --- Discovery

Outputs:

-   bounded contexts;
-   initial service boundaries;
-   migration strategy;
-   ownership model;
-   initial Kafka event catalog;
-   compliance constraints.

Architecture review determines:

``` text
Monolith
   |
   v
Strangler Fig
   |
   +--> target service
   |
   +--> legacy
```

### M1 --- Foundation

Outputs:

-   repository structure;
-   Spring Boot services;
-   PostgreSQL;
-   Kafka;
-   CI;
-   Flyway;
-   observability baseline;
-   JSON Schema contract repository.

### M2 --- MVP

First usable target flow:

``` text
DebtCase
   |
   v
Proposal
   |
   v
Notification
   |
   v
Client response
```

At this stage the architecture is functional but not yet fully hardened
for all failure scenarios.

### M3 --- Saga v1

Introduced:

-   Camunda orchestration;
-   Kafka command topics;
-   Inbox;
-   Outbox;
-   compensation;
-   workflow audit.

A key architectural change from the monolith is that the business
transaction is no longer one Oracle transaction.

### M4 --- Integration hardening

Introduced:

-   legal-hold integration service;
-   circuit breaker;
-   bulkhead;
-   `UNKNOWN` response state;
-   workflow lock;
-   orphan lock recovery;
-   canary deployment.

### M5 --- Migration pilot

First migration class enters:

``` text
extract
  -> load
  -> parallel run
  -> reconcile
  -> cutover
```

Manual reconciliation is initially retained as a safety mechanism.

### M6 --- Reconciliation at scale

Automation reduces manual verification from approximately:

``` text
~400 cases/week
```

to:

``` text
~15–20 cases/week
```

The target is not zero mismatches; the target is automatic
classification of explainable mismatches and manual review only of the
residual risk.

### M7 --- Broad cutover

Migration proceeds by risk classes.

A class becomes eligible for cutover after an agreed number of
consecutive clean reconciliation days.

### M8 --- Final migration

After all classes are migrated:

-   legacy handlers are disabled;
-   migration tables are archived/removed;
-   reconciliation service is retired;
-   `debt-case-service` remains a permanent target-domain service.

## 4. Release cadence

Business domain releases:

``` text
every 2–3 weeks
```

but not rigidly tied to sprint boundaries.

The actual release date can be controlled by the compliance gate.

Hotfixes are released outside the normal cadence when required.
