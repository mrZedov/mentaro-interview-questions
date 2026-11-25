# OpenTelemetry interview questions

[Browse Mentaro’s web question catalog](https://www.mentaro.app/interview-questions) · [Start interactive practice on Mentaro](https://www.mentaro.app/login?mode=signup&redirect=/onboarding)

## Foundations

### 1. What is the relationship between a span and a trace?

<details>
<summary>Show answer</summary>

A span represents one operation with timing, attributes and relationships. A trace groups related spans under a trace ID so the path through components can be inspected.

</details>

### 2. What does context propagation provide across process boundaries?

<details>
<summary>Show answer</summary>

It lets a downstream service correlate work with its upstream trace and parent span. Inject and extract context through supported carriers and only propagate baggage appropriate for the destination.

</details>

## Applied

### 3. What makes an attribute a bad metric label?

<details>
<summary>Show answer</summary>

Values such as user IDs or raw paths can create unbounded time series and high storage cost. Use bounded categories or route templates; use appropriately controlled traces or logs for individual cases.

</details>

### 4. When would you use a span event instead of creating a child span?

<details>
<summary>Show answer</summary>

Use a span event for a point-in-time milestone in an existing operation. Use a child span for work with an independent duration or context that you want to inspect separately.

</details>

## Advanced scenarios

### 5. Why might an error be missing from traces even when the service logs it?

<details>
<summary>Show answer</summary>

Propagation may be broken, the trace may be sampled out, or instrumentation may never record the error. Check trace IDs, sampler decisions and the collector pipeline as separate steps.

</details>

### 6. What is the operational trade-off between head and tail sampling?

<details>
<summary>Show answer</summary>

Head sampling decides early without later outcomes. Tail sampling can retain interesting completed traces, but it needs trace data to reach a decision point and increases buffering and coordination costs.

</details>

## Official references

- [OpenTelemetry documentation](https://opentelemetry.io/docs/)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
