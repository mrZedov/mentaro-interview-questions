# React interview questions

[Explore this topic on Mentaro](https://www.mentaro.app/interview-questions/react) · [Start interactive practice on Mentaro](https://www.mentaro.app/login?mode=signup&redirect=/onboarding)

## Foundations

### 1. What is the difference between rendering and committing in React?

<details>
<summary>Show answer</summary>

Rendering calls components to compute UI. Committing applies the resulting changes to the host, such as the browser DOM. A render can be discarded, so it must not have observable side effects.

</details>

### 2. Why can two setCount(count + 1) calls increment only once?

<details>
<summary>Show answer</summary>

Both use the same count from the current render snapshot and enqueue the same value. Use two functional updaters when each increment must build on the preceding pending state.

**Explanation:** Each direct call reads the same count captured by the current render. Functional updaters run against the previous pending value rather than that captured snapshot. Keep updater functions pure; React may call them more than once in development to detect accidental side effects.

**Example:**

```javascript
function incrementTwice(setCount) {
  setCount(previous => previous + 1);
  setCount(previous => previous + 1);
}
```

</details>

### 3. What makes a useful key for a list of editable rows?

<details>
<summary>Show answer</summary>

Use a stable identity from the data, unique among siblings. A positional key can attach the previous row's local state to a different item when rows move.

</details>

### 4. What does it mean for an input to be controlled?

<details>
<summary>Show answer</summary>

React state or props supply its current value and an onChange handler updates the source of truth. An uncontrolled input keeps its current value in the DOM and can start with defaultValue.

</details>

### 5. When should state be lifted to a common parent?

<details>
<summary>Show answer</summary>

Lift it when multiple children need to coordinate through one authoritative value. Keep purely local state near its owner instead of making every update flow through a distant component.

</details>

## Applied

### 6. When is an effect unnecessary for a filtered list?

<details>
<summary>Show answer</summary>

If the list is a pure function of input props and state, compute it during render. An effect that writes derived state adds an update and can briefly show stale data.

</details>

### 7. How do you avoid leaking subscriptions when an effect dependency changes?

<details>
<summary>Show answer</summary>

Return cleanup from the effect to unsubscribe the exact listener it registered. React runs cleanup before the next setup for changed dependencies and when removing the component.

</details>

### 8. When should you use a ref instead of state?

<details>
<summary>Show answer</summary>

Use a ref for a value that survives renders but does not determine the rendered output, such as a timeout handle. Updating a ref does not schedule a render.

</details>

### 9. What causes an effect to reconnect on every render?

<details>
<summary>Show answer</summary>

A dependency may be a fresh object or function each time. Move construction inside the effect or depend on the primitive inputs that determine the connection; do not omit a real dependency.

</details>

### 10. How do you reset a form when the selected account changes?

<details>
<summary>Show answer</summary>

If the account defines a distinct form instance, key the form by account ID. React then creates new state instead of momentarily retaining the prior form's edits.

</details>

### 11. When is a reducer useful for a component with several related state variables?

<details>
<summary>Show answer</summary>

A reducer groups state transitions around explicit actions. It can make coupled updates testable without rendering and prevent combinations the UI should not represent. It does not move side effects into the reducer.

</details>

## Advanced scenarios

### 12. Why can Strict Mode show an extra setup and cleanup during development?

<details>
<summary>Show answer</summary>

It tests whether setup can be undone cleanly. Fix missing cleanup or impure work; the development-only check is not a reason to add an ad hoc run-once flag.

</details>

### 13. Why might memoizing a child fail to reduce its renders?

<details>
<summary>Show answer</summary>

Fresh object or callback props can defeat shallow comparison, and the child's own state or context can still trigger rendering. Profile the component before adding memoization.

</details>

### 14. How do you stop obsolete search results replacing current React state?

<details>
<summary>Show answer</summary>

Abort previous requests where possible and ignore outcomes from outdated effects or request generations. Handle errors for the current request separately from expected cancellation.

</details>

### 15. What does an error boundary not catch by default?

<details>
<summary>Show answer</summary>

It handles rendering failures in descendants, not arbitrary event-handler errors or rejected promises in asynchronous callbacks. Handle those failures at their own boundaries.

</details>

### 16. Why can time-dependent rendering cause a hydration mismatch?

<details>
<summary>Show answer</summary>

The initial server HTML and initial client render may use different times or locales. Render shared deterministic data first, then update client-specific values after hydration.

</details>

### 17. Why can updating a widely used context from high-frequency input make typing slow?

<details>
<summary>Show answer</summary>

Consumers that read the context rerender when its value changes. Profile the affected tree, keep rapidly changing state near its consumers, and split independent context values when appropriate.

</details>

## Official references

- [React: Render and commit](https://react.dev/learn/render-and-commit)
- [React: Synchronizing with effects](https://react.dev/learn/synchronizing-with-effects)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
