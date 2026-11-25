# Microservices interview questions

[Explore this topic on Mentaro](https://www.mentaro.app/interview-questions/microservices) · [Try the Microservices practice demo](https://www.mentaro.app/demo?topic=microservices&utm_source=github)

## Foundations

### 1. When is a modular monolith a better starting point than microservices?

<details>
<summary>Show answer</summary>

Keep one deployable when boundaries are changing quickly, one team owns the system and independent scaling is not needed. Enforce module boundaries in code. Split a module when there is a concrete benefit worth the cost of a network contract, extra infrastructure and independent failure modes.

**Explanation:** For example, if account creation and profile setup change together and require one transaction, separating them immediately adds network and coordination failures without clear value. Give each module a boundary first; revisit extraction when independent release, scaling or fault isolation has measurable value.

</details>

### 2. How can you tell whether two proposed services have the wrong boundary?

<details>
<summary>Show answer</summary>

Look for changes that routinely require modifying and deploying both, or rules that need an atomic write to both sets of data. Those are signs the boundary cuts across one business capability. Measure release coupling and call frequency before choosing where to move responsibility.

</details>

### 3. Does database per service require a separate database server for every service?

<details>
<summary>Show answer</summary>

The requirement is ownership: other services do not read or mutate the service's tables directly. Separate schemas or logical databases can share infrastructure if privileges and isolation suffice. Dedicated infrastructure is useful when load, recovery or availability requirements differ.

</details>

### 4. What do you trade when replacing a synchronous call with a queue?

<details>
<summary>Show answer</summary>

A queue buffers work while the consumer is temporarily unavailable and lets producers finish earlier. The caller loses an immediate result and must account for delayed processing, duplicates and growing backlog. If a user needs a decision now, asynchronous acceptance alone may not satisfy the requirement.

</details>

### 5. What should a service discovery mechanism tell a caller?

<details>
<summary>Show answer</summary>

It maps a logical service name to endpoints the caller can reach. Registration, health and DNS caching determine how fast the view changes. Discovery does not guarantee a request will succeed: endpoints can become unhealthy between lookup and use, so callers still need deadlines and failure handling.

</details>

## Applied

### 6. How do you stop each layer of a request chain from spending the full timeout?

<details>
<summary>Show answer</summary>

Propagate a deadline or remaining time budget from the entry point. Reserve time to return the response and bound each dependency call by the remaining budget. Stop starting new work once the deadline cannot be met; cancellation helps avoid spending resources on abandoned requests.

</details>

### 7. What is the difference between a circuit breaker and a bulkhead?

<details>
<summary>Show answer</summary>

A circuit breaker temporarily rejects calls to a failing dependency so it has a chance to recover. A bulkhead isolates resources such as concurrency slots so one dependency or tenant cannot consume them all. A failing dependency can require both faster rejection and resource isolation.

</details>

### 8. Why can an event be delivered twice even with a transactional outbox?

<details>
<summary>Show answer</summary>

The outbox makes the state change and publication intent atomic, not broker delivery. A relay can publish and crash before marking the record dispatched. Consumers should deduplicate using a durable event ID or make the business action naturally idempotent.

</details>

### 9. What makes a saga compensation different from rolling back a database transaction?

<details>
<summary>Show answer</summary>

Compensation is a new business action after already committed steps. It can fail or need a policy decision, and external effects such as a sent email cannot be undone. Persist the workflow state, retry safely and make unresolved cases visible to operators.

</details>

### 10. How does a read projection handle messages delivered out of order?

<details>
<summary>Show answer</summary>

Use a source version per entity when projecting full states, so an older message cannot overwrite a newer one. For incremental events, detect missing versions and replay or rebuild rather than dropping needed changes. Specify what users see while the projection catches up.

</details>

### 11. What should a consumer-driven contract test verify that a health check cannot?

<details>
<summary>Show answer</summary>

It verifies the response or event shapes and semantics a particular consumer relies on. A healthy process can still violate that contract. Keep the test focused on required behavior rather than every incidental field.

</details>

## Advanced scenarios

### 12. What happens if three layers each retry a failing dependency twice?

<details>
<summary>Show answer</summary>

If each initial request can make three attempts, a fully exhausted chain can produce up to 27 calls at the final layer. Choose one retry owner, cap attempts within the deadline, use jitter and retry only operations that can safely be repeated.

</details>

### 13. How do you find the cause of one slow request crossing five services?

<details>
<summary>Show answer</summary>

Propagate trace context and look at the request's critical path rather than summing all span durations. Compare slow and healthy traces, then use dependency latency and error metrics to check whether the example is representative. Include queue wait if the request crosses messaging.

</details>

### 14. How do you replace an event field when consumers deploy independently?

<details>
<summary>Show answer</summary>

Introduce the new field without removing the old one. Update consumers to accept both versions, then update producers and verify compatibility through a rollout window. Retain the old reader path as long as old events can be replayed, or define a versioned conversion.

</details>

### 15. How do you know if a growing queue is a temporary burst or sustained overload?

<details>
<summary>Show answer</summary>

Compare arrival and successful processing rates, and track the age of the oldest unprocessed message. If consumers consistently process less than arrivals, adding buffer only delays the failure. Look for a throughput bottleneck, isolate poison messages and apply backpressure where needed.

</details>

### 16. What evidence would justify rolling back a canary service release?

<details>
<summary>Show answer</summary>

Define guardrails before the release: error rate, latency, resource saturation and a business success metric. Compare the canary with a similar traffic cohort on the previous version. Check whether migrations and written data remain backward-compatible before assuming a binary rollback is safe.

</details>

### 17. How do you test whether a critical service can run without an optional dependency?

<details>
<summary>Show answer</summary>

Introduce timeouts or failures in a controlled environment and check that critical operations still meet their latency and correctness targets. Verify degraded responses, recovery and monitoring, not just that a fallback exists.

</details>

## Official references

- [Microsoft Azure Architecture Center: Microservices](https://learn.microsoft.com/en-us/azure/architecture/guide/architecture-styles/microservices)
- [Amazon Builders' Library: Timeouts, retries and backoff with jitter](https://aws.amazon.com/builders-library/timeouts-retries-and-backoff-with-jitter/)
- [OpenTelemetry: Context propagation](https://opentelemetry.io/docs/concepts/context-propagation/)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).

[Try the Microservices practice demo](https://www.mentaro.app/demo?topic=microservices&utm_source=github)
