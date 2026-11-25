# Google Cloud interview questions

[Browse Mentaro’s web question catalog](https://www.mentaro.app/interview-questions) · [Try the Google Cloud practice demo](https://www.mentaro.app/demo?topic=gcp&utm_source=github)

## Foundations

### 1. What is the distinction between a service account and a user account in Google Cloud?

<details>
<summary>Show answer</summary>

A service account identifies a workload. Assign only the permissions it needs and use short-lived credentials where possible instead of storing user credentials or long-lived keys.

**Explanation:** For example, an export worker can run with an identity allowed to write to one bucket without acting as its operator. Separately control who may deploy or impersonate that identity and what actions the identity can take after deployment.

</details>

### 2. What does a Google Cloud project provide as a boundary?

<details>
<summary>Show answer</summary>

It groups resources for access, service enablement, billing and quotas. Organization and folder policies can apply to it; resources in different projects may still communicate when explicitly configured.

</details>

## Applied

### 3. How do you prevent redelivered Pub/Sub messages from repeating business effects?

<details>
<summary>Show answer</summary>

Make the consumer idempotent using a stable event or business identifier, then acknowledge after the effect is durable. Treat topic delivery guarantees and external side effects as separate concerns.

</details>

### 4. How can a workload on Google Cloud avoid shipping a service-account key?

<details>
<summary>Show answer</summary>

Attach the intended service account or use workload identity federation where appropriate. Use short-lived credentials provided by the environment, and keep permissions limited to what the workload needs.

</details>

## Advanced scenarios

### 5. How can Cloud Run scaling overload a database before CPU looks saturated?

<details>
<summary>Show answer</summary>

Scaling can increase the total connections across instances. Bound per-instance pools and service concurrency with the database budget in mind, and measure connection wait time and rejection rates.

</details>

### 6. How would you diagnose a Cloud Run rollout that fails only on new instances?

<details>
<summary>Show answer</summary>

Compare revision configuration, startup logs, identity and secret access. Separate startup failures from request-time failures, keep traffic on healthy revisions and confirm the corrected revision before increasing traffic.

</details>

## Official references

- [Google Cloud documentation](https://cloud.google.com/docs)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).

[Try the Google Cloud practice demo](https://www.mentaro.app/demo?topic=gcp&utm_source=github)
