# Python interview questions

[Explore this topic on Mentaro](https://www.mentaro.app/interview-questions/python) · [Start interactive practice on Mentaro](https://www.mentaro.app/login?mode=signup&redirect=/onboarding)

## Foundations

### 1. Does assigning one list variable to another copy the list?

<details>
<summary>Show answer</summary>

No. Assignment binds the new name to the same object. Mutating through either reference affects both; rebinding one name does not.

**Explanation:** Assignment binds a second name to the same list. Use a shallow copy when you only want independent list structure; nested mutable items stay shared unless copied too.

</details>

### 2. When should you use is rather than ==?

<details>
<summary>Show answer</summary>

Is checks identity, as in value is None. Equality compares values according to the object's equality implementation. Do not rely on identity for equal numbers or strings.

**Explanation:** Use identity for a unique sentinel such as None or a private object. Use equality for values like integers or strings; interning can make identity comparisons appear to work in tests without being a valid value comparison.

</details>

### 3. Why does a list used as a parameter default retain earlier calls' data?

<details>
<summary>Show answer</summary>

The default is evaluated once when the function is defined. Later calls without that argument reuse the object. Use a sentinel such as None and create a fresh list within the call.

**Explanation:** The same list is reused only when the caller omits the argument. Creating the list inside the function avoids sharing between omitted calls, but mutating a list explicitly supplied by the caller is still observable. Copy it if your API promises not to mutate caller-owned data.

**Example:**

```python
def add(value, items=None):
    if items is None:
        items = []
    items.append(value)
    return items

assert add("a") == ["a"]
assert add("b") == ["b"]
```

</details>

### 4. What makes a value eligible as a dictionary key?

<details>
<summary>Show answer</summary>

A key must be hashable: its hash must remain stable and equal keys must have equal hashes. A tuple is hashable only when its elements are hashable.

**Explanation:** A hashable object needs a stable hash, and objects that compare equal must hash equally. A tuple containing a list is not hashable just because the tuple itself is immutable. Prefer an immutable identity value for dictionary keys.

</details>

### 5. What is the difference between an iterable and an iterator?

<details>
<summary>Show answer</summary>

An iterable can supply an iterator; an iterator itself remembers position and yields items with next until exhaustion. Not every iterable can be traversed a second time.

**Explanation:** A list can produce multiple independent iterators. An iterator keeps traversal state and is usually exhausted after one pass. If a function needs two passes, document it or redesign it to aggregate in one pass.

</details>

### 6. What does a Python finally block guarantee when the try block returns?

<details>
<summary>Show answer</summary>

Under normal control flow, finally runs before the caller receives the result. A return or exception in finally can replace the pending result or exception, so use it for cleanup rather than returning from it.

**Explanation:** The return value is determined before the finally suite runs, but finally may still modify a returned mutable object before the caller sees it. Keep cleanup separate from business return logic and do not return from finally.

</details>

## Applied

### 7. Why does copying a nested dictionary with dict() not isolate nested changes?

<details>
<summary>Show answer</summary>

It copies the outer mapping but retains references to the original values. Copy the nested objects you intend to mutate; use a deep copy only when duplicating the full object graph is appropriate.

**Explanation:** A shallow copy produces a new outer mapping while preserving references to its values. Copy the specific nested branch you will change, or choose deep copy only if its behavior matches the object graph and ownership you intend.

</details>

### 8. How does a generator help process a file larger than memory?

<details>
<summary>Show answer</summary>

It can yield one item at a time instead of retaining the whole file. Keep the file open for the duration of iteration, and avoid downstream operations such as list() that re-materialize all records.

**Explanation:** Laziness only helps when the rest of the pipeline streams too: collecting the generator into a list recreates the memory problem. Open the file with a context manager and reduce the stream to the result you actually need.

**Example:**

```python
def count_errors(path):
    with open(path, encoding="utf-8") as lines:
        return sum(1 for line in lines if line.startswith("ERROR "))
```

</details>

### 9. How can a context manager preserve cleanup when parsing throws?

<details>
<summary>Show answer</summary>

The with statement invokes exit handling once entry succeeds, whether the block completes or raises. A context manager can close a file there; suppressing an exception should be a separate deliberate decision.

**Explanation:** A context manager establishes a cleanup boundary even when parsing raises. The context manager can deliberately suppress an error, but resource-only cleanup should usually let the original exception propagate.

</details>

### 10. Do type annotations validate values passed by a caller?

<details>
<summary>Show answer</summary>

Not by themselves. A static checker can find inconsistent calls in code, but an untrusted JSON payload needs runtime validation. Return a precisely typed value after checking it.

**Explanation:** Annotations help tools and readers, but a call with the wrong runtime value still enters an ordinary annotated function. Add validation at untrusted boundaries rather than repeating it everywhere after successful parsing.

</details>

### 11. Why do callbacks created in a loop sometimes all use the last loop value?

<details>
<summary>Show answer</summary>

A closure resolves the same outer variable when called, after the loop may have changed it. Bind the current value in a factory or by a per-function default argument.

**Explanation:** Closures resolve the shared loop variable when called, after the loop may have advanced. A default argument captures the current object reference at function definition, not a deep copy of the object.

**Example:**

```python
callbacks = [lambda value=i: value for i in range(3)]
assert [callback() for callback in callbacks] == [0, 1, 2]
```

</details>

## Advanced scenarios

### 12. When would you use processes rather than threads for Python work?

<details>
<summary>Show answer</summary>

In conventional GIL-enabled CPython, processes can run CPU-bound Python bytecode in parallel, at the cost of startup and data transfer. Threads often work well for blocking I/O; check the runtime and measure the actual workload.

**Explanation:** For typical GIL-enabled CPython, processes can parallelize pure Python computation; threads can still be useful for I/O and native work that releases the GIL. Include process startup, serialization and memory costs when measuring the crossover.

</details>

### 13. Why does an async handler stall its neighbors during a blocking call?

<details>
<summary>Show answer</summary>

The event loop thread cannot run other tasks until the call returns. Use an asynchronous library or offload blocking I/O to a bounded executor instead of assuming async def makes synchronous code non-blocking.

**Explanation:** Putting a blocking client call inside async def does not make that client asynchronous. Replace it with an async implementation or move bounded blocking work off the event loop, with timeout and cancellation behavior spelled out.

</details>

### 14. How do you diagnose slow membership checks over a growing list?

<details>
<summary>Show answer</summary>

Profile first. List membership scans elements; a set is usually faster for repeated lookups of hashable values. Account for memory, construction cost and whether duplicates or ordering matter.

**Explanation:** Benchmark representative hit and miss rates at increasing list sizes. A set trades memory and construction time for average constant-time lookup, so it helps where there are enough repeated queries and hashable items.

</details>

### 15. Why should you catch a specific exception when reading a configuration file?

<details>
<summary>Show answer</summary>

A missing file may justify a documented default; malformed content or denied access should not silently take the same path. Catch the expected failure near the operation and preserve useful context for other errors.

**Explanation:** A missing file might justify using defaults, while a permission failure or malformed file should usually be visible. Keep the try block narrow so that a failure later in parsing is not accidentally reported as a missing configuration file.

</details>

### 16. How do you stop one large input from exhausting memory in a worker?

<details>
<summary>Show answer</summary>

Set explicit size limits and process records in bounded batches or streams. Measure retained objects and resident memory under representative load so buffers and queued work are included.

**Explanation:** An upload-size limit is insufficient for a compressed input that expands dramatically. Bound decoded size and the number of records as well, and stream or reject before allocating an unbounded collection.

</details>

### 17. What happens if an asyncio task swallows cancellation?

<details>
<summary>Show answer</summary>

Structured concurrency and timeout machinery may not finish as intended. Use try/finally for cleanup and normally let CancelledError propagate. If cancellation suppression is intentional, make it part of the contract and test it.

**Explanation:** A task group waits for its children to finish. A task that catches cancellation and continues indefinitely can delay the group exit. Use finally to release resources and propagate cancellation unless the enclosing contract explicitly requires suppression.

</details>

## Official references

- [Python: Data model](https://docs.python.org/3/reference/datamodel.html)
- [Python: Concurrent execution](https://docs.python.org/3/library/concurrency.html)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
