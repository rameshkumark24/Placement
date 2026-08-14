# Day 185A

**L-185A** · Spring Kafka and RabbitMQ — messaging that does not lose or duplicate

**Time:** 3 hrs · **Mode:** NEW

> Day 125 built the concepts: acknowledgement modes, dead-letter queues, visibility timeouts, and
> Kafka's log model versus a queue's. Day 124 established the dual-write problem and the outbox.
>
> Today wires both brokers in Spring, and the through-line is one question: **at-least-once delivery
> is what you actually get, so what makes your consumer safe when the same message arrives twice?**

---

# Part 1 — Choosing, honestly

| | **Kafka** | **RabbitMQ** |
|---|---|---|
| Model | ⭐ **an ordered, replayable log** | ⭐ a broker that routes and deletes |
| Retention | ⭐ time/size-based — **replay is possible** | until acknowledged |
| Ordering | ⭐ per partition | per queue, lost with concurrent consumers |
| Routing | consumer filters | ⭐ **rich — topic, headers, fanout** |
| Per-message TTL, priority, delay | ❌ awkward | ⭐ ✅ native |
| Throughput | ⭐ very high | high |
| Operational weight | ⭐ heavier | lighter |
| Best for | ⭐ **event streaming, audit, replay, many consumers** | ⭐ **task queues, RPC, complex routing** |

**⭐ The deciding question is replay.** If a consumer has a bug and you need to reprocess last
Tuesday's events, Kafka can and Rabbit cannot — the messages are gone. If you need per-message delay,
priority or content-based routing, Rabbit does it natively and Kafka makes you build it.

**Both is common and legitimate**: Kafka for domain events, Rabbit for work queues.

---

# Part 2 — Kafka in Spring

```yaml
spring.kafka:
  bootstrap-servers: ${KAFKA_BROKERS}
  producer:
    acks: all                                # ⭐⭐ 1
    enable-idempotence: true                 # ⭐⭐ 2
    retries: 2147483647
    properties.max.in.flight.requests.per.connection: 5
  consumer:
    group-id: orders-service
    auto-offset-reset: earliest              # ⭐ 3
    enable-auto-commit: false                # ⭐⭐ 4
    isolation-level: read_committed
    max-poll-records: 100
  listener:
    ack-mode: MANUAL_IMMEDIATE               # ⭐ 4
    concurrency: 3                            # ⭐ ≤ partition count
```

**⭐ 1 — `acks: all`.** The default (`acks=1`) acknowledges when the *leader* has the message. If the
leader dies before replication, **the message is gone with no error.** `all` waits for the in-sync
replicas.

**⭐ 2 — `enable-idempotence: true`.** Without it, a producer retry after a network timeout writes the
message **twice** — the broker got it, the acknowledgement was lost, the producer retried. Idempotence
gives each producer a sequence number so the broker deduplicates. **This is the setting that stops
your own retries from creating duplicates.**

**⭐ 3 — `auto-offset-reset`.** `earliest` reprocesses from the start when a group has no offset;
`latest` skips everything published before the consumer existed. Neither is universally right —
`latest` silently drops messages published during a deploy of a brand-new consumer.

**⭐ 4 — manual acknowledgement.** With auto-commit, offsets advance on a timer *whether or not you
processed successfully* — so a crash mid-batch loses messages permanently. Manual commit after
processing converts that into at-least-once.

```java
@KafkaListener(topics = "orders.placed", groupId = "notifications")
public void handle(@Payload OrderPlacedEvent event,
                   @Header(KafkaHeaders.RECEIVED_KEY) String key,
                   Acknowledgment ack) {
    try {
        notifications.send(event);            // ⭐ MUST be idempotent — Part 4
        ack.acknowledge();                    // ⭐ only after success
    } catch (TransientException e) {
        throw e;                              // ⭐ retry + DLT handle it
    } catch (PermanentException e) {
        deadLetter(event, e);
        ack.acknowledge();                    // ⭐ don't retry what will never succeed
    }
}
```

**⭐ Distinguishing transient from permanent is the single most important design decision in a
consumer.** A malformed message retried forever is a **poison pill** that blocks the partition —
nothing behind it is ever processed, and because Kafka is ordered, that is a stalled stream, not one
lost message.

```java
@Bean
DefaultErrorHandler errorHandler(KafkaTemplate<?, ?> template) {
    var recoverer = new DeadLetterPublishingRecoverer(template);          // ⭐ → orders.placed.DLT
    var handler = new DefaultErrorHandler(recoverer,
            new ExponentialBackOffWithMaxRetries(3));                     // ⭐ bounded — Day 123
    handler.addNotRetryableExceptions(DeserializationException.class,     // ⭐ never retryable
                                      MethodArgumentNotValidException.class);
    return handler;
}
```

**⭐ Alert on the DLT depth.** Messages arriving there are messages your system did not process, and
a DLT nobody monitors is a queue of silent data loss.

**And `concurrency` must not exceed the partition count** — extra consumers in a group sit idle,
because a partition is assigned to exactly one consumer.

---

# Part 3 — RabbitMQ in Spring

```yaml
spring.rabbitmq:
  publisher-confirm-type: correlated         # ⭐⭐ broker confirms receipt
  publisher-returns: true                    # ⭐ unroutable messages come back
  listener.simple:
    acknowledge-mode: manual                 # ⭐
    prefetch: 20                             # ⭐⭐ see below
    default-requeue-rejected: false          # ⭐⭐ see below
```

```java
@Bean Queue ordersQueue() {
    return QueueBuilder.durable("orders")                      // ⭐ survives broker restart
        .withArgument("x-dead-letter-exchange", "orders.dlx")  // ⭐ DLQ
        .withArgument("x-message-ttl", 86_400_000)
        .build();
}
```

```java
@RabbitListener(queues = "orders")
public void handle(OrderPlacedEvent event, Channel channel,
                   @Header(AmqpHeaders.DELIVERY_TAG) long tag) throws IOException {
    try {
        orders.process(event);
        channel.basicAck(tag, false);
    } catch (PermanentException e) {
        channel.basicNack(tag, false, false);      // ⭐⭐ requeue=false → DLQ
    }
}
```

**⭐ `default-requeue-rejected: false` prevents the infinite-redelivery loop**, which is Rabbit's
version of the poison pill: a message that always fails, requeued forever, consuming a consumer
permanently and filling logs. **Set it, and route to a DLQ.**

**⭐ `prefetch` is the backpressure control** (Day 070). Unlimited prefetch means one consumer grabs
thousands of messages, so work is not distributed and a crash redelivers all of them. 20–50 is a
reasonable starting point; the right number is roughly `concurrency × (processing time / ack time)`.

**⭐ Publisher confirms are the analogue of `acks: all`.** Without them, `convertAndSend` returns
successfully when the message reached the *socket* — not the broker. A broker restart at that moment
loses it silently.

---

# Part 4 — ⭐⭐ The two properties that decide correctness

## ⭐ 1. At-least-once means your consumer must be idempotent

**Both brokers, correctly configured, give at-least-once.** Exactly-once is achievable in narrow
cases (Kafka transactions within Kafka), and **it is not available for the case that matters** — a
message whose handler charges a card or sends an email, because those side effects are outside the
broker's transaction.

**So duplicates will happen.** The consumer must make that harmless:

```java
@Transactional
public void process(OrderPlacedEvent event) {
    if (!processed.markProcessed(event.eventId())) {     // ⭐ unique constraint on event_id
        log.debug("duplicate_event ignored eventId={}", event.eventId());
        return;                                          // ⭐ already done
    }
    doTheWork(event);                                    // ⭐ same transaction as the marker
}
```

**⭐ The marker and the work must commit together**, or you mark-then-crash and lose the message, or
work-then-crash and duplicate it. Day 123's idempotency keys, in a consumer.

**Three ways to be idempotent**, best first:

1. ⭐ **Naturally idempotent operations** — `set status = SHIPPED` is safe any number of times.
2. ⭐ **A processed-events table** with a unique constraint, in the same transaction.
3. **Upsert on a business key** — `ON CONFLICT DO NOTHING`.

## ⭐ 2. Publishing is a dual-write — use the outbox

```java
// ❌ Day 124's dual-write
@Transactional
public void place(Request req) {
    repo.save(order);
    kafkaTemplate.send("orders.placed", event);    // ⭐ commits? maybe. Rolls back? never.
}
```

**Two failure modes:** the send succeeds and the transaction rolls back (an event for an order that
does not exist), or the transaction commits and the send fails (an order nobody downstream knows
about).

```java
// ⭐ The outbox
@Transactional
public void place(Request req) {
    repo.save(order);
    outbox.save(new OutboxEntry(order.getId(), "OrderPlaced", toJson(event)));  // ⭐ same transaction
}
```

Then a poller (Day 178, with `SKIP LOCKED`) or Debezium change-data-capture publishes from the outbox
with retries. **The event is committed with the order or not at all**, which is the only arrangement
with no failure window.

**⭐ And the outbox makes duplicates certain**, not merely possible — a publish that succeeds before
the row is marked sent will be republished. Which is fine, because point 1 already handled it. The
two halves fit together: **the outbox guarantees at-least-once, and idempotent consumers make
at-least-once sufficient.**

## Testing (Day 184)

```java
@Container static final KafkaContainer KAFKA = new KafkaContainer(parse("confluentinc/cp-kafka:7.6.0"));

@Test void duplicateDeliveryIsHarmless() {
    template.send("orders.placed", event);
    template.send("orders.placed", event);                  // ⭐ same event twice
    await().atMost(10, SECONDS).untilAsserted(() ->
        assertThat(repo.countByOrderId(event.orderId())).isEqualTo(1));   // ⭐ THE test
}
```

**That test is the whole day.** Write it for every consumer.

---

## Common mistakes

| Mistake | Why it hurts |
|---|---|
| ⭐ `acks: 1` | leader failover loses messages silently |
| ⭐ No producer idempotence | your own retries duplicate |
| ⭐ Auto-commit | offsets advance regardless of success — permanent loss |
| ⭐ Non-idempotent consumer | at-least-once becomes double charges |
| Unbounded retries | poison pill blocks the partition forever |
| No DLQ/DLT, or one nobody watches | silent data loss |
| ⭐ Publishing inside a transaction | Day 124's dual-write, both directions |
| Rabbit `requeue=true` on permanent failure | infinite redelivery loop |
| No publisher confirms | "sent" means "reached the socket" |
| Unlimited prefetch | no distribution, large redelivery on crash |
| `concurrency` above partition count | idle consumers |
| Expecting exactly-once | not available where side effects leave the broker |
| Entities in message payloads | detached, lazy, version-coupled — send ids and values |
| No schema/versioning strategy | Day 109 — a producer change breaks every consumer |

## Interview questions

**Q: Kafka or RabbitMQ?**
Kafka when I need an ordered, replayable log — event streaming, audit, or several independent
consumers reading the same events, especially if reprocessing after a consumer bug matters. RabbitMQ
for task queues and complex routing, where per-message TTL, priority and content-based routing are
native. Using both is normal.

**Q: How do you guarantee a message is not lost?**
On the producer, `acks=all` with idempotence so a retry does not duplicate. On the consumer, manual
acknowledgement after successful processing so a crash redelivers rather than skips. And publishing
goes through an outbox written in the same transaction as the business data, because sending inside a
transaction is a dual-write that can fail in either direction.

**Q: Can you get exactly-once?**
Not for anything with a side effect outside the broker — charging a card or sending an email cannot
join Kafka's transaction. So I design for at-least-once and make the consumer idempotent, usually
with a processed-events table whose insert commits in the same transaction as the work.

**Q: What is a poison pill?**
A message that always fails and is retried forever. In Kafka it blocks the partition, so nothing
behind it is processed — a stalled stream, not one lost message. Bounded retries and a dead-letter
topic, with deserialization failures marked non-retryable, are the fix.

## Mini task

1. Run Kafka in Testcontainers. Produce and consume with manual acknowledgement.
2. Set `acks=1`, kill the leader mid-publish, and **find the lost message.** Set `acks=all`.
3. Disable producer idempotence, force a retry with a network fault, and **find the duplicate.**
4. Enable auto-commit, crash mid-batch, and confirm the skipped messages.
5. Send a malformed message with unbounded retries. **Watch the partition stall.** Add the error
   handler and DLT.
6. Send the same event twice to a non-idempotent consumer. Confirm the duplicate effect. Add the
   processed-events table and re-run.
7. Mark the event processed in a *different* transaction from the work. Crash between them. Observe
   both failure modes.
8. Implement the outbox. Force a rollback after `outbox.save` and confirm nothing publishes.
9. In RabbitMQ, nack with `requeue=true` on a permanently failing message. **Watch the infinite
   loop.** Set `default-requeue-rejected: false`.
10. Set prefetch to unlimited with two consumers and confirm one takes everything.

# 🚪 Exit questions

1. Give five differences between Kafka and RabbitMQ, and the deciding question.
2. What does `acks: all` prevent, and what is the default's failure?
3. What does producer idempotence prevent?
4. Why is auto-commit unsafe?
5. What is a poison pill, and why is it worse in Kafka?
6. Why must deserialization failures be non-retryable?
7. Why alert on DLQ depth?
8. What is `prefetch` for, and what does unlimited cause?
9. Why is `default-requeue-rejected: false` important?
10. What do publisher confirms guarantee that `convertAndSend` does not?
11. Why is exactly-once unavailable in the cases that matter?
12. Give three ways to make a consumer idempotent, best first.
13. Why must the processed marker share the work's transaction?
14. Why does the outbox make duplicates certain, and why is that acceptable?

## 🎙️ Articulation drill

Two minutes: **"How do you build a reliable message-driven system?"**

Producer side: `acks=all` with idempotence, and publish through an outbox because sending inside a
transaction is a dual-write that fails in both directions. Consumer side: manual acknowledgement,
bounded retries, a dead-letter topic that is monitored, and deserialization failures marked
non-retryable so a poison pill cannot stall a partition. Then the framing that ties it together — you
get at-least-once and cannot get exactly-once once side effects leave the broker, so the consumer
must be idempotent, usually a processed-events table committing in the same transaction as the work.

---

**Previous:** [Day 185](Day-185.md) · **Next:** [Day 185B](Day-185B.md) — ➕ Spring Boot 3 specifics · 🚪 Stage 4 exit gate
