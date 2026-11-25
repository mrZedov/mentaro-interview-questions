# React Native interview questions

[Browse Mentaro’s web question catalog](https://www.mentaro.app/interview-questions) · [Try the React Native practice demo](https://www.mentaro.app/demo?topic=react-native&utm_source=github)

## Foundations

### 1. Why can a React Native screen render differently on iOS and Android?

<details>
<summary>Show answer</summary>

React Native uses native platform views and APIs. Defaults, font metrics and platform capabilities can differ. Use shared behavior where practical and verify platform-specific layout and accessibility.

**Explanation:** A fixed-height label that fits with one platform font can clip at another font size. Use a layout that can grow, test platform accessibility text settings, and adapt input or navigation behavior only where platform conventions differ.

</details>

[Try the React Native practice demo](https://www.mentaro.app/demo?topic=react-native&utm_source=github)

### 2. When should a React Native component have platform-specific implementations?

<details>
<summary>Show answer</summary>

Use them when behavior depends on a native API or platform convention. Keep shared business logic and accessible semantics consistent so the platform variants do not drift.

</details>

[Try the React Native practice demo](https://www.mentaro.app/demo?topic=react-native&utm_source=github)

## Applied

### 3. When should a React Native app use FlatList instead of ScrollView?

<details>
<summary>Show answer</summary>

Use FlatList for large lists where rendering a window of items limits memory and initial work. Use stable keys and measure item rendering costs. ScrollView is simpler for small fixed content.

</details>

[Try the React Native practice demo](https://www.mentaro.app/demo?topic=react-native&utm_source=github)

### 4. How do you keep FlatList items from showing stale selection state?

<details>
<summary>Show answer</summary>

Keep selection in an appropriate state owner, update it immutably, and ensure the list receives the changed dependency. Use stable item keys and verify updates on recycled or remounted rows.

</details>

[Try the React Native practice demo](https://www.mentaro.app/demo?topic=react-native&utm_source=github)

## Advanced scenarios

### 5. How can you tell whether jank is caused by the JavaScript thread or the UI thread?

<details>
<summary>Show answer</summary>

Compare frame measurements and profiles for both threads in a release build. Heavy JavaScript work can delay events and updates; excessive native layout or drawing can remain slow even when JavaScript is idle.

</details>

[Try the React Native practice demo](https://www.mentaro.app/demo?topic=react-native&utm_source=github)

### 6. How would you diagnose a release-only startup crash in React Native?

<details>
<summary>Show answer</summary>

Collect symbolicated JavaScript and native crash data from the same build. Check native configuration, bundle availability and environment differences, and reproduce using a signed or release-like build.

</details>

[Try the React Native practice demo](https://www.mentaro.app/demo?topic=react-native&utm_source=github)

## Official references

- [React Native documentation](https://reactnative.dev/docs/getting-started)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
