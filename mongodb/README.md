# MongoDB interview questions

[Explore this topic on Mentaro](https://www.mentaro.app/interview-questions/mongodb) · [Start interactive practice on Mentaro](https://www.mentaro.app/login?mode=signup&redirect=/onboarding)

## Foundations

### 1. When should related data be embedded rather than referenced?

<details>
<summary>Show answer</summary>

Embed bounded data read and updated with its parent. Reference independently accessed or unbounded children so one growing document does not become a bottleneck.

</details>

### 2. Does MongoDB's flexible schema prevent validation?

<details>
<summary>Show answer</summary>

No. A collection validator can reject documents that do not meet required shapes or types. Plan how existing documents will be migrated before tightening validation.

</details>

### 3. What does a single-document atomic update guarantee?

<details>
<summary>Show answer</summary>

Changes within the document apply atomically. Use a predicate that matches the expected current state if a concurrent modification should invalidate your update.

**Explanation:** Readers cannot observe half of a single-document update. A guarded update can protect a rule on that document without an application-level read followed by a separate write. Inspect matchedCount to tell whether the guard succeeded; separate documents still need a separate consistency design.

**Example:**

```javascript
async function reserveOne(inventory, itemId) {
  const result = await inventory.updateOne(
    { _id: itemId, available: { $gte: 1 } },
    { $inc: { available: -1 } }
  );
  if (result.matchedCount === 0) throw new Error("Item unavailable");
}
```

Pass a MongoDB driver Collection and the ID of an existing inventory item. With one unit left, at most one competing reservation matching that ID can decrement it.

</details>

### 4. What happens if you index a field containing arrays?

<details>
<summary>Show answer</summary>

MongoDB can build a multikey index with entries from the array. Check compound index restrictions and growth in index entries before indexing multiple array-like fields.

</details>

### 5. Why can a query using { field: null } return documents without that field?

<details>
<summary>Show answer</summary>

The condition matches both explicit null and missing fields. Use an existence or type predicate when the distinction matters.

</details>

## Applied

### 6. How do you choose a compound index for equality, sorting and a range?

<details>
<summary>Show answer</summary>

Place common equality fields first. Decide whether supporting the sort or using a selective range is more valuable, and confirm the candidate against explain and representative data.

</details>

### 7. Why is it useful to move a selective $match early in an aggregation?

<details>
<summary>Show answer</summary>

It reduces the rows downstream stages need to process and can allow indexed access near the start. Review the optimized plan; a filter depending on derived fields cannot run before those fields exist.

</details>

### 8. How can $unwind alter an aggregation's result count?

<details>
<summary>Show answer</summary>

It emits one result per array element and normally omits null, missing or empty arrays. Configure preservation explicitly when you still need one output for those cases.

</details>

### 9. How would you paginate an activity feed with duplicate timestamps?

<details>
<summary>Show answer</summary>

Order on timestamp and a unique tie-breaker, and apply a matching range predicate for the next page. Match the compound index to the filtering and sort order.

</details>

### 10. When would you use a partial index?

<details>
<summary>Show answer</summary>

Use it when an important query consistently targets a defined subset, such as active records. Its predicate must be implied by queries using the index, or the index could omit relevant documents.

</details>

### 11. How do you prevent lost updates when concurrent requests edit the same MongoDB document?

<details>
<summary>Show answer</summary>

Filter the update by a version or the expected prior value and update it atomically. If no document matches, handle the conflict instead of silently replacing newer data.

</details>

## Advanced scenarios

### 12. What does a high documents-examined to results-returned ratio suggest?

<details>
<summary>Show answer</summary>

The query may examine many candidates before filtering. Inspect its execution plan and predicate selectivity, then test an index or model change rather than tuning on timing alone.

</details>

### 13. What does majority write concern address after a primary failure?

<details>
<summary>Show answer</summary>

It asks for acknowledgement from the required majority according to the deployment's rules. It reduces rollback exposure compared with primary-only acknowledgement but does not choose the read consistency of a later request.

</details>

### 14. Why can a shard key based on increasing timestamps create uneven write load?

<details>
<summary>Show answer</summary>

New writes may concentrate in the current range even when historical data is balanced. Compare routing and query needs before using hashing or a compound key to distribute current writes.

</details>

### 15. Why avoid transactions for every multi-document read or write?

<details>
<summary>Show answer</summary>

Transactions provide guarantees when an invariant spans documents, but can add contention and cost. First consider whether the invariant can live in one document, or whether an asynchronous projection is sufficient.

</details>

### 16. How would you change a document field without breaking rolling deployments?

<details>
<summary>Show answer</summary>

Deploy readers that handle both forms, then update writers and backfill in bounded chunks. Enable the stricter validator only after old writers are gone and remaining legacy records are accounted for.

</details>

### 17. Why can a collection with a suitable index still have high write latency?

<details>
<summary>Show answer</summary>

Writes must maintain indexes and may wait for the configured write concern. Inspect write workload, storage latency and replication behavior rather than assuming read-plan improvements solve writes.

</details>

## Official references

- [MongoDB: Data modeling](https://www.mongodb.com/docs/manual/data-modeling/)
- [MongoDB: Indexes](https://www.mongodb.com/docs/manual/indexes/)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
