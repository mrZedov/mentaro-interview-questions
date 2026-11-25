# Django interview questions

[Browse Mentaro’s web question catalog](https://www.mentaro.app/interview-questions) · [Try the Django practice demo](https://www.mentaro.app/demo?topic=django&utm_source=github)

## Foundations

### 1. When does a Django QuerySet actually execute?

<details>
<summary>Show answer</summary>

Constructing or chaining it usually does not query the database. Evaluation operations such as iteration or conversion to a list do. Separate building the query from using its results.

**Explanation:** Evaluation and caching are also distinct. Iterating an ordinary queryset fills its result cache; refining it returns another queryset. Use exists() if you only need to know whether a row matches, but calling exists() before iterating an unevaluated queryset can mean two queries.

</details>

[Try the Django practice demo](https://www.mentaro.app/demo?topic=django&utm_source=github)

### 2. How does a Django model migration differ from model validation?

<details>
<summary>Show answer</summary>

Migrations describe persistent schema or data changes. Validation checks values before use and is not automatically run by every save; rely on database constraints for invariants across writers.

</details>

[Try the Django practice demo](https://www.mentaro.app/demo?topic=django&utm_source=github)

## Applied

### 3. How do select_related and prefetch_related address different N+1 queries?

<details>
<summary>Show answer</summary>

select_related joins single-valued relationships in SQL. prefetch_related runs additional queries and joins results in Python, which works for many-valued relationships. Measure row count and memory as well as query count.

</details>

[Try the Django practice demo](https://www.mentaro.app/demo?topic=django&utm_source=github)

### 4. When is a bulk update preferable to saving each model instance?

<details>
<summary>Show answer</summary>

Use a bulk operation for set-based work when per-instance save hooks are not required. Verify what signals and validation are bypassed and avoid overwriting concurrent edits.

</details>

[Try the Django practice demo](https://www.mentaro.app/demo?topic=django&utm_source=github)

## Advanced scenarios

### 5. Why might a task queued inside atomic not find the row it should process?

<details>
<summary>Show answer</summary>

A worker can run before the database commit. Use on_commit to defer submission until success; if publishing must survive a crash between commit and submission, use a durable outbox.

</details>

[Try the Django practice demo](https://www.mentaro.app/demo?topic=django&utm_source=github)

### 6. How would you avoid a long lock when adding a data backfill to a large Django table?

<details>
<summary>Show answer</summary>

Separate schema and data changes, process the data in bounded batches, and make the backfill resumable. Measure lock impact and coordinate with application versions still writing the old shape.

</details>

[Try the Django practice demo](https://www.mentaro.app/demo?topic=django&utm_source=github)

## Official references

- [Django documentation](https://docs.djangoproject.com/)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
