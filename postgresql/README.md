# PostgreSQL interview questions

[Explore this topic on Mentaro](https://www.mentaro.app/interview-questions/postgresql) · [Start interactive practice on Mentaro](https://www.mentaro.app/login?mode=signup&redirect=/onboarding)

## Foundations

### 1. How does MVCC let readers and writers work concurrently?

<details>
<summary>Show answer</summary>

An update creates a new row version. Each reader sees the version visible to its snapshot, so an ordinary read does not need to wait for an uncommitted update to finish.

</details>

### 2. What does two SELECT statements seeing different data in one default transaction tell you?

<details>
<summary>Show answer</summary>

Under Read Committed, each statement gets its own snapshot. Another transaction can commit between the queries, so the second statement can see new data.

**Explanation:** Assume no writes by the first transaction itself and an ordinary read. Reproduce this in two sessions: A reads a balance, B updates and commits, then A repeats the read. If a computation requires both reads to use the same snapshot, choose an appropriate isolation level; if it enforces a cross-transaction rule, also reason about locks, constraints or retries.

</details>

### 3. What is the difference between a primary key and a unique nullable column?

<details>
<summary>Show answer</summary>

A primary key enforces both uniqueness and non-nullability. A unique constraint ordinarily permits multiple nulls in PostgreSQL unless configured with NULLS NOT DISTINCT.

</details>

### 4. Does declaring a foreign key index the referencing column?

<details>
<summary>Show answer</summary>

Not automatically. Index the referencing columns when lookup or parent deletes and updates justify it; PostgreSQL already requires a suitable unique key on the referenced side.

</details>

### 5. What does WAL provide in crash recovery?

<details>
<summary>Show answer</summary>

PostgreSQL records changes before the data pages they describe need to reach durable storage. Recovery replays necessary records from a checkpoint rather than assuming every changed page was flushed on commit.

</details>

## Applied

### 6. When does SELECT FOR UPDATE help protect a business rule?

<details>
<summary>Show answer</summary>

It locks rows you intend to change until the transaction ends. It does not lock a row that does not exist; use a constraint or another appropriate coordination mechanism for absence-based rules.

</details>

### 7. Why can a Serializable transaction still return an error?

<details>
<summary>Show answer</summary>

PostgreSQL can abort a transaction to prevent a non-serializable outcome. Retry the whole transaction with fresh reads and avoid replaying external side effects from the failed attempt.

</details>

### 8. What is the difference between estimated and actual rows in a query plan?

<details>
<summary>Show answer</summary>

Estimated rows are planner predictions; actual rows are observed by EXPLAIN ANALYZE. Large differences can signal stale statistics or misestimated correlations. Account for the plan node's loop count.

</details>

### 9. Why does an index-only plan sometimes still fetch heap pages?

<details>
<summary>Show answer</summary>

The index may contain all requested values but not enough MVCC visibility information. PostgreSQL uses the visibility map to avoid heap checks on pages known to be all visible.

</details>

### 10. When is SKIP LOCKED useful and what does it sacrifice?

<details>
<summary>Show answer</summary>

Multiple workers can claim different available queue rows without waiting for one another. The result deliberately skips locked rows and is not a consistent read of all eligible work.

</details>

### 11. When can a partial index be smaller and more useful than a full index?

<details>
<summary>Show answer</summary>

Index only the rows commonly queried, such as open jobs. The planner must be able to prove that the query implies the index predicate; a generic parameterized condition may not let it do so.

</details>

## Advanced scenarios

### 12. How can a long-lived transaction cause table bloat?

<details>
<summary>Show answer</summary>

Its old snapshot can keep dead row versions potentially visible. Investigate long or idle-in-transaction sessions and let vacuum reclaim reusable space after the retention condition clears.

</details>

### 13. How do you diagnose a query that is waiting rather than using CPU?

<details>
<summary>Show answer</summary>

Inspect its wait event and blocking sessions before changing the query. A lock conflict and slow storage need different fixes even if the request times look similar.

</details>

### 14. Why can a sequential scan be right after adding an index?

<details>
<summary>Show answer</summary>

For a query reading a large fraction of a table, scanning it may be cheaper than many heap lookups. Confirm with realistic plans and buffers rather than forcing an index.

</details>

### 15. What should you test before adding an index concurrently on a busy table?

<details>
<summary>Show answer</summary>

Check the statement's transaction restrictions, expected disk and load headroom, and cleanup of an invalid index after failure. Verify the migration on a realistically sized table.

</details>

### 16. Why might PostgreSQL benefit from extended statistics?

<details>
<summary>Show answer</summary>

The planner can underestimate or overestimate predicates on correlated columns when it treats them independently. Extended statistics can capture some multi-column relationships, but still verify the affected query plan.

</details>

### 17. Why can adding requests to a busy connection pool make p99 worse?

<details>
<summary>Show answer</summary>

More concurrent queries can increase lock waits, memory pressure and I/O contention. Measure time waiting for a connection separately from query time, and limit admitted work based on database capacity.

</details>

## Official references

- [PostgreSQL: Transaction isolation](https://www.postgresql.org/docs/current/transaction-iso.html)
- [PostgreSQL: Using EXPLAIN](https://www.postgresql.org/docs/current/using-explain.html)
- [PostgreSQL: Routine vacuuming](https://www.postgresql.org/docs/current/routine-vacuuming.html)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
