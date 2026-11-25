# SwiftUI interview questions

[Browse Mentaro’s web question catalog](https://www.mentaro.app/interview-questions) · [Start interactive practice on Mentaro](https://www.mentaro.app/login?mode=signup&redirect=/onboarding)

## Foundations

### 1. Why should a SwiftUI view not treat body evaluation as a one-time hook?

<details>
<summary>Show answer</summary>

SwiftUI reevaluates body when dependencies may have changed. Keep it a description of current UI, and place side effects in lifecycle or action mechanisms with clear cancellation behavior.

</details>

### 2. What problem does a SwiftUI Binding solve?

<details>
<summary>Show answer</summary>

It provides access to a value owned elsewhere, allowing a child control to read and update that source of truth. It does not by itself create storage.

</details>

## Applied

### 3. How can the identity of a SwiftUI list row affect its local state?

<details>
<summary>Show answer</summary>

State storage is associated with view identity. Changing row IDs can reset state, and using positional IDs can associate state with the wrong row after reordering. Prefer stable model IDs.

</details>

### 4. When should a child view receive a plain value instead of a Binding?

<details>
<summary>Show answer</summary>

Use a plain value for read-only rendering. Grant write access only when the child edits the state directly; a discrete intent can often be represented more clearly by an action.

</details>

## Advanced scenarios

### 5. How do you stop an older search task from replacing results for a new query in SwiftUI?

<details>
<summary>Show answer</summary>

Key the task to the query so changes cancel old work. Ensure the underlying operation cooperates with cancellation and check relevance before publishing results, since completion can race with cancellation.

</details>

### 6. How do you stop navigation state from drifting after data is deleted?

<details>
<summary>Show answer</summary>

Use stable route identities and one owner for the navigation path. On data change, validate affected destinations and truncate or repair invalid paths predictably.

</details>

## Official references

- [SwiftUI documentation](https://developer.apple.com/documentation/swiftui/)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
