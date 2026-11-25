# SQL interview questions

[Explore this topic on Mentaro](https://www.mentaro.app/interview-questions/sql) · [Start interactive practice on Mentaro](https://www.mentaro.app/login?mode=signup&redirect=/onboarding)

## Foundations

### 1. Why can an inner join produce more rows than either input?

<details>
<summary>Show answer</summary>

It emits one row for each matching pair. Three left rows and four right rows sharing a key yield twelve joined rows for that key. Verify the cardinality before aggregating.

</details>

### 2. How does COUNT(*) differ from COUNT(column)?

<details>
<summary>Show answer</summary>

COUNT(*) counts result rows, including rows with null fields. COUNT(column) counts only non-null values, which is useful when counting matches after a left join.

</details>

### 3. Why does column = NULL not find missing values?

<details>
<summary>Show answer</summary>

Ordinary comparisons with NULL generally evaluate to unknown, and WHERE retains only true conditions. Use IS NULL to find missing values.

</details>

### 4. When does a filter belong in HAVING rather than WHERE?

<details>
<summary>Show answer</summary>

WHERE filters input rows before grouping; HAVING filters the groups. Put a threshold on SUM(amount) in HAVING, but put the date range of rows to sum in WHERE.

</details>

### 5. How does UNION ALL differ from UNION?

<details>
<summary>Show answer</summary>

UNION ALL keeps every input row. UNION removes duplicate result rows across and within its inputs. Use ALL when duplicates carry meaning or are already ruled out by the inputs.

</details>

## Applied

### 6. How do you keep customers with no paid order in a left join?

<details>
<summary>Show answer</summary>

Put the paid-order condition in the join's ON clause. A WHERE predicate requiring a paid status rejects the null-extended rows and loses customers with no match.

</details>

### 7. What can go wrong with NOT IN when the subquery returns NULL?

<details>
<summary>Show answer</summary>

A non-matching row may evaluate to unknown rather than true because of the NULL in the list. A correlated NOT EXISTS expresses the anti-join directly; decide explicitly how null keys should be treated.

</details>

### 8. When should you use a window function instead of GROUP BY?

<details>
<summary>Show answer</summary>

Use a window function when you need to retain detail rows and also calculate values over a partition. GROUP BY collapses rows to one result per group.

</details>

### 9. How do ROW_NUMBER, RANK and DENSE_RANK treat ties?

<details>
<summary>Show answer</summary>

ROW_NUMBER assigns distinct positions. RANK gives ties the same position and leaves gaps; DENSE_RANK gives ties the same position without gaps. Use an explicit tie-breaker when row selection must be deterministic.

</details>

### 10. Why can a running total jump across rows with the same timestamp?

<details>
<summary>Show answer</summary>

The default ordered window frame in many systems includes peers with equal ordering values. Use an explicit ROWS frame and a unique ordering when you intend one step per row.

</details>

### 11. How do you return one latest row per customer when two rows have the same timestamp?

<details>
<summary>Show answer</summary>

Rank rows in each customer partition by descending timestamp and a stable unique tie-breaker, then select rank one. Without a tie-breaker, row selection may change between executions.

</details>

### 12. When is EXISTS clearer than joining a table and removing duplicates?

<details>
<summary>Show answer</summary>

Use EXISTS when the condition is whether at least one related row qualifies. It avoids multiplying outer rows and expresses intent directly. Use a join when you actually need columns from matching rows.

</details>

## Advanced scenarios

### 13. Why does a revenue total double after joining orders to both line items and payments?

<details>
<summary>Show answer</summary>

Multiple rows on each child side multiply matches per order. Aggregate each child to the order grain first, then join the two summaries before calculating the total.

</details>

### 14. How can applying a function to a timestamp make filtering slower?

<details>
<summary>Show answer</summary>

A predicate on a transformed column may not use a normal index on the column. Prefer a half-open range on the original timestamp, or use an appropriate expression index if the engine supports it.

</details>

### 15. How would you index a query that filters by account and returns its newest events?

<details>
<summary>Show answer</summary>

Consider an index starting with account ID and then the ordering key and tie-breaker. Verify with the actual plan and realistic account sizes; indexes also cost storage and write work.

</details>

### 16. How do you claim a limited unit without overselling under concurrency?

<details>
<summary>Show answer</summary>

Avoid a separate unchecked read followed by a write. Use an atomic conditional update or an appropriate lock inside a transaction, and treat no affected row as an unsuccessful claim.

</details>

### 17. Why might adding DISTINCT hide a data bug?

<details>
<summary>Show answer</summary>

It discards equal output rows but does not fix a bad join or incorrect business grain. Compare keys and expected multiplicity before deduplicating, especially when sums or counts are involved.

</details>

## Official references

- [PostgreSQL: Table expressions](https://www.postgresql.org/docs/current/queries-table-expressions.html)
- [PostgreSQL: Window functions](https://www.postgresql.org/docs/current/tutorial-window.html)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
