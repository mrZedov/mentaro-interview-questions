# Redis interview questions

[Explore this topic on Mentaro](https://www.mentaro.app/interview-questions/redis) · [Start interactive practice on Mentaro](https://www.mentaro.app/login?mode=signup&redirect=/onboarding)

## Foundations

### 1. When would you use a sorted set rather than a set?

<details>
<summary>Show answer</summary>

Use a sorted set when members need a score and range queries by rank or score. A regular set is simpler for unordered membership and set algebra.

</details>

### 2. How does expiration differ from eviction?

<details>
<summary>Show answer</summary>

Expiration removes keys after their TTL. Eviction removes eligible keys under memory pressure according to the policy, which means a key can disappear before its TTL.

</details>

### 3. Does a normal SET preserve an existing TTL?

<details>
<summary>Show answer</summary>

Replacing a value with SET normally clears its TTL unless you use an option such as KEEPTTL or set a new expiry. Include expiry in the write when it is a business requirement.

</details>

### 4. How does a hash differ from storing a JSON string under one key?

<details>
<summary>Show answer</summary>

A hash lets clients read and update named fields independently. A JSON string is an opaque string to ordinary Redis commands and usually requires replacing the serialized value.

</details>

### 5. What does pipelining improve?

<details>
<summary>Show answer</summary>

It reduces network round trips by sending many commands without waiting after each one. It does not make the commands an atomic group.

</details>

## Applied

### 6. How does WATCH help with a read-modify-write sequence?

<details>
<summary>Show answer</summary>

It makes EXEC conditional on the watched keys remaining unchanged. If EXEC aborts, reread and retry within a bound; watching does not prevent other clients from writing.

</details>

### 7. What does a Streams consumer group track?

<details>
<summary>Show answer</summary>

It distributes deliveries among consumers and records pending entries until acknowledgement. Inspect and reclaim abandoned entries and make handlers tolerate redelivery.

</details>

### 8. How do RDB snapshots and AOF differ when recovering after a crash?

<details>
<summary>Show answer</summary>

RDB captures point-in-time state; later changes can be lost. AOF replays recorded writes, with the possible loss window determined by its sync policy. Test restoration for either mode.

</details>

### 9. Why can two Redis Cluster keys fail in one multi-key command?

<details>
<summary>Show answer</summary>

Many multi-key operations require all keys to be in the same hash slot. A shared hash tag can colocate related keys, but too much colocation can create hot slots.

</details>

## Advanced scenarios

### 10. What causes a hot key when total cache usage appears healthy?

<details>
<summary>Show answer</summary>

A single popular key can overload the shard or network connection that serves it. Measure skew by key pattern or shard, then consider request coalescing or a bounded local cache where freshness permits.

</details>

### 11. Can asynchronously replicated Redis lose an acknowledged write after failover?

<details>
<summary>Show answer</summary>

Yes, the promoted replica may not have received the write. When the loss is unacceptable, treat this as an architecture requirement rather than assuming ordinary acknowledgement provides durable consensus.

</details>

### 12. What do you measure before increasing Redis maxmemory?

<details>
<summary>Show answer</summary>

Separate evictions, object size, fragmentation, replica buffers and process RSS. Increasing the configured limit can move the failure to host memory without addressing command latency or key growth.

</details>

### 13. How should a service behave when its Redis cache is unavailable?

<details>
<summary>Show answer</summary>

If the authoritative store can serve the request, use bounded fallback and load shedding to protect it. If Redis stores essential coordination state, fail according to that contract rather than assuming it is safe to bypass.

</details>

## Official references

- [Redis: Data types](https://redis.io/docs/latest/develop/data-types/)
- [Redis: Persistence](https://redis.io/docs/latest/operate/oss_and_stack/management/persistence/)
- [Redis: Key eviction](https://redis.io/docs/latest/develop/reference/eviction/)

Source: [Mentaro](https://www.mentaro.app/interview-questions/redis). Original questions and answers: [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
