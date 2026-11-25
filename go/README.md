# Go interview questions

[Browse Mentaro’s web question catalog](https://www.mentaro.app/interview-questions) · [Try the Go practice demo](https://www.mentaro.app/demo?topic=go&utm_source=github)

## Foundations

### 1. Why can a Go error interface compare unequal to nil even when its pointer is nil?

<details>
<summary>Show answer</summary>

An interface is nil only if both its dynamic type and value are absent. Assigning a typed nil pointer gives it a dynamic type. Return a literal nil when there is no error.

**Explanation:** A nil pointer converted to error still carries its concrete type. A caller testing err != nil therefore enters the error path. The right repair is at the function returning the error, not a reflection-based nil check in every caller.

**Example:**

```go
package main

import "fmt"

type failure struct{}

func (*failure) Error() string { return "failed" }

func main() {
	var pointer *failure
	var err error = pointer
	fmt.Println(pointer == nil, err == nil)
}
```

The program prints true false.

</details>

### 2. How are a Go slice and array different when passed to a function?

<details>
<summary>Show answer</summary>

An array is copied as a value. A slice header is copied but can still point to the same backing array, so modifying shared elements can change what the caller sees.

</details>

## Applied

### 3. How do you make a goroutine stop when nobody reads its result?

<details>
<summary>Show answer</summary>

Propagate cancellation with context and select between sending the result and cancellation. Define who owns completion and test the path where consumers exit early.

</details>

### 4. Who should close a channel with multiple Go producers?

<details>
<summary>Show answer</summary>

Use a coordinator that knows all sends have finished, for example after waiting for every producer. A receiver generally cannot prove that a sender will not send after closure.

</details>

## Advanced scenarios

### 5. How would you distinguish goroutine leaks from an ordinary surge in requests?

<details>
<summary>Show answer</summary>

Compare goroutine count and stack profiles before and after similar loads. Persistent groups blocked at the same send, receive or I/O boundary suggest missing cancellation or ownership, not just more throughput.

</details>

### 6. What does a clean Go race-detector run establish?

<details>
<summary>Show answer</summary>

It did not report a race on the executed paths. It is not proof of race freedom on unexecuted paths or under untested workloads. Include concurrent tests and still reason about ownership and synchronization.

</details>

## Official references

- [Go documentation](https://go.dev/doc/)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).

[Try the Go practice demo](https://www.mentaro.app/demo?topic=go&utm_source=github)
