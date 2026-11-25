# PHP interview questions

[Browse Mentaro’s web question catalog](https://www.mentaro.app/interview-questions) · [Start interactive practice on Mentaro](https://www.mentaro.app/login?mode=signup&redirect=/onboarding)

## Foundations

### 1. Why can loose equality be surprising for values from HTTP input?

<details>
<summary>Show answer</summary>

PHP may coerce operand types with loose comparison. Parse the incoming string explicitly, validate the result and use strict comparison when type and value both matter.

**Explanation:** A form field is often a string, even when the value looks numeric. Parse according to a business rule before comparing: a leading zero may be meaningful in an identifier but not in a quantity. Casting to an integer too early can discard that distinction.

</details>

### 2. What does PHP strict_types change at a function boundary?

<details>
<summary>Show answer</summary>

For scalar parameter declarations in user-defined functions, it controls coercion based on the calling file. It does not validate array shapes or convert HTTP input into trusted data.

</details>

## Applied

### 3. How do you avoid loading a large CSV into memory in PHP?

<details>
<summary>Show answer</summary>

Read and parse rows incrementally, keep a bounded batch for writes and report malformed rows. Confirm that the database driver or abstraction is not buffering more data than intended.

</details>

### 4. How can you distinguish an absent input field from a present null in PHP?

<details>
<summary>Show answer</summary>

Use array_key_exists when you care about presence, since isset is false for missing and null values. Decide explicitly whether null should clear the field or be rejected.

</details>

## Advanced scenarios

### 5. What must change when a PHP handler runs in a long-lived worker?

<details>
<summary>Show answer</summary>

Mutable state can survive between requests. Avoid storing request identity or data in shared services, reset intentional per-request state, and test isolation across successive requests.

</details>

### 6. Why might increasing PHP-FPM workers make a site slower?

<details>
<summary>Show answer</summary>

More workers consume memory and can overwhelm databases or other dependencies. Compare process memory, queueing and downstream saturation before increasing the pool.

</details>

## Official references

- [PHP documentation](https://www.php.net/manual/en/)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
