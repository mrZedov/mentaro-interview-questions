# Core software engineering interview questions

[Browse Mentaro’s web question catalog](https://www.mentaro.app/interview-questions) · [Try the Core software engineering practice demo](https://www.mentaro.app/demo?topic=core-engineering&utm_source=github)

## Foundations

### 1. How does an invariant help when implementing a data structure?

<details>
<summary>Show answer</summary>

An invariant describes what must hold before and after operations. For a heap it constrains parent and child ordering; each update has to reestablish it before returning.

**Explanation:** For a min-heap, every parent is no greater than its children. Appending an element can violate that rule only on the path to the root; moving it up repairs the affected path without sorting every element. The same invariant explains why the minimum is available at the root.

</details>

### 2. When is a queue a better data structure than a stack?

<details>
<summary>Show answer</summary>

Use a queue when items should leave in insertion order, such as breadth-first traversal. Use a stack when the most recently added item should leave first, such as undo history.

</details>

## Applied

### 3. When is an integration test more informative than a unit test with mocks?

<details>
<summary>Show answer</summary>

Use it when correctness depends on real boundary behavior, such as transaction isolation or serialization. Keep unit tests for deterministic rules and focus integration tests on behavior a mock cannot establish.

</details>

### 4. How can property-based tests expose errors an example misses?

<details>
<summary>Show answer</summary>

Generate diverse inputs and check an invariant, such as round-trip preservation. Shrinking can reduce a failure to a small case; useful properties and generators still need deliberate design.

</details>

## Advanced scenarios

### 5. How do you decide whether a slow algorithm needs a redesign or just a tighter loop?

<details>
<summary>Show answer</summary>

Profile representative inputs, vary their size, and identify the operation driving growth. Algorithm changes help when scaling dominates; constant-factor improvements matter when realistic input sizes or setup costs dominate.

</details>

### 6. How do you split a risky refactor from a behavior change?

<details>
<summary>Show answer</summary>

Capture important existing behavior in tests, migrate in small observable steps, and review behavior changes separately. Keep the old path available until evidence supports removing it.

</details>

## Official references

- [Core software engineering documentation](https://docs.oracle.com/javase/specs/)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).

[Try the Core software engineering practice demo](https://www.mentaro.app/demo?topic=core-engineering&utm_source=github)
