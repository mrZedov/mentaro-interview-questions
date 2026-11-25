# Qdrant interview questions

[Browse Mentaro’s web question catalog](https://www.mentaro.app/interview-questions) · [Start interactive practice on Mentaro](https://www.mentaro.app/login?mode=signup&redirect=/onboarding)

## Foundations

### 1. What does a Qdrant point contain?

<details>
<summary>Show answer</summary>

A point has an ID and one or more configured vectors, with optional payload metadata. The vector supports similarity search while payload fields support business filters or accompanying data.

</details>

### 2. Why choose a vector distance when creating a Qdrant collection?

<details>
<summary>Show answer</summary>

The distance determines how vectors are ranked. Match it to the embedding model and preprocessing rather than assuming the same metric preserves rankings across representations.

</details>

## Applied

### 3. Why might a payload filter change vector search performance?

<details>
<summary>Show answer</summary>

Selectivity changes how many points are eligible and which access strategy is efficient. Index frequently used filter fields and measure retrieval quality and latency with realistic filter distributions.

</details>

### 4. How would you check whether an approximate index misses relevant results?

<details>
<summary>Show answer</summary>

Build a reference with exact search over representative queries and filters. Compare recall and latency at the same result count and tune the search parameters under your workload.

</details>

## Advanced scenarios

### 5. How can you switch embeddings without mixing incompatible vector spaces?

<details>
<summary>Show answer</summary>

Build a new vector representation alongside the old one, backfill it, and validate retrieval on representative queries. Switch readers only after coverage is adequate and keep a rollback path.

</details>

### 6. How can ingestion affect serving for a Qdrant collection?

<details>
<summary>Show answer</summary>

Large writes consume indexing, memory and storage capacity. Batch ingestion, observe indexing progress and search latency, and isolate heavy rebuilding from serving when the workload demands it.

</details>

## Official references

- [Qdrant documentation](https://qdrant.tech/documentation/)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
