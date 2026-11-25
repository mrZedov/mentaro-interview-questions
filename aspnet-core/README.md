# ASP.NET Core interview questions

[Browse Mentaro’s web question catalog](https://www.mentaro.app/interview-questions) · [Start interactive practice on Mentaro](https://www.mentaro.app/login?mode=signup&redirect=/onboarding)

## Foundations

### 1. Why does middleware registration order matter in ASP.NET Core?

<details>
<summary>Show answer</summary>

Requests enter middleware in registration order, and responses unwind in reverse. Middleware may short-circuit the pipeline. Register authentication before authorization and test both successful and rejected requests.

</details>

### 2. Which lifetimes does the ASP.NET Core service container offer?

<details>
<summary>Show answer</summary>

Transient services are created on resolution, scoped services are shared within a scope, and singleton services are shared across the container. Avoid making a singleton retain a scoped service.

</details>

## Applied

### 3. How should a BackgroundService use a scoped database context?

<details>
<summary>Show answer</summary>

Create and dispose a dependency injection scope per unit of work. Resolve the context from that scope rather than keeping a scoped dependency in the singleton hosted service.

</details>

### 4. How do you test authorization rules without deploying an ASP.NET Core application?

<details>
<summary>Show answer</summary>

Host the real application with WebApplicationFactory, replace authentication in the test host, and exercise endpoints over its test client. Include an authenticated user who lacks the required permission.

</details>

## Advanced scenarios

### 5. Why can an ASP.NET Core service with low CPU still have poor request latency?

<details>
<summary>Show answer</summary>

Blocking calls can occupy thread-pool threads while work queues. Check thread-pool queue length and stacks, remove sync-over-async paths where possible, and distinguish starvation from downstream latency.

</details>

### 6. Why might an application generate HTTP callback URLs behind an HTTPS proxy?

<details>
<summary>Show answer</summary>

The app may see only its internal HTTP connection. Configure forwarded headers for known proxies, run that middleware before URL generation, and verify that untrusted clients cannot spoof the forwarded scheme.

</details>

## Official references

- [ASP.NET Core documentation](https://learn.microsoft.com/aspnet/core/)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
