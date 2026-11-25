# CSS interview questions

[Browse Mentaro’s web question catalog](https://www.mentaro.app/interview-questions) · [Start interactive practice on Mentaro](https://www.mentaro.app/login?mode=signup&redirect=/onboarding)

## Foundations

### 1. How does the cascade decide between competing CSS declarations?

<details>
<summary>Show answer</summary>

The cascade compares origin, importance and layers before specificity; scoping proximity and source order can break remaining ties. A more specific selector is not always the winner.

**Explanation:** For normal author declarations, later declared layers outrank earlier layers, and unlayered rules outrank layered ones. The order is reversed for important declarations within layers. Inspect which layer wins before reaching for a stronger selector.

**Example:**

```css
@layer defaults, theme;
@layer defaults { #status { color: gray; } }
@layer theme { .status { color: green; } }
```

An element with both id and class set to status is green despite the ID selector having higher specificity.

</details>

### 2. What does box-sizing: border-box change?

<details>
<summary>Show answer</summary>

The declared width or height includes padding and border, but not margin. With content-box, padding and border are added outside the declared content size.

**Explanation:** With a width of 200px, padding of 16px per side and a border of 2px per side, the content is 164px wide under border-box. Margins still affect the space the box takes in its parent.

**Example:**

```css
.panel {
  box-sizing: border-box;
  width: 200px;
  padding: 16px;
  border: 2px solid;
}
```

</details>

## Applied

### 3. Why can a long label make a flex item overflow its container?

<details>
<summary>Show answer</summary>

The automatic minimum size can prevent the flex item from shrinking below its content. Allow shrinkage with min-width: 0 where appropriate and decide whether to wrap or truncate the label.

**Explanation:** In a horizontal flex row, an unbroken label can have a min-content width larger than the available space. Apply the minimum override to the flex item itself; applying ellipsis only to a descendant may not allow the item to shrink.

**Example:**

```css
.row { display: flex; }
.label {
  min-width: 0;
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
}
```

</details>

### 4. When is a container query better than a media query?

<details>
<summary>Show answer</summary>

Use a container query when a component should adapt to the space allocated by its parent. Use a media query when the decision depends on the viewport or a user preference.

**Explanation:** A product card can be narrow in a sidebar and wide in the main grid at the same viewport size. Put the containment context on an ancestor of the element you want to style, and switch the card layout when that ancestor has enough space.

**Example:**

```css
.slot { container-type: inline-size; }
@container (width >= 30rem) {
  .card { display: grid; grid-template-columns: 1fr 2fr; }
}
```

</details>

## Advanced scenarios

### 5. How would you debug a modal that appears behind a neighboring panel despite a large z-index?

<details>
<summary>Show answer</summary>

Inspect stacking contexts created by ancestors. A child cannot escape its context just by increasing z-index. Use an appropriate top-layer primitive or restructure where the modal is mounted.

**Explanation:** In DevTools, walk up both ancestor chains and look for properties such as transform or opacity that create contexts. Distinguish stacking from clipping: moving an element above its siblings will not fix overflow clipping. A modal dialog opened with showModal uses the top layer.

</details>

### 6. How would you diagnose visible layout shifts when web fonts load?

<details>
<summary>Show answer</summary>

Record the layout shifts and identify their affected elements. Compare fallback and final font metrics, reserve predictable geometry and choose loading behavior that fits the content.

**Explanation:** Throttle the font request and compare the text before and after the swap. A fallback with a different width can change line breaks and move everything below it. A closer fallback or font metric overrides can reduce movement; font-display alone does not make the two fonts occupy the same space.

</details>

## Official references

- [CSS documentation](https://developer.mozilla.org/en-US/docs/Web/CSS)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
