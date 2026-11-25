# AWS interview questions

[Browse Mentaro’s web question catalog](https://www.mentaro.app/interview-questions) · [Try the AWS practice demo](https://www.mentaro.app/demo?topic=aws&utm_source=github)

## Foundations

### 1. What is the difference between an AWS Region and an Availability Zone?

<details>
<summary>Show answer</summary>

A Region is a geographic area. An Availability Zone is an isolated infrastructure location within a Region. Deploying across zones mitigates a zone failure but does not provide regional disaster recovery.

**Explanation:** Deploying application replicas in two zones does not help much if both depend on a database available in only one zone. Follow each dependency through the failure scenario. Regional recovery also needs data, traffic routing and a tested cutover, not just another copy of the compute service.

</details>

### 2. What is the difference between an IAM role and a policy?

<details>
<summary>Show answer</summary>

A role is an assumable identity. Policies express rules or permissions. A role trust policy describes who may assume it; identity policies describe what the resulting session can do, subject to other controls.

</details>

## Applied

### 3. How should an SQS consumer handle delivery of the same job twice?

<details>
<summary>Show answer</summary>

Treat delivery and processing as separate. Store a stable job ID with the effect or make the effect independently idempotent. Delete the message after completion and set or extend visibility to match processing time.

</details>

### 4. How do you detect a Lambda consumer that cannot keep up with an SQS queue?

<details>
<summary>Show answer</summary>

Watch the age of the oldest message, backlog, errors and throttles together. Tune concurrency and batching against downstream capacity, and route repeatedly failing messages to a monitored dead-letter queue.

</details>

## Advanced scenarios

### 5. How would you design an S3 upload flow for large user files?

<details>
<summary>Show answer</summary>

Authorize the operation in the application and issue narrowly scoped short-lived upload permissions. Use multipart upload where useful, verify the completed object before processing, and expire abandoned multipart uploads.

</details>

### 6. How would you rehearse recovery of an AWS-hosted database?

<details>
<summary>Show answer</summary>

Restore into an isolated environment, measure restoration time, and check whether recovered data meets the recovery-point objective. Verify application credentials, migrations and critical queries before planning traffic cutover.

</details>

## Official references

- [AWS documentation](https://docs.aws.amazon.com/)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).

[Try the AWS practice demo](https://www.mentaro.app/demo?topic=aws&utm_source=github)
