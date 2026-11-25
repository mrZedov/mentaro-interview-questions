# SQL interview questions

[Explore this topic on Mentaro](https://www.mentaro.app/interview-questions/sql) · [Try the SQL practice demo](https://www.mentaro.app/demo?topic=sql-language&utm_source=github)

## Foundations

### 1. Why can an inner join produce more rows than either input?

<details>
<summary>Show answer</summary>

It emits one row for each matching pair. Three left rows and four right rows sharing a key yield twelve joined rows for that key. Verify the cardinality before aggregating.

**Explanation:** If two orders match three payment rows for the same customer, joining both sets on customer ID produces six pairs. Check the intended grain and join on the key of the entity you mean to match.

</details>

[Try the SQL practice demo](https://www.mentaro.app/demo?topic=sql-language&utm_source=github)

### 2. How does COUNT(*) differ from COUNT(column)?

<details>
<summary>Show answer</summary>

COUNT(*) counts result rows, including rows with null fields. COUNT(column) counts only non-null values, which is useful when counting matches after a left join.

**Explanation:** A left join produces a placeholder row for a customer with no orders. Counting rows reports one for that customer; counting the non-nullable order key correctly reports zero. Keep the distinction when counting rows after any outer join.

**Example:**

```sql
SELECT c.id, COUNT(*) AS joined_rows, COUNT(o.id) AS orders
FROM customers AS c
LEFT JOIN orders AS o ON o.customer_id = c.id
GROUP BY c.id;
```

</details>

[Try the SQL practice demo](https://www.mentaro.app/demo?topic=sql-language&utm_source=github)

### 3. Why does column = NULL not find missing values?

<details>
<summary>Show answer</summary>

Ordinary comparisons with NULL generally evaluate to unknown, and WHERE retains only true conditions. Use IS NULL to find missing values.

**Explanation:** SQL conditions can be true, false or unknown; WHERE keeps only true. Use IS NULL for missingness. In PostgreSQL, IS NOT DISTINCT FROM compares two values while treating two nulls as equal.

</details>

[Try the SQL practice demo](https://www.mentaro.app/demo?topic=sql-language&utm_source=github)

### 4. When does a filter belong in HAVING rather than WHERE?

<details>
<summary>Show answer</summary>

WHERE filters input rows before grouping; HAVING filters the groups. Put a threshold on SUM(amount) in HAVING, but put the date range of rows to sum in WHERE.

**Explanation:** Think in two stages: WHERE defines which rows contribute; HAVING decides whether a completed group qualifies. Moving the status test into HAVING is not equivalent to excluding unqualified rows from the sum.

**Example:**

```sql
SELECT customer_id, SUM(amount) AS total
FROM orders
WHERE status = 'paid'
GROUP BY customer_id
HAVING SUM(amount) >= 100;
```

</details>

[Try the SQL practice demo](https://www.mentaro.app/demo?topic=sql-language&utm_source=github)

### 5. How does UNION ALL differ from UNION?

<details>
<summary>Show answer</summary>

UNION ALL keeps every input row. UNION removes duplicate result rows across and within its inputs. Use ALL when duplicates carry meaning or are already ruled out by the inputs.

**Explanation:** If shipment and pickup events carry the same date and customer, they can still be separate events. UNION drops identical projected tuples; UNION ALL preserves them and avoids a duplicate-elimination step.

</details>

[Try the SQL practice demo](https://www.mentaro.app/demo?topic=sql-language&utm_source=github)

## Applied

### 6. How do you keep customers with no paid order in a left join?

<details>
<summary>Show answer</summary>

Put the paid-order condition in the join's ON clause. A WHERE predicate requiring a paid status rejects the null-extended rows and loses customers with no match.

**Explanation:** Filtering the right table in WHERE discards the null-extended row. Put the criterion in ON if customers without qualifying orders must survive.

**Example:**

```sql
SELECT c.id, o.id AS paid_order
FROM customers AS c
LEFT JOIN orders AS o
  ON o.customer_id = c.id AND o.status = 'paid';
```

</details>

[Try the SQL practice demo](https://www.mentaro.app/demo?topic=sql-language&utm_source=github)

### 7. What can go wrong with NOT IN when the subquery returns NULL?

<details>
<summary>Show answer</summary>

A non-matching row may evaluate to unknown rather than true because of the NULL in the list. A correlated NOT EXISTS expresses the anti-join directly; decide explicitly how null keys should be treated.

**Explanation:** A NULL in the subquery can turn every nonmatching comparison into unknown. NOT EXISTS normally expresses the intended anti-join; decide separately whether a NULL on the outer side should qualify.

</details>

[Try the SQL practice demo](https://www.mentaro.app/demo?topic=sql-language&utm_source=github)

### 8. When should you use a window function instead of GROUP BY?

<details>
<summary>Show answer</summary>

Use a window function when you need to retain detail rows and also calculate values over a partition. GROUP BY collapses rows to one result per group.

**Explanation:** A window can attach each customer total to every order while keeping individual order rows available for display. A grouped query changes the result grain to one row per customer.

</details>

[Try the SQL practice demo](https://www.mentaro.app/demo?topic=sql-language&utm_source=github)

### 9. How do ROW_NUMBER, RANK and DENSE_RANK treat ties?

<details>
<summary>Show answer</summary>

ROW_NUMBER assigns distinct positions. RANK gives ties the same position and leaves gaps; DENSE_RANK gives ties the same position without gaps. Use an explicit tie-breaker when row selection must be deterministic.

**Explanation:** For scores 100, 100, 90, RANK yields 1, 1, 3 and DENSE_RANK yields 1, 1, 2. ROW_NUMBER assigns three distinct positions; add a unique tie-breaker if equal scores need predictable order.

</details>

[Try the SQL practice demo](https://www.mentaro.app/demo?topic=sql-language&utm_source=github)

### 10. Why can a running total jump across rows with the same timestamp?

<details>
<summary>Show answer</summary>

The default ordered window frame in many systems includes peers with equal ordering values. Use an explicit ROWS frame and a unique ordering when you intend one step per row.

**Explanation:** With a peer-aware frame, both rows at a shared timestamp can receive the same cumulative value. Use a stable tie-breaker and an explicit ROWS frame when you need one step per transaction.

</details>

[Try the SQL practice demo](https://www.mentaro.app/demo?topic=sql-language&utm_source=github)

### 11. How do you return one latest row per customer when two rows have the same timestamp?

<details>
<summary>Show answer</summary>

Rank rows in each customer partition by descending timestamp and a stable unique tie-breaker, then select rank one. Without a tie-breaker, row selection may change between executions.

**Explanation:** Use the same comparison direction for the secondary key as intended by the business rule. Duplicated timestamps alone cannot determine recency, so document whether a larger ID is a meaningful tie-breaker or merely a deterministic one.

</details>

[Try the SQL practice demo](https://www.mentaro.app/demo?topic=sql-language&utm_source=github)

### 12. When is EXISTS clearer than joining a table and removing duplicates?

<details>
<summary>Show answer</summary>

Use EXISTS when the condition is whether at least one related row qualifies. It avoids multiplying outer rows and expresses intent directly. Use a join when you actually need columns from matching rows.

**Explanation:** A semi-join asks whether a match exists without selecting a matching row. It keeps each outer row once even when several matches qualify; it does not deduplicate duplicates that already exist in the outer input.

</details>

[Try the SQL practice demo](https://www.mentaro.app/demo?topic=sql-language&utm_source=github)

## Advanced scenarios

### 13. Why does a revenue total double after joining orders to both line items and payments?

<details>
<summary>Show answer</summary>

Multiple rows on each child side multiply matches per order. Aggregate each child to the order grain first, then join the two summaries before calculating the total.

**Explanation:** Two line items and three payments produce six joined rows before aggregation. Sum each child table at order grain first, then join the totals; SUM(DISTINCT amount) would wrongly collapse separate payments of equal size.

</details>

[Try the SQL practice demo](https://www.mentaro.app/demo?topic=sql-language&utm_source=github)

### 14. How can applying a function to a timestamp make filtering slower?

<details>
<summary>Show answer</summary>

A predicate on a transformed column may not use a normal index on the column. Prefer a half-open range on the original timestamp, or use an appropriate expression index if the engine supports it.

**Explanation:** Instead of extracting a date from every indexed timestamp, calculate the start and end of the desired day once and query a half-open range. For civil dates, compute both boundaries in the user’s timezone to handle daylight-saving transitions.

</details>

[Try the SQL practice demo](https://www.mentaro.app/demo?topic=sql-language&utm_source=github)

### 15. How would you index a query that filters by account and returns its newest events?

<details>
<summary>Show answer</summary>

Consider an index starting with account ID and then the ordering key and tie-breaker. Verify with the actual plan and realistic account sizes; indexes also cost storage and write work.

**Explanation:** An index starting with account ID can narrow the scan to one account, then return timestamps in index order. Add the ordered tie-breaker if the endpoint uses it for pagination and verify the actual plan with a realistic tenant.

</details>

[Try the SQL practice demo](https://www.mentaro.app/demo?topic=sql-language&utm_source=github)

### 16. How do you claim a limited unit without overselling under concurrency?

<details>
<summary>Show answer</summary>

Avoid a separate unchecked read followed by a write. Use an atomic conditional update or an appropriate lock inside a transaction, and treat no affected row as an unsuccessful claim.

**Explanation:** Make the condition and update one database operation; a prior availability read does not prevent another transaction changing the count.

**Example:**

```sql
UPDATE inventory
SET available = available - 1
WHERE id = 7 AND available > 0;
```

A zero affected-row count means no unit was claimed.

</details>

[Try the SQL practice demo](https://www.mentaro.app/demo?topic=sql-language&utm_source=github)

### 17. Why might adding DISTINCT hide a data bug?

<details>
<summary>Show answer</summary>

It discards equal output rows but does not fix a bad join or incorrect business grain. Compare keys and expected multiplicity before deduplicating, especially when sums or counts are involved.

**Explanation:** If duplicates disappear under DISTINCT, remove it temporarily and count matches per expected key. That usually reveals whether you joined on a nonunique column or selected at the wrong grain.

</details>

[Try the SQL practice demo](https://www.mentaro.app/demo?topic=sql-language&utm_source=github)

## Official references

- [PostgreSQL: Table expressions](https://www.postgresql.org/docs/current/queries-table-expressions.html)
- [PostgreSQL: Window functions](https://www.postgresql.org/docs/current/tutorial-window.html)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
