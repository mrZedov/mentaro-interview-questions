# RabbitMQ interview questions

[Explore this topic on Mentaro](https://www.mentaro.app/interview-questions/rabbitmq) · [Try the RabbitMQ practice demo](https://www.mentaro.app/demo?topic=rabbitmq&utm_source=github)

## Foundations

### 1. What do exchanges, bindings and queues each do?

<details>
<summary>Show answer</summary>

A publisher sends to an exchange. Bindings describe how it routes, and queues hold the routed messages for consumers. The same publish can reach more than one queue.

</details>

### 2. How do direct, fanout and topic exchanges select destinations?

<details>
<summary>Show answer</summary>

Direct exchanges use an exact routing-key match, fanout exchanges ignore the key and route to bound queues, and topic exchanges match key segments against binding patterns.

</details>

### 3. How does a publisher confirm differ from a consumer acknowledgement?

<details>
<summary>Show answer</summary>

A confirm describes the broker's responsibility for the publish under the queue's guarantees. A consumer acknowledgement tells the broker that a specific delivery can be removed. Neither proves both happened.

**Explanation:** Trace the failure windows separately. A producer might lose the connection before it sees a confirm even though the broker accepted the publish. A consumer can finish the business write and disconnect before acknowledging. Both paths can produce duplicates, so deduplicate where the durable business effect occurs.

</details>

### 4. Does declaring a queue durable make every published message persistent?

<details>
<summary>Show answer</summary>

No. Queue durability concerns the queue definition; message delivery mode governs message persistence where applicable. Use confirms to observe broker acceptance, not just successful client-side writes.

</details>

### 5. What does prefetch control?

<details>
<summary>Show answer</summary>

It limits deliveries awaiting acknowledgement according to the configured scope. Set it with processing concurrency and memory in mind rather than using it as a delivery-rate limit.

</details>

## Applied

### 6. What happens if a consumer loses its connection before acknowledging a delivery?

<details>
<summary>Show answer</summary>

The broker can requeue the unacknowledged message. The business operation might already have committed, so design the consumer to tolerate processing the same event again.

</details>

### 7. When should a consumer acknowledge after writing to a database?

<details>
<summary>Show answer</summary>

Acknowledge after the transaction commits. For duplicates, make the write idempotent or record the message ID in the same transaction so redelivery cannot repeat the effect.

</details>

### 8. Can the broker confirm an unroutable publish?

<details>
<summary>Show answer</summary>

Yes. A confirm alone is not evidence that a queue matched. When using the mandatory flag, handle returned messages separately and correlate them with the publication.

</details>

### 9. Why can immediate requeue of every failure hurt throughput?

<details>
<summary>Show answer</summary>

A failing message can be redelivered repeatedly without giving the dependency time to recover. Use capped retries with delay, then move exhausted work into a defined failure path.

</details>

### 10. What does a dead-letter exchange do?

<details>
<summary>Show answer</summary>

It receives messages dead-lettered by a queue under configured conditions, such as a rejection without requeue. It still needs bindings to route messages to an intended destination.

</details>

### 11. Why would a RabbitMQ operator set a queue length limit?

<details>
<summary>Show answer</summary>

A limit bounds backlog and resource use, but the overflow behavior affects delivery guarantees. Decide whether to reject new publishes or discard queued work, and verify producer confirms and monitoring under load.

</details>

## Advanced scenarios

### 12. How do you distinguish a producer surge from blocked RabbitMQ consumers?

<details>
<summary>Show answer</summary>

Compare publish, delivery and acknowledgement rates and the counts of ready and unacknowledged messages. Growing unacknowledged work can point to slow handlers; increasing ready work with stable processing can indicate excess arrivals.

</details>

### 13. Can you assume messages are processed in order with several consumers?

<details>
<summary>Show answer</summary>

No. Delivery order and completion order differ, and redelivery can change effective order. If an entity requires ordered effects, coordinate its consumers and enforce sequencing in application state.

</details>

### 14. What does a quorum queue trade for resilience?

<details>
<summary>Show answer</summary>

It replicates queue state across members using a consensus protocol. It depends on a majority being available and adds replication cost; place replicas across appropriate failure domains.

</details>

### 15. A publisher connection drops before a confirm arrives. What should the publisher assume?

<details>
<summary>Show answer</summary>

The outcome is unknown: the broker may have accepted the message. Retrying preserves delivery chances but can produce duplicates. Keep a stable message identifier and make consumers replay-safe.

</details>

### 16. How do you drain a RabbitMQ consumer during rollout?

<details>
<summary>Show answer</summary>

Stop accepting new deliveries, finish or safely abandon in-flight work, and acknowledge only successful commits. Let unresolved deliveries be requeued when the channel closes and verify handlers are safe on replay.

</details>

### 17. Why can aggressive consumer prefetch increase latency for some jobs?

<details>
<summary>Show answer</summary>

A consumer can hold many deliveries that other workers could otherwise process sooner. Tune prefetch against handler concurrency and processing-time variance, and observe ready versus unacknowledged messages.

</details>

## Official references

- [RabbitMQ: Consumer acknowledgements and publisher confirms](https://github.com/rabbitmq/rabbitmq-website/blob/main/docs/confirms.md)
- [RabbitMQ: Queues](https://github.com/rabbitmq/rabbitmq-website/blob/main/docs/queues.md)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).

[Try the RabbitMQ practice demo](https://www.mentaro.app/demo?topic=rabbitmq&utm_source=github)
