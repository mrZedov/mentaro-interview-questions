# FastAPI interview questions

[Browse Mentaro’s web question catalog](https://www.mentaro.app/interview-questions) · [Try the FastAPI practice demo](https://www.mentaro.app/demo?topic=fastapi&utm_source=github)

## Foundations

### 1. What is the practical difference between def and async def route handlers in FastAPI?

<details>
<summary>Show answer</summary>

FastAPI runs normal def handlers in a thread pool. An async def handler runs on the event loop and should await nonblocking I/O; calling a blocking library inside it can stall unrelated requests.

**Explanation:** FastAPI chooses how to invoke the route handler, not every function it calls. A plain blocking helper called directly from async def still blocks that event loop. An async client fits an async handler; a synchronous client fits a sync path or an explicit bounded offload.

</details>

[Try the FastAPI practice demo](https://www.mentaro.app/demo?topic=fastapi&utm_source=github)

### 2. What does a FastAPI response model provide beyond a return annotation?

<details>
<summary>Show answer</summary>

It can validate, serialize and filter output and describe the schema in OpenAPI. Define an explicit public schema instead of returning persistence objects with fields clients should not see.

</details>

[Try the FastAPI practice demo](https://www.mentaro.app/demo?topic=fastapi&utm_source=github)

## Applied

### 3. How do you share client setup without constructing a connection pool on every request?

<details>
<summary>Show answer</summary>

Create the client in the application lifespan, make it available to handlers through dependency injection or application state, and close it on shutdown. Keep per-request transaction state separate.

</details>

[Try the FastAPI practice demo](https://www.mentaro.app/demo?topic=fastapi&utm_source=github)

### 4. How do you test a route that depends on an external client?

<details>
<summary>Show answer</summary>

Override the dependency in the test app, exercise the HTTP route, and clear the override afterward. Keep real request validation in the test and include upstream failure cases.

</details>

[Try the FastAPI practice demo](https://www.mentaro.app/demo?topic=fastapi&utm_source=github)

## Advanced scenarios

### 5. When is FastAPI BackgroundTasks a poor choice for processing an upload?

<details>
<summary>Show answer</summary>

Use durable external work when jobs must survive process restarts, require retries, or are CPU-intensive. In-process background tasks are not a durable job queue.

</details>

[Try the FastAPI practice demo](https://www.mentaro.app/demo?topic=fastapi&utm_source=github)

### 6. What can go wrong if every application worker opens a large database pool?

<details>
<summary>Show answer</summary>

Pool sizes multiply across workers and replicas. Budget the total number of connections, set checkout timeouts and observe connection wait separately from database execution time.

</details>

[Try the FastAPI practice demo](https://www.mentaro.app/demo?topic=fastapi&utm_source=github)

## Official references

- [FastAPI documentation](https://fastapi.tiangolo.com/)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
