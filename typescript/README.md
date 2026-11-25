# TypeScript interview questions

[Explore this topic on Mentaro](https://www.mentaro.app/interview-questions/typescript) · [Start interactive practice on Mentaro](https://www.mentaro.app/login?mode=signup&redirect=/onboarding)

## Foundations

### 1. When should a function accept unknown rather than any?

<details>
<summary>Show answer</summary>

Use unknown when callers can supply arbitrary input but the implementation must check it before using it. For example, an event decoder must verify that the input is a non-null object and validate the relevant fields. Any permits unchecked property access and can propagate an unchecked type to callers.

</details>

### 2. What does it mean for TypeScript to use structural typing?

<details>
<summary>Show answer</summary>

Compatibility generally depends on available members and their types, not on whether two declarations share a name. An object with an ID and a timestamp can be passed to a function that only requires the ID. A fresh object literal can still trigger excess-property checks, and private or protected class members impose additional constraints.

</details>

### 3. When can you use a type alias where an interface is not enough?

<details>
<summary>Show answer</summary>

An alias can name a union such as Loading | Ready | Failed, a primitive, or a mapped type. Interfaces describe object shapes and can be reopened through declaration merging. For a plain object contract either can work; choose based on whether you want merging or need to name a type that is not an object shape.

</details>

### 4. With strictNullChecks enabled, what would you return from a lookup that might miss?

<details>
<summary>Show answer</summary>

Include the possibility of absence in the return type, such as User | undefined. The caller must handle the missing case before dereferencing the result. A non-null assertion removes the type error, not the possibility of a miss, so reserve it for a separately established invariant.

</details>

### 5. Why can an object property become string even if you initialized it with one string literal?

<details>
<summary>Show answer</summary>

A mutable property may later be assigned a different string, so inference can widen its type even when the object variable is const. A literal annotation or an as const assertion can keep the literal type. As const also marks literal properties readonly in the type system; it does not freeze the object at runtime.

</details>

## Applied

### 6. How do you make adding a new result variant cause a compile error in old handlers?

<details>
<summary>Show answer</summary>

Give each variant a shared discriminant with a different literal value. Switch on that discriminant and require the remaining value to be never in the fallback. If a new variant is not handled, it is still a possible value in the fallback and will not be assignable to never.

</details>

### 7. Why use a generic rather than a union for a function that picks one property from an object?

<details>
<summary>Show answer</summary>

A generic can express the relationship between the key and the return type. With T for the object and K constrained to keyof T, the return type T[K] refers to exactly that key. Returning a union of all property types would make the caller narrow a result whose concrete type was already known at the call site.

</details>

### 8. When would you use satisfies instead of explicitly annotating a configuration object?

<details>
<summary>Show answer</summary>

Use satisfies to check that an expression conforms to a contract without replacing its result type with the contract. For example, check a handler map against a required set of events while retaining the inferred types of its specific entries. It is a compile-time check, not a validator for configuration loaded from a file.

</details>

### 9. When is an optional property different from a required property whose value can be undefined?

<details>
<summary>Show answer</summary>

A required property must be present even if its type allows undefined. An optional property can be absent, which matters to Object.keys, object spread and the in operator. With exactOptionalPropertyTypes, setting an optional property to undefined explicitly is rejected unless undefined is part of its declared value type.

</details>

## Advanced scenarios

### 10. A response is assigned to User after JSON parsing. What evidence do you need before using it?

<details>
<summary>Show answer</summary>

Check the HTTP result and treat the decoded value as untrusted. Validate the fields and nested shapes that the application relies on before returning a User. An annotation or type assertion does not inspect JSON; an invalid payload otherwise reaches consumers as though it had been verified.

</details>

### 11. Can a validator declared as input is Order still cause a runtime failure?

<details>
<summary>Show answer</summary>

Yes. TypeScript does not prove that the declared predicate covers every required field. A check for a single ID could promise too much about nested items or status. Test invalid as well as valid inputs and derive the type from a runtime schema where that reduces duplication.

</details>

### 12. Why might an import compile but fail when a Node.js process starts?

<details>
<summary>Show answer</summary>

The compiler can resolve a path using options that the runtime does not implement. For example, paths can help TypeScript find source files without rewriting those specifiers in emitted JavaScript. Match module resolution to the actual host, use import paths the host understands, and test the emitted application rather than only running the type checker.

</details>

### 13. How would you migrate a JavaScript boundary without replacing uncertainty with type assertions?

<details>
<summary>Show answer</summary>

Start with one boundary that has a clear contract. Treat incoming values as unknown, validate what is needed and return a narrow typed result to consumers. Keep checks for missing or malformed data in tests. Then migrate callers incrementally so the strongest guarantees are concentrated where data enters the typed code.

</details>

## Official references

- [TypeScript: Everyday types](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html)
- [TypeScript: Narrowing](https://www.typescriptlang.org/docs/handbook/2/narrowing.html)
- [TypeScript: Generics](https://www.typescriptlang.org/docs/handbook/2/generics.html)
- [TypeScript: Type compatibility](https://www.typescriptlang.org/docs/handbook/type-compatibility.html)
- [TypeScript: exactOptionalPropertyTypes](https://www.typescriptlang.org/tsconfig/exactOptionalPropertyTypes.html)
- [TypeScript: noUncheckedIndexedAccess](https://www.typescriptlang.org/tsconfig/noUncheckedIndexedAccess.html)
- [TypeScript: Choosing compiler options](https://www.typescriptlang.org/docs/handbook/modules/guides/choosing-compiler-options.html)

Source: [Mentaro](https://www.mentaro.app/interview-questions/typescript). Original questions and answers: [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
