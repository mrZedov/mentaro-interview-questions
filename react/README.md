# React interview questions

[Explore this topic on Mentaro](https://www.mentaro.app/interview-questions/react) · [Try the React practice demo](https://www.mentaro.app/demo?topic=react&utm_source=github)

## Foundations

### 1. What is the difference between rendering and committing in React?

<details>
<summary>Show answer</summary>

Rendering calls components to compute UI. Committing applies the resulting changes to the host, such as the browser DOM. A render can be discarded, so it must not have observable side effects.

**Explanation:** Calling a component computes the next UI description; React may discard that work without changing the DOM. Keep rendering pure because a render is not a reliable signal that the user saw a change.

</details>

[Try the React practice demo](https://www.mentaro.app/demo?topic=react&utm_source=github)

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

[Try the React practice demo](https://www.mentaro.app/demo?topic=react&utm_source=github)

### 3. What makes a useful key for a list of editable rows?

<details>
<summary>Show answer</summary>

Use a stable identity from the data, unique among siblings. A positional key can attach the previous row's local state to a different item when rows move.

**Explanation:** With index keys, inserting a row at the top can move an input’s draft to the next record. Use an ID stable across edits and reordering. A freshly generated key on every render resets state instead of preserving it.

</details>

[Try the React practice demo](https://www.mentaro.app/demo?topic=react&utm_source=github)

### 4. What does it mean for an input to be controlled?

<details>
<summary>Show answer</summary>

React state or props supply its current value and an onChange handler updates the source of truth. An uncontrolled input keeps its current value in the DOM and can start with defaultValue.

**Explanation:** A controlled input displays the value provided by React state, so the change handler must update that value. Initialize consistently: switching between undefined and a string can switch the control mode and trigger warnings.

</details>

[Try the React practice demo](https://www.mentaro.app/demo?topic=react&utm_source=github)

### 5. When should state be lifted to a common parent?

<details>
<summary>Show answer</summary>

Lift it when multiple children need to coordinate through one authoritative value. Keep purely local state near its owner instead of making every update flow through a distant component.

**Explanation:** Two sibling controls that must agree should share a state owner. Keeping two copies and synchronizing them through effects introduces drift and extra updates; lift only the shared state, not unrelated local UI details.

</details>

[Try the React practice demo](https://www.mentaro.app/demo?topic=react&utm_source=github)

## Applied

### 6. When is an effect unnecessary for a filtered list?

<details>
<summary>Show answer</summary>

If the list is a pure function of input props and state, compute it during render. An effect that writes derived state adds an update and can briefly show stale data.

**Explanation:** A filtered list is a deterministic view of inputs. Storing it separately and updating it in an effect can commit a stale intermediate render whenever the search term changes. Derive it during render and optimize only if measurement warrants it.

</details>

[Try the React practice demo](https://www.mentaro.app/demo?topic=react&utm_source=github)

### 7. How do you avoid leaking subscriptions when an effect dependency changes?

<details>
<summary>Show answer</summary>

Return cleanup from the effect to unsubscribe the exact listener it registered. React runs cleanup before the next setup for changed dependencies and when removing the component.

**Explanation:** Close over the exact connection created by each setup, then dispose that connection when dependencies change. Test changing the selected channel without unmounting, because cleanup must run on replacement too.

</details>

[Try the React practice demo](https://www.mentaro.app/demo?topic=react&utm_source=github)

### 8. When should you use a ref instead of state?

<details>
<summary>Show answer</summary>

Use a ref for a value that survives renders but does not determine the rendered output, such as a timeout handle. Updating a ref does not schedule a render.

**Explanation:** A timer ID should normally survive renders without triggering another one, so a ref fits. A count displayed on screen needs state. Updating a ref does not ask React to render the new value.

</details>

[Try the React practice demo](https://www.mentaro.app/demo?topic=react&utm_source=github)

### 9. What causes an effect to reconnect on every render?

<details>
<summary>Show answer</summary>

A dependency may be a fresh object or function each time. Move construction inside the effect or depend on the primitive inputs that determine the connection; do not omit a real dependency.

**Explanation:** An object created during every render has a different identity, even if it contains the same fields. Construct configuration inside the effect if only the effect needs it, and depend on the meaningful primitives.

</details>

[Try the React practice demo](https://www.mentaro.app/demo?topic=react&utm_source=github)

### 10. How do you reset a form when the selected account changes?

<details>
<summary>Show answer</summary>

If the account defines a distinct form instance, key the form by account ID. React then creates new state instead of momentarily retaining the prior form's edits.

**Explanation:** Key the form by account ID when all local drafts should reset. If some fields must survive switching accounts, store them at the appropriate owner instead of remounting unrelated components.

</details>

[Try the React practice demo](https://www.mentaro.app/demo?topic=react&utm_source=github)

### 11. When is a reducer useful for a component with several related state variables?

<details>
<summary>Show answer</summary>

A reducer groups state transitions around explicit actions. It can make coupled updates testable without rendering and prevent combinations the UI should not represent. It does not move side effects into the reducer.

**Explanation:** Model a transition such as checkout success in one reducer action so status and receipt cannot drift apart. Trigger network effects outside that reducer and pass the result back as a second action.

</details>

[Try the React practice demo](https://www.mentaro.app/demo?topic=react&utm_source=github)

## Advanced scenarios

### 12. Why can Strict Mode show an extra setup and cleanup during development?

<details>
<summary>Show answer</summary>

It tests whether setup can be undone cleanly. Fix missing cleanup or impure work; the development-only check is not a reason to add an ad hoc run-once flag.

**Explanation:** Development setup → cleanup → setup helps expose subscriptions that cannot be safely restarted. Fix the resource lifecycle instead of using a ref to suppress the second setup, which hides the same problem on real remounts.

</details>

[Try the React practice demo](https://www.mentaro.app/demo?topic=react&utm_source=github)

### 13. Why might memoizing a child fail to reduce its renders?

<details>
<summary>Show answer</summary>

Fresh object or callback props can defeat shallow comparison, and the child's own state or context can still trigger rendering. Profile the component before adding memoization.

**Explanation:** A fresh object or callback can make a memoized child receive different props each render. Check the profiler first: memoization is not a useful fix if the child is already cheap or updates because of its own state or context.

</details>

[Try the React practice demo](https://www.mentaro.app/demo?topic=react&utm_source=github)

### 14. How do you stop obsolete search results replacing current React state?

<details>
<summary>Show answer</summary>

Abort previous requests where possible and ignore outcomes from outdated effects or request generations. Handle errors for the current request separately from expected cancellation.

**Explanation:** An old response can arrive after a new query has already settled. Use cleanup or a generation check on result, error and loading updates. Cancel the transport where possible, but do not rely on cancellation alone.

</details>

[Try the React practice demo](https://www.mentaro.app/demo?topic=react&utm_source=github)

### 15. What does an error boundary not catch by default?

<details>
<summary>Show answer</summary>

It handles rendering failures in descendants, not arbitrary event-handler errors or rejected promises in asynchronous callbacks. Handle those failures at their own boundaries.

**Explanation:** Handle failures in interaction callbacks and asynchronous work at the appropriate boundary. An error boundary is useful for failed rendering of descendants; it is not a global application error handler.

</details>

[Try the React practice demo](https://www.mentaro.app/demo?topic=react&utm_source=github)

### 16. Why can time-dependent rendering cause a hydration mismatch?

<details>
<summary>Show answer</summary>

The initial server HTML and initial client render may use different times or locales. Render shared deterministic data first, then update client-specific values after hydration.

**Explanation:** The first client render must agree with the server HTML. Use the timestamp or locale serialized with the page for that first render, then update after hydration if the browser needs a different value.

</details>

[Try the React practice demo](https://www.mentaro.app/demo?topic=react&utm_source=github)

### 17. Why can updating a widely used context from high-frequency input make typing slow?

<details>
<summary>Show answer</summary>

Consumers that read the context rerender when its value changes. Profile the affected tree, keep rapidly changing state near its consumers, and split independent context values when appropriate.

**Explanation:** Context updates follow consumers, not just immediate children. Profile which consumers rerender as keys are typed, and move high-frequency input state closer to the field rather than memoizing every affected display.

</details>

[Try the React practice demo](https://www.mentaro.app/demo?topic=react&utm_source=github)

## Official references

- [React: Render and commit](https://react.dev/learn/render-and-commit)
- [React: Synchronizing with effects](https://react.dev/learn/synchronizing-with-effects)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
