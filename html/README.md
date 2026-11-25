# HTML interview questions

[Browse Mentaro’s web question catalog](https://www.mentaro.app/interview-questions) · [Start interactive practice on Mentaro](https://www.mentaro.app/login?mode=signup&redirect=/onboarding)

## Foundations

### 1. When should an interface use a button instead of a link?

<details>
<summary>Show answer</summary>

Use a link for navigation to a destination and a button for an action. Native controls provide different keyboard behavior and semantics that styling alone does not reproduce.

</details>

### 2. Why give a form control a name even when it already has an ID?

<details>
<summary>Show answer</summary>

The ID identifies the element in the document and can associate it with a label. The name identifies a successful control in submitted form data.

</details>

## Applied

### 3. How do you associate a visible label and a validation message with a form input?

<details>
<summary>Show answer</summary>

Link the label to the input with matching for and id values. Reference descriptive or error text via aria-describedby, expose invalid state as appropriate, and verify with keyboard and assistive technology.

</details>

### 4. When should alternative text for an image be empty?

<details>
<summary>Show answer</summary>

Use empty alt text for a decorative image or when adjacent content already provides the same meaning. Give meaningful images concise equivalent text; for a linked image consider the action or destination.

</details>

## Advanced scenarios

### 5. Why might changing the visual order of a responsive form confuse keyboard users?

<details>
<summary>Show answer</summary>

Keyboard focus generally follows document order, not the apparent order created by layout styles. Keep DOM order logical and test the reading and focus sequences at different viewport sizes.

</details>

### 6. How would you verify that a dialog is usable without a mouse?

<details>
<summary>Show answer</summary>

Open it via keyboard, check initial focus and navigation inside it, dismiss it and verify focus returns to the appropriate trigger. Ensure background content is not inadvertently interactive for a modal.

</details>

## Official references

- [HTML documentation](https://html.spec.whatwg.org/)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
