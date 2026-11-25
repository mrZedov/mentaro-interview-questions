# Laravel interview questions

[Browse Mentaro’s web question catalog](https://www.mentaro.app/interview-questions) · [Start interactive practice on Mentaro](https://www.mentaro.app/login?mode=signup&redirect=/onboarding)

## Foundations

### 1. What distinguishes a Laravel middleware from a controller?

<details>
<summary>Show answer</summary>

Middleware applies concerns around the request pipeline, such as authentication. A controller translates a matched request into application work and a response. Keep core rules callable outside HTTP.

</details>

### 2. Why do Laravel route model binding and authorization solve different problems?

<details>
<summary>Show answer</summary>

Binding resolves a route parameter to an object. Authorization decides whether the current caller may perform the action on it. A resolved record is not evidence of access.

</details>

## Applied

### 3. How can you remove an Eloquent N+1 problem in a list endpoint?

<details>
<summary>Show answer</summary>

Eager-load the relations the view or serializer needs explicitly, then inspect SQL and response size. Limiting fetched columns and paginating can matter as much as the number of queries.

</details>

### 4. What changes when Laravel chunking updates the records being scanned?

<details>
<summary>Show answer</summary>

Offset-based chunks can skip or repeat rows if updates move records into or out of the result set. Iterate using a stable ordered key and define what to do with new rows during the run.

</details>

## Advanced scenarios

### 5. Why can a queued Laravel job execute before a transaction is visible?

<details>
<summary>Show answer</summary>

Dispatch and database commit are separate. Use after-commit dispatch when the job needs committed state; for stronger crash recovery, record the work transactionally and publish it separately.

</details>

### 6. How do you investigate job retries that cause inconsistent results?

<details>
<summary>Show answer</summary>

Inspect whether the effect survives before the job is acknowledged. Use an operation ID or unique constraint to deduplicate, and make timeouts and retry intervals consistent with the worker visibility settings.

</details>

## Official references

- [Laravel documentation](https://laravel.com/docs)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
