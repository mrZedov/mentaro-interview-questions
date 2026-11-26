# Backend engineering interview questions

[Browse Mentaro’s web question catalog](https://www.mentaro.app/interview-questions)

## Foundations

### 1. What is the difference between validation and enforcing an invariant?

<details>
<summary>Show answer</summary>

Validation checks whether input is acceptable. An invariant must remain true after concurrent updates and retries, so enforce it where authoritative state changes, such as with a transactional constraint.

**Explanation:** Two signup requests can both pass a check that an email is unused. A unique constraint on the normalized email prevents both inserts from succeeding. Keep validation for helpful feedback, but handle the storage conflict because a preliminary read cannot serialize concurrent writers.

</details>

### 2. What is the difference between a request timeout and an operation deadline?

<details>
<summary>Show answer</summary>

A timeout bounds a wait at one boundary. A deadline limits the end-to-end operation; propagate the remaining time so each retry and dependency does not start with a fresh full budget.

</details>

## Applied

### 3. How can a retryable payment request avoid charging twice?

<details>
<summary>Show answer</summary>

Require an operation identifier, bind it to the request, and reuse the provider idempotency mechanism. Persist the resulting status and reconcile uncertain outcomes rather than treating a network timeout as a definite failure.

</details>

### 4. Where should you normalize a user-editable identifier?

<details>
<summary>Show answer</summary>

Choose one normalization rule and apply it before comparison and persistence. Enforce uniqueness over the normalized value; preserve the original spelling separately if users should see it.

</details>

## Advanced scenarios

### 5. What should happen when a downstream notification service is slower than the write API?

<details>
<summary>Show answer</summary>

Decide whether notification is part of the success contract. If it is not, persist a durable work item with the write, process it with bounded retries, and monitor the backlog and delivery age.

</details>

### 6. How do you roll out a mandatory database column without breaking old app instances?

<details>
<summary>Show answer</summary>

Add a compatible nullable column first, deploy writers, backfill in bounded batches, then validate and enforce the constraint after incompatible instances are gone. Plan rollback between phases.

</details>

## Official references

- [Backend engineering documentation](https://sre.google/books/)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
