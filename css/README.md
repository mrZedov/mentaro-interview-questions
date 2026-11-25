# CSS interview questions

[Browse Mentaro’s web question catalog](https://www.mentaro.app/interview-questions) · [Start interactive practice on Mentaro](https://www.mentaro.app/login?mode=signup&redirect=/onboarding)

## Foundations

### 1. How does the cascade decide between competing CSS declarations?

<details>
<summary>Show answer</summary>

The cascade compares origin, importance and layers before specificity; scoping proximity and source order can break remaining ties. A more specific selector is not always the winner.

</details>

### 2. What does box-sizing: border-box change?

<details>
<summary>Show answer</summary>

The declared width or height includes padding and border, but not margin. With content-box, padding and border are added outside the declared content size.

</details>

## Applied

### 3. Why can a long label make a flex item overflow its container?

<details>
<summary>Show answer</summary>

The automatic minimum size can prevent the flex item from shrinking below its content. Allow shrinkage with min-width: 0 where appropriate and decide whether to wrap or truncate the label.

</details>

### 4. When is a container query better than a media query?

<details>
<summary>Show answer</summary>

Use a container query when a component should adapt to the space allocated by its parent. Use a media query when the decision depends on the viewport or a user preference.

</details>

## Advanced scenarios

### 5. How would you debug a modal that appears behind a neighboring panel despite a large z-index?

<details>
<summary>Show answer</summary>

Inspect stacking contexts created by ancestors. A child cannot escape its context just by increasing z-index. Use an appropriate top-layer primitive or restructure where the modal is mounted.

</details>

### 6. How would you diagnose visible layout shifts when web fonts load?

<details>
<summary>Show answer</summary>

Record the layout shifts and identify their affected elements. Compare fallback and final font metrics, reserve predictable geometry and choose loading behavior that fits the content.

</details>

## Official references

- [CSS documentation](https://developer.mozilla.org/en-US/docs/Web/CSS)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
