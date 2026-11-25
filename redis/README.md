# Redis interview questions

[Explore this topic on Mentaro](https://www.mentaro.app/interview-questions/redis) · [Try the Redis practice demo](https://www.mentaro.app/demo?topic=redis&utm_source=github)

## Foundations

### 1. When would you use a sorted set rather than a set?

<details>
<summary>Show answer</summary>

Use a sorted set when members need a score and range queries by rank or score. A regular set is simpler for unordered membership and set algebra.

**Explanation:** A leaderboard needs to retrieve members by rank or score, so the score is part of the data model. For membership checks and set intersection, use a regular set and avoid keeping an ordering you never read.

</details>

[Try the Redis practice demo](https://www.mentaro.app/demo?topic=redis&utm_source=github)

### 2. How does expiration differ from eviction?

<details>
<summary>Show answer</summary>

Expiration removes keys after their TTL. Eviction removes eligible keys under memory pressure according to the policy, which means a key can disappear before its TTL.

**Explanation:** If an entry disappears before its TTL, check the eviction policy and counters. A cache can tolerate that condition; a system that treats the key as durable state cannot assume the TTL guarantees a minimum lifetime.

</details>

[Try the Redis practice demo](https://www.mentaro.app/demo?topic=redis&utm_source=github)

### 3. Does a normal SET preserve an existing TTL?

<details>
<summary>Show answer</summary>

Replacing a value with SET normally clears its TTL unless you use an option such as KEEPTTL or set a new expiry. Include expiry in the write when it is a business requirement.

**Explanation:** Treat expiry as part of the write contract. Refreshing a cache entry and updating it without extending its lifetime are different operations. A TTL of -1 means the key exists without expiry; -2 means it does not exist.

**Example:**

```text
SET example value EX 60
SET example replacement KEEPTTL
TTL example
SET example replacement
TTL example
```

If run immediately, the first TTL is close to 60 seconds; the last is -1 because the ordinary SET removed the expiry.

</details>

[Try the Redis practice demo](https://www.mentaro.app/demo?topic=redis&utm_source=github)

### 4. How does a hash differ from storing a JSON string under one key?

<details>
<summary>Show answer</summary>

A hash lets clients read and update named fields independently. A JSON string is an opaque string to ordinary Redis commands and usually requires replacing the serialized value.

**Explanation:** An account with independently updated flags can use HSET without overwriting unrelated fields. A serialized blob may be simpler for whole-document reads, but a client-side read/modify/write on it needs concurrency control.

</details>

[Try the Redis practice demo](https://www.mentaro.app/demo?topic=redis&utm_source=github)

### 5. What does pipelining improve?

<details>
<summary>Show answer</summary>

It reduces network round trips by sending many commands without waiting after each one. It does not make the commands an atomic group.

**Explanation:** Sending 100 independent commands one at a time pays up to 100 round trips. Batching them saves network waits, but each command still runs and returns separately. Bound batch size so buffering does not dominate memory.

</details>

[Try the Redis practice demo](https://www.mentaro.app/demo?topic=redis&utm_source=github)

## Applied

### 6. How does WATCH help with a read-modify-write sequence?

<details>
<summary>Show answer</summary>

It makes EXEC conditional on the watched keys remaining unchanged. If EXEC aborts, reread and retry within a bound; watching does not prevent other clients from writing.

**Explanation:** Watch before reading the value, then stage the write in MULTI and check the EXEC result. If a competing change invalidates the transaction, recompute from fresh state; retrying only the same derived write can preserve a stale decision.

</details>

[Try the Redis practice demo](https://www.mentaro.app/demo?topic=redis&utm_source=github)

### 7. What does a Streams consumer group track?

<details>
<summary>Show answer</summary>

It distributes deliveries among consumers and records pending entries until acknowledgement. Inspect and reclaim abandoned entries and make handlers tolerate redelivery.

**Explanation:** A delivered message can be pending under a dead consumer even when no new deliveries remain. Track pending age and reclaim abandoned work. If processing finished just before a crash, a second consumer may see the message again.

</details>

[Try the Redis practice demo](https://www.mentaro.app/demo?topic=redis&utm_source=github)

### 8. How do RDB snapshots and AOF differ when recovering after a crash?

<details>
<summary>Show answer</summary>

RDB captures point-in-time state; later changes can be lost. AOF replays recorded writes, with the possible loss window determined by its sync policy. Test restoration for either mode.

**Explanation:** Estimate recovery against actual write rate rather than just configuration names. Snapshots bound the recoverable point by their cadence; for an append-only log, sync behavior and storage failures affect durability. Time the restore as well as checking data loss.

</details>

[Try the Redis practice demo](https://www.mentaro.app/demo?topic=redis&utm_source=github)

### 9. Why can two Redis Cluster keys fail in one multi-key command?

<details>
<summary>Show answer</summary>

Many multi-key operations require all keys to be in the same hash slot. A shared hash tag can colocate related keys, but too much colocation can create hot slots.

**Explanation:** Keys such as cart:{42}:items and cart:{42}:totals share a hash tag. Choose the tag around the operations that must be colocated; using a single tag for all customers would defeat distribution.

</details>

[Try the Redis practice demo](https://www.mentaro.app/demo?topic=redis&utm_source=github)

### 10. Why might removing a very large Redis key increase latency for other requests?

<details>
<summary>Show answer</summary>

Freeing a large value synchronously can take time on the command thread. Consider UNLINK when asynchronous reclamation is appropriate and monitor memory until reclamation completes.

**Explanation:** UNLINK can remove the key from lookup before reclaiming its allocations in the background. It does not guarantee an instant drop in RSS. Confirm whether removal latency or memory recovery time is your actual constraint.

</details>

[Try the Redis practice demo](https://www.mentaro.app/demo?topic=redis&utm_source=github)

## Advanced scenarios

### 11. What causes a hot key when total cache usage appears healthy?

<details>
<summary>Show answer</summary>

A single popular key can overload the shard or network connection that serves it. Measure skew by key pattern or shard, then consider request coalescing or a bounded local cache where freshness permits.

**Explanation:** An evenly distributed key space does not distribute access to one celebrity profile. Replicating or caching reads can lower pressure, but evaluate freshness and whether the real load is a write hotspot that readers cannot absorb.

</details>

[Try the Redis practice demo](https://www.mentaro.app/demo?topic=redis&utm_source=github)

### 12. Can asynchronously replicated Redis lose an acknowledged write after failover?

<details>
<summary>Show answer</summary>

Yes, the promoted replica may not have received the write. When the loss is unacceptable, treat this as an architecture requirement rather than assuming ordinary acknowledgement provides durable consensus.

**Explanation:** An immediate success response is not the same thing as a durable majority decision. After promotion, a client may find that its previous update is absent. Decide which state can be reconstructed and which must live in a store with stronger guarantees.

</details>

[Try the Redis practice demo](https://www.mentaro.app/demo?topic=redis&utm_source=github)

### 13. What do you measure before increasing Redis maxmemory?

<details>
<summary>Show answer</summary>

Separate evictions, object size, fragmentation, replica buffers and process RSS. Increasing the configured limit can move the failure to host memory without addressing command latency or key growth.

**Explanation:** Redis eviction accounting and process RSS are not interchangeable. If fragmentation or replication buffers explain the gap, changing maxmemory does not solve all memory pressure. Leave host headroom and check how eviction affects hit rate.

</details>

[Try the Redis practice demo](https://www.mentaro.app/demo?topic=redis&utm_source=github)

### 14. How should a service behave when its Redis cache is unavailable?

<details>
<summary>Show answer</summary>

If the authoritative store can serve the request, use bounded fallback and load shedding to protect it. If Redis stores essential coordination state, fail according to that contract rather than assuming it is safe to bypass.

**Explanation:** If every miss falls back to the database, a cache outage may become a database outage. Bound fallback concurrency and reject or serve safely stale data where the product allows; test cold recovery under load.

</details>

[Try the Redis practice demo](https://www.mentaro.app/demo?topic=redis&utm_source=github)

### 15. Why is INCR followed by EXPIRE not automatically a reliable counter with a lifetime?

<details>
<summary>Show answer</summary>

The increment may succeed and the process may fail before setting the expiry. Use a reviewed server-side operation for conditional expiry or an atomic transaction when expiry should be set on every update. Test retries and the intended window semantics.

**Explanation:** For a fixed window, set expiry when initializing the counter rather than sliding it on every increment. Make the update and expiry decision one operation, and decide separately how a retry after an uncertain response should be counted.

</details>

[Try the Redis practice demo](https://www.mentaro.app/demo?topic=redis&utm_source=github)

## Official references

- [Redis: Data types](https://redis.io/docs/latest/develop/data-types/)
- [Redis: Persistence](https://redis.io/docs/latest/operate/oss_and_stack/management/persistence/)
- [Redis: Key eviction](https://redis.io/docs/latest/develop/reference/eviction/)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
