# Java interview questions

[Explore this topic on Mentaro](https://www.mentaro.app/interview-questions/java) · [Try the Java practice demo](https://www.mentaro.app/demo?topic=java&utm_source=github)

## Foundations

### 1. What contract must equals and hashCode satisfy?

<details>
<summary>Show answer</summary>

Two equal objects must have equal hash codes; unequal objects may collide. Do not mutate equality-relevant fields while an object is used as a hash-map key.

**Explanation:** A hash-based collection first uses a hash to find candidate entries and then checks equality. Equal objects must therefore share a hash, but different objects can collide without being equal. Do not mutate fields used for identity while the object is stored as a key.

**Example:**

```java
record UserId(long value) {}

var users = new java.util.HashMap<UserId, String>();
users.put(new UserId(7), "Ada");
System.out.println(users.get(new UserId(7))); // Ada
```

</details>

### 2. When would ArrayList be preferable to LinkedList?

<details>
<summary>Show answer</summary>

ArrayList supports fast indexed access and good locality. LinkedList only makes insertion cheap once you already have the position; finding it can still require a scan.

**Explanation:** Indexing an ArrayList gets the element directly, while a LinkedList may have to traverse many nodes. Even for insertion, a linked list helps only when the position is already available cheaply; locality often favors an array-backed list.

</details>

### 3. Why is List&lt;Integer&gt; not assignable to List&lt;Number&gt;?

<details>
<summary>Show answer</summary>

If it were, the recipient could insert a Double into a list meant only for integers. Use List&lt;? extends Number&gt; when reading numeric values without adding new ones.

**Explanation:** If a List of Integer were assignable to a writable List of Number, the recipient could insert a Double. Use a bounded wildcard to describe a producer or consumer, instead of assuming the list itself is covariant.

</details>

### 4. What is the practical difference between checked and unchecked exceptions?

<details>
<summary>Show answer</summary>

A checked exception must be caught or declared by callers at compile time. Unchecked exceptions have no such requirement, but still need handling at a boundary where recovery or reporting is meaningful.

**Explanation:** Checked exceptions must be caught or declared at compile time; unchecked exceptions do not impose that requirement. Pick the contract based on what callers should handle, not on whether the exception is severe.

</details>

### 5. Does final make a Java object immutable?

<details>
<summary>Show answer</summary>

A final reference cannot be assigned again, but the object may still mutate. Immutable types must also prevent mutation through fields and references they expose.

**Explanation:** A final variable cannot be reassigned, but a final List can still be mutated. For an immutable object, also control access to its mutable components and avoid handing out references that let callers change its state.

</details>

## Applied

### 6. Why does volatile not make count++ thread-safe?

<details>
<summary>Show answer</summary>

It establishes visibility and ordering for accesses, not atomicity of the read-modify-write sequence. Use an atomic operation or a lock that covers the whole update.

**Explanation:** Increment is a read, an addition and a write. Two threads can read the same value before either writes. Use an atomic increment or a lock for the complete operation rather than just making one field volatile.

</details>

### 7. Is get followed by put an atomic operation on ConcurrentHashMap?

<details>
<summary>Show answer</summary>

No. Another thread can act between the calls. Use operations such as putIfAbsent or compute where their atomic single-key semantics match the requirement.

**Explanation:** Both get and put can be individually thread-safe while the sequence still races. Use a compound operation such as putIfAbsent when that is the intended change. Avoid putting long-running side effects in mapping operations.

</details>

### 8. What does try-with-resources guarantee when closing also throws?

<details>
<summary>Show answer</summary>

It closes initialized resources in reverse order. If the body already threw, close failures are attached as suppressed exceptions rather than replacing the primary failure.

**Explanation:** If the work throws and closing throws as well, the work failure remains primary and the closing failure is suppressed. Inspect suppressed exceptions when diagnosing cleanup problems instead of assuming the close succeeded.

</details>

### 9. Why should shared mutable state be avoided in a parallel stream?

<details>
<summary>Show answer</summary>

Pipeline operations may run concurrently and in a different order than expected. Use a collector or reduction with appropriate semantics rather than mutating an external list.

**Explanation:** Parallel execution can interleave independent element operations. Let a collector handle accumulation according to its contract, or keep the stream sequential if the business operation relies on ordered mutation of shared state.

</details>

### 10. What does thenCompose do differently from thenApply?

<details>
<summary>Show answer</summary>

ThenApply maps a successful result to a new value. When the mapping starts another completion stage, thenCompose flattens it so callers wait on a single stage for the final value.

**Explanation:** If a transformation already returns a CompletionStage, thenApply yields a stage containing another stage. thenCompose flattens that additional asynchronous step so downstream code can await the actual result.

</details>

### 11. Why is a mutable object a risky HashMap key?

<details>
<summary>Show answer</summary>

If fields involved in equals or hashCode change after insertion, lookups can hash to a different bucket or compare differently. Use an immutable key or snapshot its stable identity before inserting.

**Explanation:** If the fields used by hashCode change after insertion, a later lookup can start in another bucket. Treat map keys as stable identifiers; a mutable business entity can be the value rather than the key.

</details>

## Advanced scenarios

### 12. How do you diagnose a Java process whose heap never returns to its old baseline?

<details>
<summary>Show answer</summary>

Compare live memory after equivalent workloads and collections. Inspect retained objects and their paths to roots before increasing heap size; unbounded caches and live listeners are common retention sources.

**Explanation:** Compare memory after comparable GC cycles rather than comparing two arbitrary peaks. Use retained size and reference chains to distinguish a genuine growing owner from caches that warm up and eventually plateau.

</details>

### 13. Why can an unbounded executor queue cause high latency without high CPU?

<details>
<summary>Show answer</summary>

Tasks may wait behind blocked workers while the queue grows. Measure queue age and dependency wait, then bound the queue and choose an explicit rejection or backpressure policy.

**Explanation:** A request may spend most of its latency sitting in the queue, especially when workers are occupied by slow I/O. Measure queue age, cap waiting work and define what rejection or backpressure looks like before tuning thread count.

</details>

### 14. Do virtual threads remove the need to limit database concurrency?

<details>
<summary>Show answer</summary>

No. They reduce the cost of many blocked Java tasks, not the database's capacity or available connections. Bound scarce resources explicitly and measure waits.

**Explanation:** Virtual threads make waiting cheaper for the JVM, not for the database. Put the concurrency limit at the scarce resource and report time spent waiting for admission separately from time spent running the query.

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

[Try the Java practice demo](https://www.mentaro.app/demo?topic=java&utm_source=github)
