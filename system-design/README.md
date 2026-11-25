# System Design interview questions

[Explore this topic on Mentaro](https://www.mentaro.app/interview-questions/system-design) · [Start interactive practice on Mentaro](https://www.mentaro.app/login?mode=signup&redirect=/onboarding)

## Foundations

### 1. What should you clarify before drawing a system diagram?

<details>
<summary>Show answer</summary>

Ask who uses the system, the main operations and their required availability and latency. Identify what must never happen, such as a confirmed reservation being sold twice.

</details>

### 2. Why estimate peak traffic separately from daily traffic?

<details>
<summary>Show answer</summary>

A daily average can hide bursts that overwhelm queues or connection pools. Estimate the busiest interval, read-to-write mix and request size, then check how sensitive the design is to each assumption.

**Explanation:** One million requests a day averages about 12 per second. If 10% arrive within one minute, that minute averages about 1,667 requests per second. Then look inside that minute: short bursts matter for queue limits, while sustained bursts determine how long reduced capacity can last.

</details>

### 3. How do a replication strategy and a partitioning strategy differ?

<details>
<summary>Show answer</summary>

Replication creates copies of data for redundancy or reads. Partitioning divides the dataset so different nodes own different ranges. A system can do both by replicating each partition.

</details>

### 4. What does a stateless application tier make easier?

<details>
<summary>Show answer</summary>

Any healthy instance can normally serve a request, so scaling and recovery do not depend on a particular process. Durable state still needs an external owner and failure policy.

</details>

### 5. What does p99 latency tell you that average latency does not?

<details>
<summary>Show answer</summary>

It describes the threshold at or below which about 99% of observations fall. A small fraction of slow requests can be invisible in the average yet affect many real users.

</details>

## Applied

### 6. Why can a well-distributed partition key still create a hotspot?

<details>
<summary>Show answer</summary>

Keys can be evenly distributed while traffic is highly skewed. A popular item can overload the partition that owns it. Consider caching hot reads or splitting writes if the invariant allows it.

</details>

### 7. How would you provide read-your-writes after moving reads to replicas?

<details>
<summary>Show answer</summary>

Read from the primary temporarily, or use a commit position and wait for a replica that has caught up. Define the fallback if the replica cannot meet the required position within the deadline.

</details>

### 8. When does an asynchronous job queue improve perceived latency?

<details>
<summary>Show answer</summary>

It lets the user receive acceptance without waiting for processing. Expose status and errors separately, and enforce a backlog limit so a fast acceptance response does not hide a very long wait.

</details>

### 9. How would you limit cache stampedes when a popular entry expires?

<details>
<summary>Show answer</summary>

Coalesce concurrent refreshes, stagger expirations and consider serving stale values if the product permits it. Preserve an upper freshness bound for data on which decisions depend.

</details>

### 10. When would you keep a materialized read model instead of querying the source of truth directly?

<details>
<summary>Show answer</summary>

Use a projection when read latency or query shape justifies the extra representation. Define acceptable lag, how to rebuild it and what clients see while the projection is stale.

</details>

### 11. How do you choose a pagination approach for a feed that changes during browsing?

<details>
<summary>Show answer</summary>

Use a cursor based on a stable total ordering, including a unique tie-breaker. Decide whether browsing should preserve a snapshot or reflect live changes. Test insertions and removals between page requests.

</details>

## Advanced scenarios

### 12. What trades off when writes fail over between regions?

<details>
<summary>Show answer</summary>

Synchronous replication can reduce loss at the cost of latency and availability during partitions. Asynchronous replication permits faster local commits but may lose recent writes on failover. Fence the old writer before accepting writes elsewhere.

</details>

### 13. How do recovery point and recovery time objectives shape backups?

<details>
<summary>Show answer</summary>

Recovery point is tolerable data loss measured in time; recovery time is how long restoration may take. Backup and log-shipping frequency affect the former, while restore rehearsals and capacity planning establish the latter.

</details>

### 14. How does fan-out affect latency when a request waits for every dependency?

<details>
<summary>Show answer</summary>

The slowest required response dominates. As fan-out grows, a rare slow dependency becomes more likely to affect any given request. Limit fan-out, use deadlines and make optional results genuinely optional.

</details>

### 15. What is load shedding and how do you choose what to shed?

<details>
<summary>Show answer</summary>

Reject or defer work before saturation makes all requests fail. Protect the cheapest or highest-priority critical operations and bound queues. Communicate retry expectations so rejected work does not immediately return.

</details>

### 16. How would you prove a design can survive losing its largest node?

<details>
<summary>Show answer</summary>

Check remaining headroom and run a controlled failure test under representative load. Observe failover time, queue growth and data invariants; healthy idle failover does not prove capacity at peak.

</details>

### 17. How would you roll back a feature after it has already written a new data format?

<details>
<summary>Show answer</summary>

Keep the old version able to read transitional writes or separate the format rollout from feature enablement. If old readers cannot understand the new format, plan a forward fix or a tested conversion; disabling a flag does not undo stored data.

</details>

## Official references

- [Google SRE: Service level objectives](https://sre.google/sre-book/service-level-objectives/)
- [Google SRE: Addressing cascading failures](https://sre.google/sre-book/addressing-cascading-failures/)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
