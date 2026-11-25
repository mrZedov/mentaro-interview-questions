# JavaScript interview questions

[Explore this topic on Mentaro](https://www.mentaro.app/interview-questions/javascript) · [Try the JavaScript practice demo](https://www.mentaro.app/demo?topic=javascript&utm_source=github)

## Foundations

### 1. How can a const object still change?

<details>
<summary>Show answer</summary>

Const prevents reassignment of the variable binding, not mutation of the object. Object.freeze is a separate operation and is shallow unless nested objects are also frozen.

**Explanation:** Separate the variable binding from the object it points to. Two bindings can still reference the same object, so mutating it through either binding is visible through both. Object.freeze only freezes the object passed to it; freezing a nested object graph is a separate decision.

**Example:**

```javascript
const settings = Object.freeze({ ui: { zoom: 1 } });
const alias = settings.ui;
alias.zoom = 2;
console.log(settings.ui.zoom); // 2
```

</details>

### 2. When does an arrow function get its this value?

<details>
<summary>Show answer</summary>

It captures this from the surrounding scope. Calling it as an object property or using call does not give it a new receiver, unlike an ordinary unbound method.

**Explanation:** An arrow created inside an ordinary method can use that method’s receiver later. An arrow placed directly in an object literal does not learn the receiver from the property access.

**Example:**

```javascript
const counter = {
  value: 7,
  reader() { return () => this.value; },
};
console.log(counter.reader()());
```

The result is 7.

</details>

### 3. What does a closure retain after its outer function returns?

<details>
<summary>Show answer</summary>

It retains access to captured lexical bindings. It is not necessarily a snapshot of their values, so later changes to a shared binding can be visible.

**Explanation:** A lexical binding is the storage location the closure reads. Two callbacks created by the same invocation can update and read one shared variable, while separate invocations create independent variables.

**Example:**

```javascript
function makeCounter() {
  let count = 0;
  return () => ++count;
}
const a = makeCounter();
const b = makeCounter();
console.log(a(), a(), b());
```

The result is 1 2 1.

</details>

### 4. What is the difference between ?? and || for defaults?

<details>
<summary>Show answer</summary>

The nullish operator falls back only for null and undefined. The logical OR operator also falls back for falsy values such as zero, false and the empty string.

**Explanation:** Choose based on what counts as missing in the domain. Zero can mean a deliberate retry limit; converting it to a nonzero default changes behavior. Neither operator validates input: parse and check the range separately.

**Example:**

```javascript
const retries = 0;
console.log(retries ?? 3); // 0
console.log(retries || 3); // 3
```

</details>

### 5. Why can two Date objects with the same timestamp fail strict equality?

<details>
<summary>Show answer</summary>

Object equality by === compares identity. Compare the numeric timestamps when the intent is to compare instants.

**Explanation:** Choose the comparison based on meaning: getTime compares instants, while comparing a calendar date requires a timezone or date-only representation. Invalid dates produce NaN from getTime, so validate before treating the result as a timestamp.

</details>

## Applied

### 6. Why can invoking a stored reference to a method lose its receiver?

<details>
<summary>Show answer</summary>

An ordinary function's this depends on how it is called. Extracting obj.method and invoking it separately loses the object receiver; bind it or call it through a wrapper.

**Explanation:** The receiver comes from the call expression, not from where the function was declared. A wrapper can defer the property lookup to invocation time; bind fixes the function and receiver at binding time. This difference matters if a method can be replaced.

</details>

### 7. Does object spread make a deep copy?

<details>
<summary>Show answer</summary>

No. It copies own enumerable properties into a new outer object, but nested object values still refer to the same objects. Copy the nested paths you intend to change.

**Explanation:** Copy the path you intend to mutate and reuse untouched branches when they can remain immutable. Object spread also does not preserve the source prototype, so spreading a class instance is not a general-purpose cloning method.

**Example:**

```javascript
const before = { preferences: { zoom: 1 } };
const after = { ...before, preferences: { ...before.preferences, zoom: 2 } };
console.log(before.preferences.zoom, after.preferences.zoom);
```

</details>

### 8. What does a browser run first after the current task: a queued microtask or a timer callback?

<details>
<summary>Show answer</summary>

The browser drains microtasks at the checkpoint before moving on to another task. A timer callback is a separate task, even with zero delay.

**Explanation:** A microtask queued by another microtask can run in the same checkpoint. Repeating this indefinitely delays timers and opportunities to paint; Promise.resolve does not create a chance for the browser to render.

**Example:**

```javascript
console.log("current");
setTimeout(() => console.log("timer"), 0);
queueMicrotask(() => console.log("microtask"));
```

In a browser, the output is current, microtask, timer.

</details>

### 9. What happens if a callback passed to then throws?

<details>
<summary>Show answer</summary>

The promise returned by that then call rejects with the thrown error. Return or await the chain so the consumer can observe the failure.

**Explanation:** The original promise may already be fulfilled. Attach error handling to the promise returned by then, not just the original promise, if you want to observe failures in the transformation.

**Example:**

```javascript
await Promise.resolve("data")
  .then(() => { throw new Error("invalid"); })
  .catch(error => console.log(error.message));
```

</details>

### 10. Does Array.forEach wait for an async callback to finish?

<details>
<summary>Show answer</summary>

No. Its iteration is synchronous and it discards callback return values. Await a for-of loop for sequential work or an explicitly bounded pool for parallel work.

**Explanation:** The outer loop does not own the promises returned by its callbacks, so awaiting the loop does not wait for the work or propagate callback failures. A for-of loop awaits each item before starting the next; for independent work, use an explicit concurrency limit when the input is large.

**Example:**

```javascript
const completed = [];
async function save(id) {
  await Promise.resolve();
  completed.push(id);
}
for (const id of [1, 2]) {
  await save(id);
}
console.log(completed); // [1, 2]
```

</details>

### 11. Why can an array hole behave differently from an explicit undefined?

<details>
<summary>Show answer</summary>

A hole has no own element at that index. Methods such as map skip absent indices under ordinary conditions, while the array iterator reads them as undefined. Use explicit entries when consistent iteration matters.

**Explanation:** A property-presence test can distinguish a hole from an explicit undefined element. Spread iterates the source and fills holes with explicit undefined; map on an ordinary sparse array preserves the holes.

**Example:**

```javascript
const sparse = Array(1);
console.log(0 in sparse, 0 in [...sparse]);
```

The result is false true.

</details>

### 12. When does a WeakMap help associate metadata with objects?

<details>
<summary>Show answer</summary>

A WeakMap lets the metadata follow the lifetime of its object key without keeping that key alive solely through the map. It is not enumerable and does not offer predictable eviction timing or a size limit.

**Explanation:** Useful metadata might be a memoized measurement for a DOM element. If the task instead requires listing all entries, deterministic eviction or bulk invalidation, use a different structure rather than treating garbage collection as a cache policy.

</details>

## Advanced scenarios

### 13. How do you prevent a slow autocomplete response from replacing newer results?

<details>
<summary>Show answer</summary>

Track the latest request or generation and apply only its result. Abort prior requests where possible, but retain the generation check because cancellation can race with a response.

**Explanation:** Decide whether to apply all state changes, not just the success result. An obsolete request can also clear the loading indicator or replace a new result with an error. Check the generation at each publication point; cancellation is an optimization.

</details>

### 14. How can an event listener cause a detached element to remain in memory?

<details>
<summary>Show answer</summary>

A listener attached to a long-lived object can close over the element. Dispose of the listener when the component unmounts and inspect the heap's retaining paths to confirm the leak.

**Explanation:** Removing a node is not enough if a callback registered on window keeps it reachable. An AbortController is one way to tie the subscription lifetime to a component; inspect a heap snapshot to see whether that listener is the actual retaining path.

</details>

### 15. Why is sorting state in place risky in a reactive application?

<details>
<summary>Show answer</summary>

Array.sort mutates the original array, so code sharing the reference sees unexpected changes. Return a new array when deriving a sorted view, using toSorted where supported.

**Explanation:** A shared reference can make a memoized component miss an update or make another view see unexpectedly reordered data. Copy before sorting if toSorted is unavailable. Neither version clones the element objects.

</details>

### 16. How would you keep a browser responsive while processing a large local dataset?

<details>
<summary>Show answer</summary>

Split work into tasks that yield to rendering, or move sustained computation to a worker. Consider serialization and memory costs, and measure long tasks rather than assuming smaller chunks are enough.

**Explanation:** Start by measuring long tasks and identifying CPU versus parsing and rendering cost. A worker can isolate CPU work, but copying a large payload can itself take time; consider transferable buffers where ownership can move. For main-thread chunking, yield to a task rather than only to a microtask.

</details>

### 17. How do you parse an optional user-entered count without turning a blank into zero?

<details>
<summary>Show answer</summary>

Distinguish blank input before numeric conversion, then check the resulting number is finite and an integer within range. A coercion result alone cannot tell you what the user intended.

**Explanation:** Define the accepted grammar before converting. Number accepts whitespace and forms such as hexadecimal; parseInt can accept a prefix and silently ignore trailing text. For a decimal count, check the text and the safe integer range.

**Example:**

```javascript
function optionalCount(text) {
  const value = text.trim();
  if (value === "") return undefined;
  if (!/^\d+$/.test(value)) throw new Error("Expected decimal digits");
  const count = Number(value);
  if (!Number.isSafeInteger(count)) throw new RangeError("Count is too large");
  return count;
}
```

</details>

## Official references

- [MDN: JavaScript Guide](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide)
- [WHATWG: Event loops](https://html.spec.whatwg.org/multipage/webappapis.html#event-loops)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).

[Try the JavaScript practice demo](https://www.mentaro.app/demo?topic=javascript&utm_source=github)
