# NestJS interview questions

[Explore this topic on Mentaro](https://www.mentaro.app/interview-questions/nestjs) · [Try the NestJS practice demo](https://www.mentaro.app/demo?topic=nestjs&utm_source=github)

## Foundations

### 1. What makes a provider in one NestJS module available to another module?

<details>
<summary>Show answer</summary>

Export the provider from its declaring module and import that module in the consumer. Registering the same class separately in the consumer can create a different instance instead of reusing the exported provider.

</details>

### 2. Why are TypeScript interfaces not usable as injection tokens at runtime?

<details>
<summary>Show answer</summary>

Interfaces are erased. Use a class, symbol or string as a runtime token, register a provider with that token and inject it explicitly where necessary.

</details>

### 3. Where should domain rules live: in a controller or a provider?

<details>
<summary>Show answer</summary>

Let controllers translate transport requests into calls and responses. Put reusable business rules in a service or domain component so other entry points can use them without HTTP coupling.

</details>

### 4. How does a guard differ from a pipe?

<details>
<summary>Show answer</summary>

A guard decides whether execution may continue based on context, typically for access control. A pipe validates or transforms arguments before the handler receives them.

**Explanation:** Guards run before the route pipes, so a guard cannot rely on a parameter having already been converted by a pipe. A common split is to check authentication or broad permissions in the guard, then enforce resource ownership in the application layer after validating the input.

</details>

### 5. When would you use an exception filter instead of an interceptor?

<details>
<summary>Show answer</summary>

A filter translates selected uncaught exceptions into transport responses. An interceptor surrounds handler execution and can time calls or transform successful results as well as handle errors.

</details>

## Applied

### 6. Why might a guard see raw parameters before a validation pipe changes them?

<details>
<summary>Show answer</summary>

Guards run before pipes. If an access decision depends on an input, validate it independently in the guard or move the resource-specific decision to a service with validated arguments.

</details>

### 7. When does request scope affect more than one provider?

<details>
<summary>Show answer</summary>

A consumer that depends on a request-scoped provider becomes request-scoped through the dependency chain. Prefer explicit context parameters for simple use cases when per-request construction adds cost.

</details>

### 8. What is the benefit of a dynamic module?

<details>
<summary>Show answer</summary>

It lets a module produce providers and exports from configuration supplied at import time. For example, a module can construct a shared client with different options without duplicating registration logic.

</details>

### 9. What problem does useExisting solve compared with useClass?

<details>
<summary>Show answer</summary>

UseExisting aliases a token to an already registered provider. UseClass tells the container to instantiate the class for the configured provider, which may create a second instance.

</details>

### 10. Why might a global exception filter not affect an error caught inside a controller?

<details>
<summary>Show answer</summary>

Filters handle exceptions that reach Nest exception handling. If the controller catches the exception and returns normally, there is no uncaught exception for the filter to translate.

</details>

## Advanced scenarios

### 11. How do you diagnose a provider dependency that Nest cannot resolve?

<details>
<summary>Show answer</summary>

Check the reported parameter index and token, then follow the declaring module's imports and exports. Verify the token is identical at runtime, especially for symbols and modules reached through multiple package copies.

</details>

### 12. What should you try before resolving a service cycle with forwardRef?

<details>
<summary>Show answer</summary>

Look for a shared responsibility that belongs in a third provider or an orchestration layer. Forward references can unblock unavoidable cycles but make initialization and testing harder to reason about.

</details>

### 13. How do you test an HTTP endpoint while replacing a downstream service?

<details>
<summary>Show answer</summary>

Build a testing module and override the same token used in production. Initialize the application with its real global pipes and filters so the HTTP test exercises routing and validation rather than only calling controller methods.

</details>

### 14. Why is a request-specific property on a default-scoped provider unsafe?

<details>
<summary>Show answer</summary>

Default providers are normally shared across requests. Concurrent requests can overwrite the property; pass the value as an argument or use a properly scoped context.

</details>

### 15. How do you ensure a server shutdown closes consumers and database connections?

<details>
<summary>Show answer</summary>

Register the appropriate lifecycle hooks and enable signal-driven shutdown hooks where required. Stop new work, drain what is in flight and close shared resources in a tested order.

</details>

### 16. Why can a validation pipe not guarantee that an email address is unique?

<details>
<summary>Show answer</summary>

Validation can check input shape, but a separate uniqueness lookup races with concurrent writes. Enforce uniqueness in storage and translate the conflict into the transport response.

</details>

## Official references

- [NestJS: Modules](https://docs.nestjs.com/modules)
- [NestJS: Request lifecycle](https://docs.nestjs.com/faq/request-lifecycle)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).

[Try the NestJS practice demo](https://www.mentaro.app/demo?topic=nestjs&utm_source=github)
