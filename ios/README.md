# iOS interview questions

[Browse Mentaro’s web question catalog](https://www.mentaro.app/interview-questions) · [Start interactive practice on Mentaro](https://www.mentaro.app/login?mode=signup&redirect=/onboarding)

## Foundations

### 1. Why should an iOS app not rely on termination to save important state?

<details>
<summary>Show answer</summary>

The operating system can suspend or terminate the process without giving it an opportunity to save. Persist important changes when made and treat lifecycle transitions as additional checkpoints.

**Explanation:** A draft that exists only in a view controller disappears if the app is suspended and later reclaimed. Persist durable input as it changes, perhaps with a short debounce, and reconstruct the screen from that saved state when launched again.

</details>

### 2. How does Dynamic Type affect choices about iOS layout?

<details>
<summary>Show answer</summary>

Users can increase text size substantially. Use scalable fonts and layouts that wrap or reflow instead of assuming labels always fit their default size.

</details>

## Applied

### 3. Where should an iOS app store a small authentication secret?

<details>
<summary>Show answer</summary>

Use Keychain with accessibility appropriate to when the app needs the secret. Avoid using preferences for credentials and define clearing behavior on sign-out.

</details>

### 4. Why should image downsampling happen before displaying a thumbnail?

<details>
<summary>Show answer</summary>

Decoding a full-resolution image can allocate much more memory than the rendered thumbnail needs. Decode closer to display size and keep the work off the main thread if it is expensive.

</details>

## Advanced scenarios

### 5. How would you investigate stuttering while scrolling an image-heavy screen?

<details>
<summary>Show answer</summary>

Profile frame time and main-thread work. Avoid synchronous decoding and oversized images during rendering, cancel work for recycled views, and confirm changes on representative devices.

</details>

### 6. How do you test an iOS flow that depends on returning from another app?

<details>
<summary>Show answer</summary>

Test cancel, successful return, repeated callbacks and process recreation. Persist essential operation context and avoid assuming an in-memory controller will still exist.

</details>

## Official references

- [iOS documentation](https://developer.apple.com/documentation/)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
