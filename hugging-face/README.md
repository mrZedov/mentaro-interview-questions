# Hugging Face interview questions

[Browse Mentaro’s web question catalog](https://www.mentaro.app/interview-questions) · [Try the Hugging Face practice demo](https://www.mentaro.app/demo?topic=hugging-face&utm_source=github)

## Foundations

### 1. How does a tokenizer differ from model weights?

<details>
<summary>Show answer</summary>

A tokenizer converts input into model-compatible token IDs and back to text where applicable. Weights define learned numerical parameters. Load a tokenizer compatible with the model and its expected input format.

**Explanation:** The model sees integers, not the original words. A different tokenizer can assign the same integer to a different fragment and still produce inputs of valid shape. Pin the tokenizer and any chat template with the checkpoint to preserve that mapping.

</details>

### 2. What should you read in a model card before selecting a checkpoint?

<details>
<summary>Show answer</summary>

Check intended use, license, evaluation setup, training disclosures and limitations. A score on one benchmark does not establish fit for your workload or permission for redistribution.

</details>

## Applied

### 3. Why pin a model revision instead of loading the latest branch in production?

<details>
<summary>Show answer</summary>

A branch can change weights, tokenizer or metadata without a deployment. Pin a reviewed revision, record artifact dependencies and license requirements, and deliberately test upgrades.

</details>

### 4. Why test a text-generation model with its intended chat template?

<details>
<summary>Show answer</summary>

The model may expect particular role markers and special tokens. A mismatched template can degrade responses even when the tokenizer and weights load successfully.

</details>

## Advanced scenarios

### 5. How would you find out whether padding is driving slow inference?

<details>
<summary>Show answer</summary>

Measure token lengths, batch throughput and per-request latency. Compare dynamic padding or length bucketing with a representative traffic mix. Check whether the shorter compute time is worth the additional queueing.

</details>

### 6. What should an evaluation check before replacing a model with a quantized version?

<details>
<summary>Show answer</summary>

Compare output quality on representative cases, including edge cases, as well as memory, latency and throughput. Quantization effects depend on the model, workload and hardware.

</details>

## Official references

- [Hugging Face documentation](https://huggingface.co/docs)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).

[Try the Hugging Face practice demo](https://www.mentaro.app/demo?topic=hugging-face&utm_source=github)
