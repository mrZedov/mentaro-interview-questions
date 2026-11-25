# Node.js interview questions

[Explore this topic on Mentaro](https://www.mentaro.app/interview-questions/nodejs) · [Start interactive practice on Mentaro](https://www.mentaro.app/login?mode=signup&redirect=/onboarding)

## Foundations

### 1. How can one Node.js process serve multiple requests if JavaScript runs on one thread by default?

<details>
<summary>Show answer</summary>

The event loop runs one JavaScript callback at a time, but the process can have many I/O operations outstanding. Network I/O is generally coordinated with the operating system; some work, such as asynchronous file operations, uses the libuv worker pool. When work is ready, its callback can run on the event-loop thread. A long JavaScript callback still delays other requests.

</details>

### 2. Why can a zero-delay timer fire much later than scheduled?

<details>
<summary>Show answer</summary>

A timer delay is a threshold, not a deadline. The callback has to wait for the event loop to reach the timers phase. Synchronous work, a crowded callback queue, or repeatedly scheduling more next-tick callbacks can delay it. Measure the actual elapsed time rather than treating a timer as a precise clock.

</details>

### 3. Does putting a CPU-heavy loop in an async function make it non-blocking?

<details>
<summary>Show answer</summary>

No. An async function runs its synchronous work on the calling thread. Await can yield while waiting for a promise, but it does not move computation to another thread. Partition short work and yield between chunks, or move sustained computation to a bounded worker pool.

</details>

### 4. How do you make Node.js treat a file as an ES module or as CommonJS?

<details>
<summary>Show answer</summary>

Use .mjs for an ES module and .cjs for CommonJS. For .js files, set the nearest package.json type explicitly to module or commonjs instead of relying on defaults or syntax detection. ES modules use import and export; CommonJS normally uses require and module.exports. Be explicit at package boundaries to make resolution predictable.

</details>

### 5. How do you tell whether a function throws synchronously or returns a rejected promise?

<details>
<summary>Show answer</summary>

A normal function can throw before it returns anything. An error thrown inside an async function becomes a rejected promise instead. Use try/catch around await to handle both a synchronous call failure and a rejected promise. A try/catch around a call without await will not catch its later rejection.

</details>

## Applied

### 6. When would you use a worker thread instead of a child process?

<details>
<summary>Show answer</summary>

Use a worker thread for CPU-intensive JavaScript when you want work to stay in the same process, with message passing and optional shared or transferred buffers. Use a child process when you need a separate process boundary or must run a different executable. Reuse workers for frequent tasks: creating one per request adds startup and memory costs.

</details>

### 7. You have 10,000 URLs to fetch. Why is mapping them all into Promise.all a poor default?

<details>
<summary>Show answer</summary>

The fetches begin as the map runs; Promise.all aggregates their results but does not limit concurrency or cancel the others on rejection. Start only a bounded number at a time and replenish the pool as each task settles. Choose the limit using upstream capacity, connection limits and measured latency, and define how retries and partial failures work.

</details>

### 8. What does it mean when a Writable stream returns false from write?

<details>
<summary>Show answer</summary>

The chunk was accepted, but the buffer has reached its backpressure threshold. Pause producing new chunks and wait for drain, while handling errors and closure. For stream-to-stream copying, use pipeline where appropriate to coordinate backpressure and errors rather than writing each incoming chunk unconditionally.

</details>

### 9. Why isn't racing an HTTP request against a timeout enough to stop the request?

<details>
<summary>Show answer</summary>

Promise.race only settles the outer promise first. It does not stop the losing operation, which may keep using a connection or consuming a response. Pass an AbortSignal to an API that supports cancellation, such as fetch, and propagate it to downstream operations that should stop with the request.

</details>

### 10. Can a Node.js server switch to a JSON error response after sending HTTP headers?

<details>
<summary>Show answer</summary>

No. Once the headers are sent, the status and headers cannot be replaced. Validate what you can before committing the response. After commitment, use the protocol's defined in-band error representation if one exists, or end the failing transfer and log the cause. Do not append arbitrary JSON to a partially sent success body.

</details>

### 11. Why might error logs refer to the wrong request in a busy Node.js service?

<details>
<summary>Show answer</summary>

A mutable global request ID is shared by concurrent callbacks. Pass context explicitly or carry it with AsyncLocalStorage, and test boundaries where a library creates its own async resources.

</details>

## Advanced scenarios

### 12. Request p99 rises across unrelated endpoints. How do you check whether the event loop is responsible?

<details>
<summary>Show answer</summary>

First measure event-loop delay and CPU together over the affected period. High delay with CPU pressure suggests profiling synchronous work such as JSON parsing or a hot callback. If delay is low, inspect upstream latency, connection-pool wait and other queues. Correlate measurements by instance so a healthy process does not hide a stalled one.

</details>

### 13. RSS keeps growing, but V8 heap usage is flat. What do you investigate next?

<details>
<summary>Show answer</summary>

RSS includes more than the JavaScript heap. Look at process.memoryUsage values for external and arrayBuffers, then inspect buffer retention and native allocations. Also consider allocator fragmentation. Compare equivalent load windows before assuming you have a JavaScript object leak.

</details>

### 14. Why can filesystem latency rise while event-loop delay remains low?

<details>
<summary>Show answer</summary>

Many asynchronous filesystem operations use libuv's worker pool, which they share with work such as some crypto and compression operations. They can queue behind other tasks even while the event loop is responsive. Measure disk latency and competing work, then try bounding that workload before changing the pool size.

</details>

### 15. How would you shut down a Node.js HTTP service during a rolling deploy?

<details>
<summary>Show answer</summary>

On the shutdown signal, mark the instance unready, stop accepting new requests and let in-flight work finish within a deadline. Stop background consumers as appropriate and close outbound resources after draining. Define what to do with long-lived or upgraded connections separately. Use an enforced deadline so one stalled request cannot hang deployment.

</details>

### 16. A cache keyed by request parameters grows all day. How do you test whether it is retaining memory?

<details>
<summary>Show answer</summary>

Track entry count and retained heap after comparable traffic and idle periods. If both rise with unique keys, inspect what keeps the entries reachable. Use a bounded cache with an eviction policy and verify under sustained load that memory plateaus. A TTL alone does not guarantee a size bound when new keys arrive quickly.

</details>

### 17. Why can several independent Node.js processes report different in-memory cache contents?

<details>
<summary>Show answer</summary>

Each process has its own address space. In-memory invalidation affects only the process receiving it. Decide whether bounded staleness is acceptable or use a shared cache or an explicit invalidation mechanism.

</details>

## Official references

- [Node.js: The event loop](https://nodejs.org/en/learn/asynchronous-work/event-loop-timers-and-nexttick)
- [Node.js: Worker threads](https://nodejs.org/api/worker_threads.html)
- [Node.js: Streams](https://nodejs.org/api/stream.html)
- [Node.js: HTTP](https://nodejs.org/api/http.html)
- [Node.js: Performance hooks](https://nodejs.org/api/perf_hooks.html)
- [Node.js: Process memory](https://nodejs.org/api/process.html#processmemoryusage)
- [Node.js: AbortSignal](https://nodejs.org/api/globals.html#class-abortsignal)
- [Node.js: ECMAScript modules](https://nodejs.org/api/esm.html)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
