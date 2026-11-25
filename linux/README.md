# Linux interview questions

[Browse Mentaro’s web question catalog](https://www.mentaro.app/interview-questions) · [Start interactive practice on Mentaro](https://www.mentaro.app/login?mode=signup&redirect=/onboarding)

## Foundations

### 1. How does a file descriptor differ from a filename?

<details>
<summary>Show answer</summary>

A file descriptor is a process-local integer referring to an open kernel object. A filename is a directory entry used for lookup. An open descriptor can remain usable after that name is removed.

**Explanation:** When a log file is renamed and replaced, a process that still holds the original descriptor can continue writing to the old file. A rotation procedure may need to make that process reopen the path; creating a file with the old name does not redirect existing descriptors.

</details>

### 2. What is the difference between a process and a thread on Linux?

<details>
<summary>Show answer</summary>

Threads in a process normally share an address space and resources such as file descriptors, while retaining individual scheduling state and stacks. Separate processes normally have distinct address spaces.

</details>

## Applied

### 3. Why can disk usage remain high after deleting a large log file?

<details>
<summary>Show answer</summary>

A process may still hold an open descriptor to the unlinked file. Find the owning process and arrange a safe reopen or rotation. Unlinking removes the name, not every reference.

</details>

### 4. Why is low free memory not always a problem on Linux?

<details>
<summary>Show answer</summary>

The kernel uses spare memory for reclaimable caches. Look at available memory and pressure or swapping, not free memory alone. Account for container limits as well as host totals.

</details>

## Advanced scenarios

### 5. How would you investigate high load average on a host with idle CPU?

<details>
<summary>Show answer</summary>

Linux load can include tasks in uninterruptible sleep as well as runnable tasks. Inspect task states, I/O wait, device latency and pressure signals before assuming the system needs more CPU.

</details>

### 6. How do you identify whether one workload is causing disk contention?

<details>
<summary>Show answer</summary>

Correlate device latency and queue depth with process or cgroup I/O and workload timing. A high byte count alone may be harmless; operations that inflate tail latency are more informative.

</details>

## Official references

- [Linux documentation](https://www.kernel.org/doc/html/latest/)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
