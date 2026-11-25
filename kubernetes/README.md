# Kubernetes interview questions

[Explore this topic on Mentaro](https://www.mentaro.app/interview-questions/kubernetes) · [Start interactive practice on Mentaro](https://www.mentaro.app/login?mode=signup&redirect=/onboarding)

## Foundations

### 1. Why is a Pod rather than a container the scheduling unit?

<details>
<summary>Show answer</summary>

Containers in one Pod are placed together and share a network namespace. Use this for tightly coupled processes that need to run together, not as a substitute for independent scaling.

**Explanation:** Containers in a Pod are placed together on one node and share its Pod networking identity. They are useful for tightly coupled helpers; independently scaled components usually belong in separate workload objects.

</details>

### 2. What does a Deployment add on top of a ReplicaSet?

<details>
<summary>Show answer</summary>

It manages ReplicaSets for successive versions of a Pod template, coordinates replacement and exposes rollout state. A ReplicaSet primarily maintains the desired count for one template.

**Explanation:** A Deployment coordinates the transition between old and new ReplicaSets when the Pod template changes. Scaling a ReplicaSet alone changes the number of matching replicas but does not manage a controlled version rollout.

</details>

### 3. What problem does a Service solve when Pods restart?

<details>
<summary>Show answer</summary>

It gives clients a stable name and endpoint abstraction while selected Pods come and go. Traffic normally goes only to eligible endpoints, not to an arbitrary Pod IP remembered by a caller.

**Explanation:** A selector-backed Service maps a stable address to current endpoints rather than asking clients to track Pod IPs. Check readiness and selector labels if the address resolves but has nowhere to send traffic.

</details>

### 4. How do a resource request and limit differ?

<details>
<summary>Show answer</summary>

Requests inform scheduling and resource allocation. Limits constrain use at runtime; CPU can be throttled while excessive memory use may lead to termination.

**Explanation:** A CPU request is used for placement and influences CPU share under contention. A CPU limit can throttle the container even when its node has spare CPU. Memory is different: past the enforced memory limit, the container can be OOM-killed rather than throttled like CPU.

</details>

### 5. When would you choose a StatefulSet over a Deployment?

<details>
<summary>Show answer</summary>

Use a StatefulSet when replicas need stable identities or per-replica persistent storage. It does not automatically replicate application data or make a database highly available.

**Explanation:** StatefulSet is a fit when a replica needs stable identity or its own persistent volume across replacement. It does not create database replication or quorum automatically; the application still has to manage membership and data.

</details>

## Applied

### 6. How do readiness and liveness probes affect a Pod differently?

<details>
<summary>Show answer</summary>

Readiness affects eligibility for Service traffic. Repeated liveness failures can restart a container. Do not use liveness to restart every instance just because a shared downstream service is temporarily down.

**Explanation:** A temporary overload may justify taking a Pod out of traffic without restarting it. Avoid a liveness probe that fails whenever a shared dependency is slow: restarting all consumers can make recovery harder.

</details>

### 7. What does a startup probe change for a slow-starting workload?

<details>
<summary>Show answer</summary>

Readiness and liveness probes do not run until the startup probe succeeds. This gives startup its own allowance without making recovery from later failures unnecessarily slow.

**Explanation:** Until the startup probe succeeds, Kubernetes does not run readiness or liveness probes for that container. Budget enough attempts for legitimate startup, but still ensure a permanently stalled process eventually fails.

</details>

### 8. Why might an autoscaler add Pods that cannot start?

<details>
<summary>Show answer</summary>

Scaling creates demand for more replicas but does not guarantee capacity. Inspect scheduling events, requested resources, placement rules and node capacity when replicas remain pending.

**Explanation:** A workload autoscaler can request more replicas without making any node suitable for them. Check scheduler events and capacity, including resource requests, placement constraints and whether node scaling is available.

</details>

### 9. How can a ConfigMap update reach a running application?

<details>
<summary>Show answer</summary>

Mounted files generally update eventually, but the application must reload them and subPath mounts do not receive updates. Values loaded as environment variables need a new Pod.

**Explanation:** Environment variables are set at process startup. A standard mounted ConfigMap volume can update after a delay, but the application must reread it; a subPath mount does not receive those updates.

</details>

### 10. What can a PodDisruptionBudget protect against?

<details>
<summary>Show answer</summary>

It limits supported voluntary evictions such as a node drain. It does not prevent unexpected node failure or replace a Deployment's own rollout availability configuration.

**Explanation:** A budget limits voluntary eviction requests such as those used by a node drain. It does not prevent a node from failing, and it does not replace the rollout strategy that limits unavailable replicas during a Deployment update.

</details>

### 11. How can topology spread constraints improve placement even when a Deployment has several replicas?

<details>
<summary>Show answer</summary>

Replica count alone does not guarantee distribution. Use constraints over domains such as zones or nodes, choose how strictly to enforce skew, and check whether capacity and labels allow the desired placements.

**Explanation:** Pick a topology key that reflects your failure domain, then verify nodes actually have that label and sufficient capacity. Strict skew can leave Pods pending; a soft preference can leave replicas clustered under pressure.

</details>

## Advanced scenarios

### 12. A Pod stays Pending after deployment. What do you check first?

<details>
<summary>Show answer</summary>

Read Pod conditions and events. Unschedulable usually means capacity or placement constraints, while a scheduled Pod can still wait for volume mounting or image retrieval.

**Explanation:** Read scheduling events before container logs: an unscheduled Pod has no running container to inspect. If it was scheduled, distinguish image, volume and container-startup problems from a placement failure.

</details>

### 13. How do you diagnose a container in CrashLoopBackOff?

<details>
<summary>Show answer</summary>

Check the last termination reason and exit code, then read logs from the previous container. Compare recent config and probe changes; the backoff itself is not the root cause.

**Explanation:** Inspect the last termination reason and previous container logs. An OOM kill, failing liveness probe and immediate process exit require different fixes even though all can present as restart backoff.

</details>

### 14. Why can a Service have no endpoints even if Pods are running?

<details>
<summary>Show answer</summary>

The selector may not match the Pods or none may be ready. Check labels, namespace and EndpointSlices before assuming the network is broken.

**Explanation:** Running does not imply ready or selected. Confirm the Service and Pods are in the expected namespace, labels match, and readiness is positive; inspect EndpointSlices to distinguish no match from an unavailable endpoint.

</details>

### 15. How do maxSurge and maxUnavailable constrain a rolling update?

<details>
<summary>Show answer</summary>

Surge allows temporary extra replicas; unavailable limits how far availability can fall relative to desired replicas. Account for capacity and time to readiness when selecting values.

**Explanation:** With four desired replicas, maxSurge 1 and maxUnavailable 0 let the controller bring a replacement up before reducing available capacity. If the new Pod cannot become ready, keeping availability may cause the rollout to stall.

</details>

### 16. What do you plan for when terminating a Pod during a rollout?

<details>
<summary>Show answer</summary>

Allow for traffic routing to converge, stop accepting new work and drain in-flight requests within the grace period. Test real shutdown timing rather than assuming traffic stops immediately.

**Explanation:** Set a termination grace period long enough for the drain you actually support. Endpoint changes propagate asynchronously, so handle a short overlap and separately decide how to finish or close long-lived connections.

</details>

### 17. Why can a pod be scheduled successfully but later be CPU-throttled?

<details>
<summary>Show answer</summary>

Scheduling considers requested CPU against available capacity. A CPU limit caps execution time even when a workload is bursting. Compare throttling, usage and request latency before changing requests or limits.

**Explanation:** CPU requests drive placement; CPU limits control runtime bandwidth. Compare throttled time and user-visible latency, then change one setting at a time so you can tell improved placement from improved runtime headroom.

</details>

## Official references

- [Kubernetes: Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)
- [Kubernetes: Probes](https://kubernetes.io/docs/concepts/configuration/liveness-readiness-startup-probes/)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
