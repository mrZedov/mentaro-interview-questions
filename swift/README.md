# Swift interview questions

[Browse Mentaro’s web question catalog](https://www.mentaro.app/interview-questions) · [Try the Swift practice demo](https://www.mentaro.app/demo?topic=swift&utm_source=github)

## Foundations

### 1. When is a Swift struct a better default than a class?

<details>
<summary>Show answer</summary>

Use a struct when independent value semantics fit the data. Use a class when shared identity or lifecycle matters. A struct that contains a reference does not necessarily make that object independent.

**Explanation:** A value like a screen filter should usually be independently editable after assignment. A coordinator shared by multiple views may instead need one identity and lifetime. If a struct contains a mutable class instance, copying the struct does not copy that referenced object.

</details>

### 2. How does optional binding differ from force unwrapping in Swift?

<details>
<summary>Show answer</summary>

Optional binding handles the missing case and exposes a value only when it exists. Force unwrapping traps on nil and should only follow a separately enforced invariant.

</details>

## Applied

### 3. How does a closure contribute to an ARC retain cycle?

<details>
<summary>Show answer</summary>

An escaping closure can capture an owner strongly while the owner retains the closure. Use a weak capture if the owner may disappear, or break the ownership cycle explicitly when the work ends.

</details>

### 4. What is the benefit of implementing a protocol with associated types?

<details>
<summary>Show answer</summary>

It lets a conforming type specify related types while preserving relationships in generic code. Choose a generic API or a suitably constrained existential based on what the caller must know.

</details>

## Advanced scenarios

### 5. Why must actor-isolated state sometimes be checked again after await?

<details>
<summary>Show answer</summary>

Actors can run other messages while an operation is suspended. An invariant checked before suspension may no longer hold when it resumes. Revalidate it or keep the state change in an uninterrupted isolated section.

</details>

### 6. How do you cancel a Swift task without leaving resources in an inconsistent state?

<details>
<summary>Show answer</summary>

Cancellation is cooperative. Check cancellation at meaningful boundaries, use defer for cleanup, and ensure an interrupted operation can roll back or finish a small atomic step safely.

</details>

## Official references

- [Swift documentation](https://www.swift.org/documentation/)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).

[Try the Swift practice demo](https://www.mentaro.app/demo?topic=swift&utm_source=github)
