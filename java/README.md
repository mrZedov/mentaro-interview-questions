# Java interview questions

[Explore this topic on Mentaro](https://www.mentaro.app/interview-questions/java) · [Start interactive practice on Mentaro](https://www.mentaro.app/login?mode=signup&redirect=/onboarding)

## Foundations

### 1. What contract must equals and hashCode satisfy?

<details>
<summary>Show answer</summary>

Two equal objects must have equal hash codes; unequal objects may collide. Do not mutate equality-relevant fields while an object is used as a hash-map key.

</details>

### 2. When would ArrayList be preferable to LinkedList?

<details>
<summary>Show answer</summary>

ArrayList supports fast indexed access and good locality. LinkedList only makes insertion cheap once you already have the position; finding it can still require a scan.

</details>

### 3. Why is List&lt;Integer&gt; not assignable to List&lt;Number&gt;?

<details>
<summary>Show answer</summary>

If it were, the recipient could insert a Double into a list meant only for integers. Use List&lt;? extends Number&gt; when reading numeric values without adding new ones.

</details>

### 4. What is the practical difference between checked and unchecked exceptions?

<details>
<summary>Show answer</summary>

A checked exception must be caught or declared by callers at compile time. Unchecked exceptions have no such requirement, but still need handling at a boundary where recovery or reporting is meaningful.

</details>

### 5. Does final make a Java object immutable?

<details>
<summary>Show answer</summary>

A final reference cannot be assigned again, but the object may still mutate. Immutable types must also prevent mutation through fields and references they expose.

</details>

## Applied

### 6. Why does volatile not make count++ thread-safe?

<details>
<summary>Show answer</summary>

It establishes visibility and ordering for accesses, not atomicity of the read-modify-write sequence. Use an atomic operation or a lock that covers the whole update.

</details>

### 7. Is get followed by put an atomic operation on ConcurrentHashMap?

<details>
<summary>Show answer</summary>

No. Another thread can act between the calls. Use operations such as putIfAbsent or compute where their atomic single-key semantics match the requirement.

</details>

### 8. What does try-with-resources guarantee when closing also throws?

<details>
<summary>Show answer</summary>

It closes initialized resources in reverse order. If the body already threw, close failures are attached as suppressed exceptions rather than replacing the primary failure.

</details>

### 9. Why should shared mutable state be avoided in a parallel stream?

<details>
<summary>Show answer</summary>

Pipeline operations may run concurrently and in a different order than expected. Use a collector or reduction with appropriate semantics rather than mutating an external list.

</details>

### 10. What does thenCompose do differently from thenApply?

<details>
<summary>Show answer</summary>

ThenApply maps a successful result to a new value. When the mapping starts another completion stage, thenCompose flattens it so callers wait on a single stage for the final value.

</details>

### 11. Why is a mutable object a risky HashMap key?

<details>
<summary>Show answer</summary>

If fields involved in equals or hashCode change after insertion, lookups can hash to a different bucket or compare differently. Use an immutable key or snapshot its stable identity before inserting.

</details>

## Advanced scenarios

### 12. How do you diagnose a Java process whose heap never returns to its old baseline?

<details>
<summary>Show answer</summary>

Compare live memory after equivalent workloads and collections. Inspect retained objects and their paths to roots before increasing heap size; unbounded caches and live listeners are common retention sources.

</details>

### 13. Why can an unbounded executor queue cause high latency without high CPU?

<details>
<summary>Show answer</summary>

Tasks may wait behind blocked workers while the queue grows. Measure queue age and dependency wait, then bound the queue and choose an explicit rejection or backpressure policy.

</details>

### 14. Do virtual threads remove the need to limit database concurrency?

<details>
<summary>Show answer</summary>

No. They reduce the cost of many blocked Java tasks, not the database's capacity or available connections. Bound scarce resources explicitly and measure waits.

</details>

### 15. How do you distinguish lock contention from a CPU bottleneck?

<details>
<summary>Show answer</summary>

Sample thread states and use profiling or Flight Recorder. Many blocked threads with a hot monitor point to contention; high CPU with runnable stacks points toward computation or spin.

</details>

### 16. What should a service do when interrupted while blocking?

<details>
<summary>Show answer</summary>

Propagate InterruptedException when the contract allows it. If translating it, typically restore the interrupt flag and exit or cancel work rather than silently proceeding.

</details>

### 17. How can you cap the time a Java request waits on several concurrent operations?

<details>
<summary>Show answer</summary>

Calculate one deadline from a monotonic time source and give each wait only the remaining budget. Decide what to cancel or ignore after timeout; timing out while waiting for a Future does not by itself stop the task.

</details>

## Official references

- [Oracle: Java Collections API](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/package-summary.html)
- [Oracle: Java concurrency utilities](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/concurrent/package-summary.html)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
