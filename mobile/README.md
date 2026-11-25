# Mobile engineering interview questions

[Browse Mentaro’s web question catalog](https://www.mentaro.app/interview-questions) · [Try the Mobile engineering practice demo](https://www.mentaro.app/demo?topic=mobile&utm_source=github)

## Foundations

### 1. Why is network reachability not proof that a mobile request will succeed?

<details>
<summary>Show answer</summary>

An interface can be connected while the server, DNS or authentication fails. Treat reachability as a hint and handle actual request outcomes with deadlines and clear offline states.

**Explanation:** A device may have Wi-Fi but be trapped behind a captive portal, or the access token may have expired. Keep connectivity, request errors and authentication failures separate in the UI. If you offer retry for writes, account for uncertainty about whether the first write succeeded.

</details>

[Try the Mobile engineering practice demo](https://www.mentaro.app/demo?topic=mobile&utm_source=github)

### 2. What is the distinction between an offline cache and the local source of truth?

<details>
<summary>Show answer</summary>

An offline cache is a possibly stale copy. A local source of truth defines what the UI reads and writes while sync reconciles it with the server. Specify freshness and conflicts explicitly.

</details>

[Try the Mobile engineering practice demo](https://www.mentaro.app/demo?topic=mobile&utm_source=github)

## Applied

### 3. How should a screen resume a query after the mobile process is recreated?

<details>
<summary>Show answer</summary>

Persist or reconstruct the durable inputs, such as the item ID and filters. Recreate transient tasks from those inputs and do not rely on an in-memory callback surviving process death.

</details>

[Try the Mobile engineering practice demo](https://www.mentaro.app/demo?topic=mobile&utm_source=github)

### 4. How do you design a push notification tap for a signed-out user?

<details>
<summary>Show answer</summary>

Resolve the destination without assuming a live session. Authenticate if needed, then revalidate access and handle missing content. Do not put private content or credentials in the deep-link URL.

</details>

[Try the Mobile engineering practice demo](https://www.mentaro.app/demo?topic=mobile&utm_source=github)

## Advanced scenarios

### 5. How would you merge edits made offline on two devices?

<details>
<summary>Show answer</summary>

Store a stable record ID and enough version context to detect conflicts. Pick a rule per field or operation, preserve changes that cannot be merged safely, and test out-of-order sync and retries.

</details>

[Try the Mobile engineering practice demo](https://www.mentaro.app/demo?topic=mobile&utm_source=github)

### 6. How can you detect a startup regression that affects only low-end devices?

<details>
<summary>Show answer</summary>

Measure cold-start milestones by device class and release, then reproduce on representative hardware. Separate first-frame time from interactive readiness and inspect initialization on the critical path.

</details>

[Try the Mobile engineering practice demo](https://www.mentaro.app/demo?topic=mobile&utm_source=github)

## Official references

- [Mobile engineering documentation](https://developer.android.com/topic/architecture)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
