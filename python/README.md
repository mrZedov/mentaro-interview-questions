# Python interview questions

[Explore this topic on Mentaro](https://www.mentaro.app/interview-questions/python) · [Start interactive practice on Mentaro](https://www.mentaro.app/login?mode=signup&redirect=/onboarding)

## Foundations

### 1. Does assigning one list variable to another copy the list?

<details>
<summary>Show answer</summary>

No. Assignment binds the new name to the same object. Mutating through either reference affects both; rebinding one name does not.

</details>

### 2. When should you use is rather than ==?

<details>
<summary>Show answer</summary>

Is checks identity, as in value is None. Equality compares values according to the object's equality implementation. Do not rely on identity for equal numbers or strings.

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

</details>

### 5. What is the difference between an iterable and an iterator?

<details>
<summary>Show answer</summary>

An iterable can supply an iterator; an iterator itself remembers position and yields items with next until exhaustion. Not every iterable can be traversed a second time.

</details>

### 6. What does a Python finally block guarantee when the try block returns?

<details>
<summary>Show answer</summary>

Under normal control flow, finally runs before the caller receives the result. A return or exception in finally can replace the pending result or exception, so use it for cleanup rather than returning from it.

</details>

## Applied

### 7. Why does copying a nested dictionary with dict() not isolate nested changes?

<details>
<summary>Show answer</summary>

It copies the outer mapping but retains references to the original values. Copy the nested objects you intend to mutate; use a deep copy only when duplicating the full object graph is appropriate.

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

</details>

### 10. Do type annotations validate values passed by a caller?

<details>
<summary>Show answer</summary>

Not by themselves. A static checker can find inconsistent calls in code, but an untrusted JSON payload needs runtime validation. Return a precisely typed value after checking it.

</details>

### 11. Why do callbacks created in a loop sometimes all use the last loop value?

<details>
<summary>Show answer</summary>

A closure resolves the same outer variable when called, after the loop may have changed it. Bind the current value in a factory or by a per-function default argument.

</details>

## Advanced scenarios

### 12. When would you use processes rather than threads for Python work?

<details>
<summary>Show answer</summary>

In conventional GIL-enabled CPython, processes can run CPU-bound Python bytecode in parallel, at the cost of startup and data transfer. Threads often work well for blocking I/O; check the runtime and measure the actual workload.

</details>

### 13. Why does an async handler stall its neighbors during a blocking call?

<details>
<summary>Show answer</summary>

The event loop thread cannot run other tasks until the call returns. Use an asynchronous library or offload blocking I/O to a bounded executor instead of assuming async def makes synchronous code non-blocking.

</details>

### 14. How do you diagnose slow membership checks over a growing list?

<details>
<summary>Show answer</summary>

Profile first. List membership scans elements; a set is usually faster for repeated lookups of hashable values. Account for memory, construction cost and whether duplicates or ordering matter.

</details>

### 15. Why should you catch a specific exception when reading a configuration file?

<details>
<summary>Show answer</summary>

A missing file may justify a documented default; malformed content or denied access should not silently take the same path. Catch the expected failure near the operation and preserve useful context for other errors.

</details>

### 16. How do you stop one large input from exhausting memory in a worker?

<details>
<summary>Show answer</summary>

Set explicit size limits and process records in bounded batches or streams. Measure retained objects and resident memory under representative load so buffers and queued work are included.

</details>

### 17. What happens if an asyncio task swallows cancellation?

<details>
<summary>Show answer</summary>

Structured concurrency and timeout machinery may not finish as intended. Use try/finally for cleanup and normally let CancelledError propagate. If cancellation suppression is intentional, make it part of the contract and test it.

</details>

## Official references

- [Python: Data model](https://docs.python.org/3/reference/datamodel.html)
- [Python: Concurrent execution](https://docs.python.org/3/library/concurrency.html)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
