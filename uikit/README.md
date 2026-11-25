# UIKit interview questions

[Browse Mentaro’s web question catalog](https://www.mentaro.app/interview-questions) · [Try the UIKit practice demo](https://www.mentaro.app/demo?topic=uikit&utm_source=github)

## Foundations

### 1. What is the purpose of the UIKit responder chain?

<details>
<summary>Show answer</summary>

It passes events or actions through relevant responders, such as views and view controllers, until one handles them. This separates routing from direct ownership of every handler.

**Explanation:** A command such as Paste can be routed to the currently focused text responder rather than wiring every command directly to every input. For a business action with an explicit owner, a direct call or delegate may be clearer than relying on whichever responder currently handles the selector.

</details>

[Try the UIKit practice demo](https://www.mentaro.app/demo?topic=uikit&utm_source=github)

### 2. How is a view controller different from a UIView in UIKit?

<details>
<summary>Show answer</summary>

A controller coordinates screen lifecycle and transitions. A view handles presentation, layout and interaction within the hierarchy. Keep reusable presentation behavior in views.

</details>

[Try the UIKit practice demo](https://www.mentaro.app/demo?topic=uikit&utm_source=github)

## Applied

### 3. Why can a reused table view cell show the wrong image?

<details>
<summary>Show answer</summary>

An earlier asynchronous load can finish after the cell represents a different item. Reset transient state on reuse, cancel the previous load and check the current item identity before applying a result.

</details>

[Try the UIKit practice demo](https://www.mentaro.app/demo?topic=uikit&utm_source=github)

### 4. Why use stable identifiers with a diffable data source?

<details>
<summary>Show answer</summary>

Diffing compares identities to compute inserts, moves and removals. Mutable display text is a poor identifier because edits can turn an update into a delete and insert.

</details>

[Try the UIKit practice demo](https://www.mentaro.app/demo?topic=uikit&utm_source=github)

## Advanced scenarios

### 5. How would you debug constraints that work in English but break with larger text?

<details>
<summary>Show answer</summary>

Reproduce using accessibility text sizes and a longer localization. Inspect broken constraints, content hugging and compression resistance, and allow vertical growth or wrapping rather than fixing text to one size.

</details>

[Try the UIKit practice demo](https://www.mentaro.app/demo?topic=uikit&utm_source=github)

### 6. How would you remove expensive layout work from a scrolling path?

<details>
<summary>Show answer</summary>

Profile before optimizing, avoid repeatedly creating equivalent constraints, and defer expensive content preparation. Validate improvements using frame timing rather than merely counting layout calls.

</details>

[Try the UIKit practice demo](https://www.mentaro.app/demo?topic=uikit&utm_source=github)

## Official references

- [UIKit documentation](https://developer.apple.com/documentation/uikit/)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
