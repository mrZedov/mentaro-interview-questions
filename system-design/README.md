# System Design interview questions

[Explore this topic on Mentaro](https://www.mentaro.app/interview-questions/system-design)

## Foundations

### 1. What should you clarify before drawing a system diagram?

<details>
<summary>Show answer</summary>

Ask who uses the system, the main operations and their required availability and latency. Identify what must never happen, such as a confirmed reservation being sold twice.

**Explanation:** For a concert booking system, ask whether the goal is browsing, allocating seats or finalizing payments. Overselling is an invariant, while search freshness may be an SLO. Those constraints lead to different consistency and capacity choices.

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

**Explanation:** A partition key sends different records to different groups, while replication gives one group multiple copies. Replicating each partition can improve recovery, but a bad partition key can still funnel most writes into one group.

</details>

### 4. What does a stateless application tier make easier?

<details>
<summary>Show answer</summary>

Any healthy instance can normally serve a request, so scaling and recovery do not depend on a particular process. Durable state still needs an external owner and failure policy.

**Explanation:** Avoid keeping the only copy of a session or workflow in a process that may restart. Stateless application nodes simplify replacement, but the stateful dependencies still need their own scaling and recovery strategy.

</details>

### 5. What does p99 latency tell you that average latency does not?

<details>
<summary>Show answer</summary>

It describes the threshold at or below which about 99% of observations fall. A small fraction of slow requests can be invisible in the average yet affect many real users.

**Explanation:** A p99 of 300 ms does not tell you how slow the worst one percent is. Read it alongside error rate, traffic volume and endpoint breakdowns, and do not average percentiles from different servers to derive a global percentile.

</details>

## Applied

### 6. Why can a well-distributed partition key still create a hotspot?

<details>
<summary>Show answer</summary>

Keys can be evenly distributed while traffic is highly skewed. A popular item can overload the partition that owns it. Consider caching hot reads or splitting writes if the invariant allows it.

**Explanation:** If one key receives most requests, hashing a million keys evenly still sends most of that traffic to one place. Cache hot reads or split the operation only if read freshness and write invariants permit it.

</details>

### 7. How would you provide read-your-writes after moving reads to replicas?

<details>
<summary>Show answer</summary>

Read from the primary temporarily, or use a commit position and wait for a replica that has caught up. Define the fallback if the replica cannot meet the required position within the deadline.

**Explanation:** A commit position returned with a write can let a reader select a replica that has caught up. Put a bound on how long it waits, then fall back to the primary or return an explicit retryable response.

</details>

### 8. When does an asynchronous job queue improve perceived latency?

<details>
<summary>Show answer</summary>

It lets the user receive acceptance without waiting for processing. Expose status and errors separately, and enforce a backlog limit so a fast acceptance response does not hide a very long wait.

**Explanation:** An accepted response can be fast even as the backlog grows. Track the age of queued work and provide an explicit completion state. Reject or defer new work when the completion SLO can no longer be met.

</details>

### 9. How would you limit cache stampedes when a popular entry expires?

<details>
<summary>Show answer</summary>

Coalesce concurrent refreshes, stagger expirations and consider serving stale values if the product permits it. Preserve an upper freshness bound for data on which decisions depend.

**Explanation:** Single-flight cuts work when many requests miss the same key; randomized TTLs help when many keys expire together. Serving stale data can hide refresh latency, but set a hard age limit based on the application’s decisions.

</details>

### 10. When would you keep a materialized read model instead of querying the source of truth directly?

<details>
<summary>Show answer</summary>

Use a projection when read latency or query shape justifies the extra representation. Define acceptable lag, how to rebuild it and what clients see while the projection is stale.

**Explanation:** A projection that joins several tables can make reads cheaper but adds a second observable state. Decide what happens after a write and on a rebuild, and avoid presenting stale results as authoritative for decisions that require fresh data.

</details>

### 11. How do you choose a pagination approach for a feed that changes during browsing?

<details>
<summary>Show answer</summary>

Use a cursor based on a stable total ordering, including a unique tie-breaker. Decide whether browsing should preserve a snapshot or reflect live changes. Test insertions and removals between page requests.

**Explanation:** Offset pagination counts positions in a moving list. A cursor can anchor on the last observed ordering key, but edits to that key can still cause omissions or duplicates; use snapshot semantics when a consistent export matters more than live updates.

</details>

## Advanced scenarios

### 12. What trades off when writes fail over between regions?

<details>
<summary>Show answer</summary>

Synchronous replication can reduce loss at the cost of latency and availability during partitions. Asynchronous replication permits faster local commits but may lose recent writes on failover. Fence the old writer before accepting writes elsewhere.

**Explanation:** Before assuming the other region can take writes, determine whether the old writer is really fenced and whether any acknowledged changes are missing. Test failback and reconciliation as well as the first promotion.

</details>

### 13. How do recovery point and recovery time objectives shape backups?

<details>
<summary>Show answer</summary>

Recovery point is tolerable data loss measured in time; recovery time is how long restoration may take. Backup and log-shipping frequency affect the former, while restore rehearsals and capacity planning establish the latter.

**Explanation:** A five-minute RPO and a one-hour RTO mean you may lose at most five minutes of changes and should restore service within an hour. Verify both by restoring a backup and replaying representative logs under timed conditions.

</details>

### 14. How does fan-out affect latency when a request waits for every dependency?

<details>
<summary>Show answer</summary>

The slowest required response dominates. As fan-out grows, a rare slow dependency becomes more likely to affect any given request. Limit fan-out, use deadlines and make optional results genuinely optional.

**Explanation:** If ten independent calls each have a one percent chance of being slow, the chance that at least one is slow is about 9.6 percent. Correlated failures change the estimate, but the design still needs to decide which calls are necessary and which can degrade.

</details>

### 15. What is load shedding and how do you choose what to shed?

<details>
<summary>Show answer</summary>

Reject or defer work before saturation makes all requests fail. Protect the cheapest or highest-priority critical operations and bound queues. Communicate retry expectations so rejected work does not immediately return.

**Explanation:** Admission control before expensive work can protect capacity for priority requests. Keep the policy explicit and monitor which clients or operations are rejected; unconstrained immediate retries can undo the benefit.

</details>

### 16. How would you prove a design can survive losing its largest node?

<details>
<summary>Show answer</summary>

Check remaining headroom and run a controlled failure test under representative load. Observe failover time, queue growth and data invariants; healthy idle failover does not prove capacity at peak.

**Explanation:** Test the actual failure model at representative traffic and data volume. A healthy standby that can take traffic is not enough if replica lag, capacity or warm-up time prevents it from meeting recovery targets.

</details>

### 17. How would you roll back a feature after it has already written a new data format?

<details>
<summary>Show answer</summary>

Keep the old version able to read transitional writes or separate the format rollout from feature enablement. If old readers cannot understand the new format, plan a forward fix or a tested conversion; disabling a flag does not undo stored data.

**Explanation:** Separate a reversible code rollout from an irreversible data migration. A compatibility reader or versioned format can make a rollback safe. Before enabling new writes, rehearse what the old binary does with a representative new-format record.

</details>

## Official references

- [Google SRE: Service level objectives](https://sre.google/sre-book/service-level-objectives/)
- [Google SRE: Addressing cascading failures](https://sre.google/sre-book/addressing-cascading-failures/)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
