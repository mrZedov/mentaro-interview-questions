# Node.js interview questions

[Explore this topic on Mentaro](https://www.mentaro.app/interview-questions/nodejs) · [Try the Node.js practice demo](https://www.mentaro.app/demo?topic=node-js&utm_source=github)

## Foundations

### 1. How can one Node.js process serve multiple requests if JavaScript runs on one thread by default?

<details>
<summary>Show answer</summary>

The event loop runs one JavaScript callback at a time, but the process can have many I/O operations outstanding. Network I/O is generally coordinated with the operating system; some work, such as asynchronous file operations, uses the libuv worker pool. When work is ready, its callback can run on the event-loop thread. A long JavaScript callback still delays other requests.

**Explanation:** Concurrency does not mean that two handler callbacks execute JavaScript at once. If request A is waiting on a socket, request B can make progress while A is suspended. Conversely, an expensive synchronous parse blocks both the next handler and callbacks for I/O that has already finished.

</details>

[Try the Node.js practice demo](https://www.mentaro.app/demo?topic=node-js&utm_source=github)

### 2. Why can a zero-delay timer fire much later than scheduled?

<details>
<summary>Show answer</summary>

A timer delay is a threshold, not a deadline. The callback has to wait for the event loop to reach the timers phase. Synchronous work, a crowded callback queue, or repeatedly scheduling more next-tick callbacks can delay it. Measure the actual elapsed time rather than treating a timer as a precise clock.

**Explanation:** A zero delay expresses eligibility, not priority. Measure actual delay with a monotonic clock when diagnosing a stall. Do not assume the ordering of a zero-delay timer and setImmediate is identical in every scheduling context.

</details>

[Try the Node.js practice demo](https://www.mentaro.app/demo?topic=node-js&utm_source=github)

### 3. Does putting a CPU-heavy loop in an async function make it non-blocking?

<details>
<summary>Show answer</summary>

No. An async function runs its synchronous work on the calling thread. Await can yield while waiting for a promise, but it does not move computation to another thread. Partition short work and yield between chunks, or move sustained computation to a bounded worker pool.

**Explanation:** Calling an async function starts it immediately up to its first suspension. A CPU loop before that point still blocks the current thread. Splitting small work across event-loop turns can improve responsiveness, but parallel execution requires another worker or process.

</details>

[Try the Node.js practice demo](https://www.mentaro.app/demo?topic=node-js&utm_source=github)

### 4. How do you make Node.js treat a file as an ES module or as CommonJS?

<details>
<summary>Show answer</summary>

Use .mjs for an ES module and .cjs for CommonJS. For .js files, set the nearest package.json type explicitly to module or commonjs instead of relying on defaults or syntax detection. ES modules use import and export; CommonJS normally uses require and module.exports. Be explicit at package boundaries to make resolution predictable.

**Explanation:** Explicit extensions are useful during migrations: a package can use type module and still have a .cjs file for an older tool. Test both package entry points and relative imports in the actual runtime, since transpiler shortcuts may not apply to Node.

</details>

[Try the Node.js practice demo](https://www.mentaro.app/demo?topic=node-js&utm_source=github)

### 5. How do you tell whether a function throws synchronously or returns a rejected promise?

<details>
<summary>Show answer</summary>

A normal function can throw before it returns anything. An error thrown inside an async function becomes a rejected promise instead. Use try/catch around await to handle both a synchronous call failure and a rejected promise. A try/catch around a call without await will not catch its later rejection.

**Explanation:** The relevant distinction is whether a promise was returned before the failure. In an async handler, placing both the call and await inside one try block covers synchronous throws and rejected promises without requiring separate catch paths.

</details>

[Try the Node.js practice demo](https://www.mentaro.app/demo?topic=node-js&utm_source=github)

## Applied

### 6. When would you use a worker thread instead of a child process?

<details>
<summary>Show answer</summary>

Use a worker thread for CPU-intensive JavaScript when you want work to stay in the same process, with message passing and optional shared or transferred buffers. Use a child process when you need a separate process boundary or must run a different executable. Reuse workers for frequent tasks: creating one per request adds startup and memory costs.

**Explanation:** A worker has a separate JavaScript isolate but lives in the same process. Transfer an ArrayBuffer if you can give up ownership; cloning can add noticeable cost. A child process is usually a better fit for an external executable or a stronger process boundary.

</details>

[Try the Node.js practice demo](https://www.mentaro.app/demo?topic=node-js&utm_source=github)

### 7. You have 10,000 URLs to fetch. Why is mapping them all into Promise.all a poor default?

<details>
<summary>Show answer</summary>

The fetches begin as the map runs; Promise.all aggregates their results but does not limit concurrency or cancel the others on rejection. Start only a bounded number at a time and replenish the pool as each task settles. Choose the limit using upstream capacity, connection limits and measured latency, and define how retries and partial failures work.

**Explanation:** An aggregate rejection reports one failure early but does not stop requests that already began. Bound starts before promises are created, and define whether the batch should collect per-item failures or stop admission and cancel requests that support cancellation.

</details>

[Try the Node.js practice demo](https://www.mentaro.app/demo?topic=node-js&utm_source=github)

### 8. What does it mean when a Writable stream returns false from write?

<details>
<summary>Show answer</summary>

The chunk was accepted, but the buffer has reached its backpressure threshold. Pause producing new chunks and wait for drain, while handling errors and closure. For stream-to-stream copying, use pipeline where appropriate to coordinate backpressure and errors rather than writing each incoming chunk unconditionally.

**Explanation:** The false result does not mean the chunk was rejected; writing it again duplicates data. Wait until the writable can accept more, while allowing the operation to fail if the stream errors or closes. Pipeline handles this for connected streams.

</details>

[Try the Node.js practice demo](https://www.mentaro.app/demo?topic=node-js&utm_source=github)

### 9. Why isn't racing an HTTP request against a timeout enough to stop the request?

<details>
<summary>Show answer</summary>

Promise.race only settles the outer promise first. It does not stop the losing operation, which may keep using a connection or consuming a response. Pass an AbortSignal to an API that supports cancellation, such as fetch, and propagate it to downstream operations that should stop with the request.

**Explanation:** The client-facing timeout and downstream cleanup are separate. Passing a signal lets fetch cancel the client operation, including response-body consumption, but cannot guarantee that the remote server stopped or reversed a write that it already began.

**Example:**

```javascript
async function getJson(url) {
  const response = await fetch(url, { signal: AbortSignal.timeout(500) });
  if (!response.ok) throw new Error(`HTTP ${response.status}`);
  return response.json();
}
```

</details>

[Try the Node.js practice demo](https://www.mentaro.app/demo?topic=node-js&utm_source=github)

### 10. Can a Node.js server switch to a JSON error response after sending HTTP headers?

<details>
<summary>Show answer</summary>

No. Once the headers are sent, the status and headers cannot be replaced. Validate what you can before committing the response. After commitment, use the protocol's defined in-band error representation if one exists, or end the failing transfer and log the cause. Do not append arbitrary JSON to a partially sent success body.

**Explanation:** Consider a file download that fails halfway: a JSON error would become extra bytes in the file. Prefer validating prerequisites before sending headers, then let the client detect a truncated transfer if no in-band recovery mechanism exists.

</details>

[Try the Node.js practice demo](https://www.mentaro.app/demo?topic=node-js&utm_source=github)

### 11. Why might error logs refer to the wrong request in a busy Node.js service?

<details>
<summary>Show answer</summary>

A mutable global request ID is shared by concurrent callbacks. Pass context explicitly or carry it with AsyncLocalStorage, and test boundaries where a library creates its own async resources.

**Explanation:** A log written after await may run after a different request has updated a global ID. Initialize a fresh context at the request entry point. In tests, overlap at least two requests to expose problems that sequential runs hide.

</details>

[Try the Node.js practice demo](https://www.mentaro.app/demo?topic=node-js&utm_source=github)

## Advanced scenarios

### 12. Request p99 rises across unrelated endpoints. How do you check whether the event loop is responsible?

<details>
<summary>Show answer</summary>

First measure event-loop delay and CPU together over the affected period. High delay with CPU pressure suggests profiling synchronous work such as JSON parsing or a hot callback. If delay is low, inspect upstream latency, connection-pool wait and other queues. Correlate measurements by instance so a healthy process does not hide a stalled one.

**Explanation:** Compare delay, CPU and dependency timings on the same instances and time window. A short synchronous hotspot may inflate tail latency without dominating an average CPU graph. Profile during a representative incident or reproduction, not only after recovery.

</details>

[Try the Node.js practice demo](https://www.mentaro.app/demo?topic=node-js&utm_source=github)

### 13. RSS keeps growing, but V8 heap usage is flat. What do you investigate next?

<details>
<summary>Show answer</summary>

RSS includes more than the JavaScript heap. Look at process.memoryUsage values for external and arrayBuffers, then inspect buffer retention and native allocations. Also consider allocator fragmentation. Compare equivalent load windows before assuming you have a JavaScript object leak.

**Explanation:** Break down RSS by heap, external memory and operating-system memory observations. ArrayBuffers are included in external memory; do not add both as independent totals. A growing native working set can need a different profiler than a JavaScript heap leak.

</details>

[Try the Node.js practice demo](https://www.mentaro.app/demo?topic=node-js&utm_source=github)

### 14. Why can filesystem latency rise while event-loop delay remains low?

<details>
<summary>Show answer</summary>

Many asynchronous filesystem operations use libuv's worker pool, which they share with work such as some crypto and compression operations. They can queue behind other tasks even while the event loop is responsive. Measure disk latency and competing work, then try bounding that workload before changing the pool size.

**Explanation:** Compare CPU work in the shared pool with device latency. A queued file operation is slow from the caller’s perspective even if its actual disk service time is short. A larger pool may just shift saturation to the disk or CPU.

</details>

[Try the Node.js practice demo](https://www.mentaro.app/demo?topic=node-js&utm_source=github)

### 15. How would you shut down a Node.js HTTP service during a rolling deploy?

<details>
<summary>Show answer</summary>

On the shutdown signal, mark the instance unready, stop accepting new requests and let in-flight work finish within a deadline. Stop background consumers as appropriate and close outbound resources after draining. Define what to do with long-lived or upgraded connections separately. Use an enforced deadline so one stalled request cannot hang deployment.

**Explanation:** Readiness changes may take time to propagate. Design shutdown to tolerate a few late requests, handle upgraded connections separately, and ensure the application’s drain deadline fits inside the orchestrator’s termination grace period.

</details>

[Try the Node.js practice demo](https://www.mentaro.app/demo?topic=node-js&utm_source=github)

### 16. A cache keyed by request parameters grows all day. How do you test whether it is retaining memory?

<details>
<summary>Show answer</summary>

Track entry count and retained heap after comparable traffic and idle periods. If both rise with unique keys, inspect what keeps the entries reachable. Use a bounded cache with an eviction policy and verify under sustained load that memory plateaus. A TTL alone does not guarantee a size bound when new keys arrive quickly.

**Explanation:** Replay a workload that steadily introduces new keys, then observe both entry count and heap after activity subsides. A maximum size bounds retention even when expiration has not caught up; check the hit-rate cost of that bound.

</details>

[Try the Node.js practice demo](https://www.mentaro.app/demo?topic=node-js&utm_source=github)

### 17. Why can several independent Node.js processes report different in-memory cache contents?

<details>
<summary>Show answer</summary>

Each process has its own address space. In-memory invalidation affects only the process receiving it. Decide whether bounded staleness is acceptable or use a shared cache or an explicit invalidation mechanism.

**Explanation:** A worker that receives an invalidation cannot directly mutate another process’s map. Decide how stale a value may be, including after a subscriber restarts, and use expiration or reconciliation to recover from missed invalidation messages.

</details>

[Try the Node.js practice demo](https://www.mentaro.app/demo?topic=node-js&utm_source=github)

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
