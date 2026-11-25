# REST API interview questions

[Explore this topic on Mentaro](https://www.mentaro.app/interview-questions/rest-api) · [Start interactive practice on Mentaro](https://www.mentaro.app/login?mode=signup&redirect=/onboarding)

## Foundations

### 1. How do a safe HTTP method and an idempotent method differ?

<details>
<summary>Show answer</summary>

A safe method does not ask the server to change resource state; incidental logging is still allowed. An idempotent method has the same intended effect when the same request is repeated. DELETE is idempotent but not safe: repeating it can leave the resource absent even if the second response differs.

**Explanation:** Safety is about the action requested, not whether the server logs the request or updates metrics. Idempotence is about effect, not identical responses: a repeated DELETE may return 404 after an initial success while still leaving the resource absent. Do not infer that an arbitrary POST is safe to retry after a lost response.

</details>

### 2. When would you use PUT rather than PATCH to update a resource?

<details>
<summary>Show answer</summary>

Use PUT when the client supplies the desired replacement state for a known resource URI. Use PATCH when the body describes changes rather than the whole replacement. Whether a particular patch is idempotent depends on its operations and the API contract.

</details>

### 3. How do 201 Created and 202 Accepted differ for an export request?

<details>
<summary>Show answer</summary>

Use 201 if the request has created the resource; give the client its URI when appropriate. Use 202 if processing was accepted but not completed. Returning a status URL gives the client a way to discover success or failure instead of treating acceptance as success.

</details>

### 4. When is 401 a better response than 403?

<details>
<summary>Show answer</summary>

Use 401 when valid authentication credentials are missing or invalid; it carries an authentication challenge. Use 403 when the server understood the request but refuses it. An API may deliberately return 404 rather than disclose the existence of an inaccessible resource.

</details>

### 5. What belongs in a machine-readable validation error?

<details>
<summary>Show answer</summary>

Include a stable problem type, an appropriate HTTP status, and actionable detail. Use a documented extension for field-level issues when needed. Clients should branch on the type or structured fields, not localized prose; do not emit stack traces or private submitted values.

</details>

## Applied

### 6. How can clients retry a create request after the connection drops?

<details>
<summary>Show answer</summary>

Use a client-provided idempotency key scoped to the caller and operation. Persist a record of the key and outcome atomically with the mutation, or implement an equivalent durable coordination mechanism. Reuse the stored outcome on replay, and reject the same key with a different request payload.

</details>

### 7. How can an API prevent two editors from silently overwriting one another?

<details>
<summary>Show answer</summary>

Return a strong ETag with the representation. Require If-Match on writes and enforce the comparison atomically with the update. If the supplied validator no longer matches, respond with 412 so the caller can reload and reconcile rather than overwrite newer data.

</details>

### 8. What does a 304 response save, and when should a server send it?

<details>
<summary>Show answer</summary>

It saves retransmitting a representation the client already has. A conditional GET carrying a matching validator can receive 304 without the response body. The client then reuses its cached representation and updates the relevant stored response metadata.

</details>

### 9. How do Cache-Control private and no-store differ?

<details>
<summary>Show answer</summary>

Private disallows storage by a shared cache but can permit storage in the user's own cache. No-store directs caches not to store the response. Neither substitutes for authentication or for checking how application caches handle user-specific data.

</details>

### 10. Why include a tie-breaker in cursor pagination?

<details>
<summary>Show answer</summary>

If rows share a timestamp, a cursor containing only that timestamp cannot distinguish their order. Sort by a stable pair such as timestamp and ID and encode both in the cursor. Document how updates or deletions affect the traversal; cursor pagination is not automatically a consistent snapshot.

</details>

### 11. Why include Vary: Accept-Language on a cacheable localized response?

<details>
<summary>Show answer</summary>

It tells a shared cache that the selected representation depends on the request language preference. Without it, a cached response can be reused for another language. Consider explicit locale URLs if they suit the product.

</details>

## Advanced scenarios

### 12. How should a client respond to a 429 rate-limit response?

<details>
<summary>Show answer</summary>

Respect Retry-After when supplied. Otherwise use bounded backoff with jitter and cap attempts within an overall deadline. Retrying every rejected request immediately makes overload worse; confirm the operation is safe to repeat and avoid synchronized retry bursts.

</details>

### 13. What must an API verify before serving GET /projects/:id to a signed-in user?

<details>
<summary>Show answer</summary>

Verify that the current user can access that particular project, not just that they have a valid session or a general role. Query through the membership or ownership scope and test that another user's project cannot be read. Apply the same rule to nested resources.

</details>

### 14. When can adding a response field still break old clients?

<details>
<summary>Show answer</summary>

Clients may enforce an exact schema, generate brittle decoders or reinterpret a new enum value as an impossible case. Document extension rules, use tolerant readers where possible and test representative old clients before relying on additive compatibility.

</details>

### 15. How do you model a slow export without keeping an HTTP request open?

<details>
<summary>Show answer</summary>

Accept the submission and expose a job resource with pending, complete and failed states. Return a status link the authorized caller can poll with backoff. Separate job status from the lifetime of the resulting download and define replay and expiration behavior.

</details>

### 16. What should you measure when a list endpoint grows slow for larger tenants?

<details>
<summary>Show answer</summary>

Break latency into authorization, database work, serialization and response size. Compare query plans and row counts across tenant sizes, enforce bounded page sizes and check indexes against the actual filtering and ordering. An average across tenants can hide the slow path.

</details>

### 17. What must a server check before resuming a download with a saved byte offset?

<details>
<summary>Show answer</summary>

Ensure the bytes belong to the same representation. A client can send If-Range with a strong validator and its Range header. If the validator no longer matches, the server can send the full representation instead of mixing versions.

</details>

## Official references

- [IETF: HTTP Semantics (RFC 9110)](https://www.rfc-editor.org/rfc/rfc9110.html)
- [IETF: HTTP Caching (RFC 9111)](https://www.rfc-editor.org/rfc/rfc9111.html)
- [IETF: Problem Details for HTTP APIs (RFC 9457)](https://www.rfc-editor.org/rfc/rfc9457.html)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
