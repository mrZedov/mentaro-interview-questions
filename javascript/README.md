# JavaScript interview questions

[Explore this topic on Mentaro](https://www.mentaro.app/interview-questions/javascript) · [Start interactive practice on Mentaro](https://www.mentaro.app/login?mode=signup&redirect=/onboarding)

## Foundations

### 1. How can a const object still change?

<details>
<summary>Show answer</summary>

Const prevents reassignment of the variable binding, not mutation of the object. Object.freeze is a separate operation and is shallow unless nested objects are also frozen.

</details>

### 2. When does an arrow function get its this value?

<details>
<summary>Show answer</summary>

It captures this from the surrounding scope. Calling it as an object property or using call does not give it a new receiver, unlike an ordinary unbound method.

</details>

### 3. What does a closure retain after its outer function returns?

<details>
<summary>Show answer</summary>

It retains access to captured lexical bindings. It is not necessarily a snapshot of their values, so later changes to a shared binding can be visible.

</details>

### 4. What is the difference between ?? and || for defaults?

<details>
<summary>Show answer</summary>

The nullish operator falls back only for null and undefined. The logical OR operator also falls back for falsy values such as zero, false and the empty string.

</details>

### 5. Why can two Date objects with the same timestamp fail strict equality?

<details>
<summary>Show answer</summary>

Object equality by === compares identity. Compare the numeric timestamps when the intent is to compare instants.

</details>

## Applied

### 6. Why can invoking a stored reference to a method lose its receiver?

<details>
<summary>Show answer</summary>

An ordinary function's this depends on how it is called. Extracting obj.method and invoking it separately loses the object receiver; bind it or call it through a wrapper.

</details>

### 7. Does object spread make a deep copy?

<details>
<summary>Show answer</summary>

No. It copies own enumerable properties into a new outer object, but nested object values still refer to the same objects. Copy the nested paths you intend to change.

</details>

### 8. What does a browser run first after the current task: a queued microtask or a timer callback?

<details>
<summary>Show answer</summary>

The browser drains microtasks at the checkpoint before moving on to another task. A timer callback is a separate task, even with zero delay.

</details>

### 9. What happens if a callback passed to then throws?

<details>
<summary>Show answer</summary>

The promise returned by that then call rejects with the thrown error. Return or await the chain so the consumer can observe the failure.

</details>

### 10. Does Array.forEach wait for an async callback to finish?

<details>
<summary>Show answer</summary>

No. Its iteration is synchronous and it discards callback return values. Await a for-of loop for sequential work or an explicitly bounded pool for parallel work.

</details>

### 11. Why can an array hole behave differently from an explicit undefined?

<details>
<summary>Show answer</summary>

A hole has no own element at that index. Methods such as map skip absent indices under ordinary conditions, while the array iterator reads them as undefined. Use explicit entries when consistent iteration matters.

</details>

### 12. When does a WeakMap help associate metadata with objects?

<details>
<summary>Show answer</summary>

A WeakMap lets the metadata follow the lifetime of its object key without keeping that key alive solely through the map. It is not enumerable and does not offer predictable eviction timing or a size limit.

</details>

## Advanced scenarios

### 13. How do you prevent a slow autocomplete response from replacing newer results?

<details>
<summary>Show answer</summary>

Track the latest request or generation and apply only its result. Abort prior requests where possible, but retain the generation check because cancellation can race with a response.

</details>

### 14. How can an event listener cause a detached element to remain in memory?

<details>
<summary>Show answer</summary>

A listener attached to a long-lived object can close over the element. Dispose of the listener when the component unmounts and inspect the heap's retaining paths to confirm the leak.

</details>

### 15. Why is sorting state in place risky in a reactive application?

<details>
<summary>Show answer</summary>

Array.sort mutates the original array, so code sharing the reference sees unexpected changes. Return a new array when deriving a sorted view, using toSorted where supported.

</details>

### 16. How would you keep a browser responsive while processing a large local dataset?

<details>
<summary>Show answer</summary>

Split work into tasks that yield to rendering, or move sustained computation to a worker. Consider serialization and memory costs, and measure long tasks rather than assuming smaller chunks are enough.

</details>

### 17. How do you parse an optional user-entered count without turning a blank into zero?

<details>
<summary>Show answer</summary>

Distinguish blank input before numeric conversion, then check the resulting number is finite and an integer within range. A coercion result alone cannot tell you what the user intended.

</details>

## Official references

- [MDN: JavaScript Guide](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide)
- [WHATWG: Event loops](https://html.spec.whatwg.org/multipage/webappapis.html#event-loops)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
