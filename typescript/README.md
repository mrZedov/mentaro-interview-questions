# TypeScript interview questions

[Explore this topic on Mentaro](https://www.mentaro.app/interview-questions/typescript) · [Start interactive practice on Mentaro](https://www.mentaro.app/login?mode=signup&redirect=/onboarding)

## Foundations

### 1. When should a function accept unknown rather than any?

<details>
<summary>Show answer</summary>

Use unknown when callers can supply arbitrary input but the implementation must check it before using it. For example, an event decoder must verify that the input is a non-null object and validate the relevant fields. Any permits unchecked property access and can propagate an unchecked type to callers.

**Explanation:** An external value and a validated application value are different contracts. Start with unknown at the boundary, check the property you need, and only then return the narrower type. A type assertion would silence the compiler but would not reject a malformed payload.

**Example:**

```typescript
function readId(input: unknown): string {
  if (typeof input !== "object" || input === null ||
      !("id" in input) || typeof input.id !== "string") {
    throw new TypeError("Expected a string id");
  }
  return input.id;
}
```

</details>

### 2. What does it mean for TypeScript to use structural typing?

<details>
<summary>Show answer</summary>

Compatibility generally depends on available members and their types, not on whether two declarations share a name. An object with an ID and a timestamp can be passed to a function that only requires the ID. A fresh object literal can still trigger excess-property checks, and private or protected class members impose additional constraints.

**Explanation:** A variable with extra properties is normally assignable to a smaller compatible object type; a fresh literal in that position can receive an excess-property diagnostic. This is a check for likely mistakes, not a claim that the object is sealed.

</details>

### 3. When can you use a type alias where an interface is not enough?

<details>
<summary>Show answer</summary>

An alias can name a union such as Loading | Ready | Failed, a primitive, or a mapped type. Interfaces describe object shapes and can be reopened through declaration merging. For a plain object contract either can work; choose based on whether you want merging or need to name a type that is not an object shape.

**Explanation:** Use an alias for an application-owned set of states that exhaustive code must handle. Use an interface when you intend a public object contract to support declaration merging; do not pick one solely because the syntax is shorter.

</details>

### 4. With strictNullChecks enabled, what would you return from a lookup that might miss?

<details>
<summary>Show answer</summary>

Include the possibility of absence in the return type, such as User | undefined. The caller must handle the missing case before dereferencing the result. A non-null assertion removes the type error, not the possibility of a miss, so reserve it for a separately established invariant.

**Explanation:** Put the absence in the return type at the lookup boundary, then make the caller choose between an explicit fallback, a not-found response or a required-record error. Using a non-null assertion there moves the failure away from the code that knows why the record is required.

</details>

### 5. Why can an object property become string even if you initialized it with one string literal?

<details>
<summary>Show answer</summary>

A mutable property may later be assigned a different string, so inference can widen its type even when the object variable is const. A literal annotation or an as const assertion can keep the literal type. As const also marks literal properties readonly in the type system; it does not freeze the object at runtime.

**Explanation:** Const protects the variable binding, not the fields of the bound object. A literal field is helpful for a discriminated union; use a targeted annotation if other fields should remain mutable.

</details>

## Applied

### 6. How do you make adding a new result variant cause a compile error in old handlers?

<details>
<summary>Show answer</summary>

Give each variant a shared discriminant with a different literal value. Switch on that discriminant and require the remaining value to be never in the fallback. If a new variant is not handled, it is still a possible value in the fallback and will not be assignable to never.

**Explanation:** The check works because control-flow analysis removes each handled variant. After the final case, the remaining type should be never. Returning a generic fallback without that check would let the compiler accept a newly added variant silently.

**Example:**

```typescript
type Result = { kind: "ok"; value: string } | { kind: "missing" };

function describe(result: Result): string {
  switch (result.kind) {
    case "ok": return result.value;
    case "missing": return "Not found";
    default: {
      const unhandled: never = result;
      throw new Error(String(unhandled));
    }
  }
}
```

</details>

### 7. Why use a generic rather than a union for a function that picks one property from an object?

<details>
<summary>Show answer</summary>

A generic can express the relationship between the key and the return type. With T for the object and K constrained to keyof T, the return type T[K] refers to exactly that key. Returning a union of all property types would make the caller narrow a result whose concrete type was already known at the call site.

**Explanation:** A union return loses information the caller already supplied. A constrained type parameter lets the compiler connect a particular property name with the corresponding value type.

**Example:**

```typescript
function pick<T, K extends keyof T>(item: T, key: K): T[K] {
  return item[key];
}
const fileLength: number = pick({ length: 4, tag: "file" }, "length");
```

</details>

### 8. When would you use satisfies instead of explicitly annotating a configuration object?

<details>
<summary>Show answer</summary>

Use satisfies to check that an expression conforms to a contract without replacing its result type with the contract. For example, check a handler map against a required set of events while retaining the inferred types of its specific entries. It is a compile-time check, not a validator for configuration loaded from a file.

**Explanation:** Satisfies is useful for validating a finite set of keys without giving every property the broad type of the target. It can influence contextual typing; the important guarantee is that it does not simply replace the expression’s resulting type with the constraint.

</details>

### 9. When is an optional property different from a required property whose value can be undefined?

<details>
<summary>Show answer</summary>

A required property must be present even if its type allows undefined. An optional property can be absent, which matters to Object.keys, object spread and the in operator. With exactOptionalPropertyTypes, setting an optional property to undefined explicitly is rejected unless undefined is part of its declared value type.

**Explanation:** A patch API can use omission to leave a property unchanged and null to clear it. JSON drops undefined-valued object properties, so a required property with an undefined value cannot represent that distinction reliably over JSON.

</details>

### 10. How can TypeScript prevent mixing order IDs and customer IDs when both are strings?

<details>
<summary>Show answer</summary>

Use distinct branded string types and construct them at a trusted validation boundary. The brand prevents accidental interchange at compile time; it does not validate a string received over the network.

**Explanation:** Keep branded IDs behind constructors or decoders. A validated order ID should be granted the order brand at the point where the system knows its domain; asserting the brand at each call site defeats the guard against accidental interchange.

</details>

## Advanced scenarios

### 11. A response is assigned to User after JSON parsing. What evidence do you need before using it?

<details>
<summary>Show answer</summary>

Check the HTTP result and treat the decoded value as untrusted. Validate the fields and nested shapes that the application relies on before returning a User. An annotation or type assertion does not inspect JSON; an invalid payload otherwise reaches consumers as though it had been verified.

**Explanation:** Check nested fields your business logic actually reads, including nullability and arrays. A syntactically valid JSON document can still be an array or a scalar. Avoid returning a typed value until the decoder has accepted it.

</details>

### 12. Can a validator declared as input is Order still cause a runtime failure?

<details>
<summary>Show answer</summary>

Yes. TypeScript does not prove that the declared predicate covers every required field. A check for a single ID could promise too much about nested items or status. Test invalid as well as valid inputs and derive the type from a runtime schema where that reduces duplication.

**Explanation:** A user-defined predicate is a promise to the type checker. If it only tests that a property exists, callers may still assume the property has the right type and invoke methods on it. Negative fixtures are particularly important for these predicates.

</details>

### 13. Why might an import compile but fail when a Node.js process starts?

<details>
<summary>Show answer</summary>

The compiler can resolve a path using options that the runtime does not implement. For example, paths can help TypeScript find source files without rewriting those specifiers in emitted JavaScript. Match module resolution to the actual host, use import paths the host understands, and test the emitted application rather than only running the type checker.

**Explanation:** Compare what the compiler resolved with the actual import in emitted JavaScript. A build tool may rewrite aliases or resolve extensions that Node does not; validate entry points in the same module mode and package layout used for deployment.

</details>

### 14. How would you migrate a JavaScript boundary without replacing uncertainty with type assertions?

<details>
<summary>Show answer</summary>

Start with one boundary that has a clear contract. Treat incoming values as unknown, validate what is needed and return a narrow typed result to consumers. Keep checks for missing or malformed data in tests. Then migrate callers incrementally so the strongest guarantees are concentrated where data enters the typed code.

**Explanation:** Choose a boundary that turns an untyped value into a small internal shape. Test malformed and missing input there, then remove downstream assertions that have become redundant. Avoid declaring the entire upstream object valid just because you need one property.

</details>

### 15. Why might a callback that accepts a narrower input be unsafe to register?

<details>
<summary>Show answer</summary>

The caller may pass any value from the wider contract. Under strictFunctionTypes, ordinary function parameters are checked contravariantly; method signatures have a bivariant exception. Test the public type contract rather than relying on convenient inference.

**Explanation:** A handler for only dogs cannot stand in for a handler required to accept every animal, because a cat is a legal input to the caller. If a library needs the broader contract, narrow within the handler or make the registration generic in the event type.

</details>

## Official references

- [TypeScript: Everyday types](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html)
- [TypeScript: Narrowing](https://www.typescriptlang.org/docs/handbook/2/narrowing.html)
- [TypeScript: Generics](https://www.typescriptlang.org/docs/handbook/2/generics.html)
- [TypeScript: Type compatibility](https://www.typescriptlang.org/docs/handbook/type-compatibility.html)
- [TypeScript: exactOptionalPropertyTypes](https://www.typescriptlang.org/tsconfig/exactOptionalPropertyTypes.html)
- [TypeScript: noUncheckedIndexedAccess](https://www.typescriptlang.org/tsconfig/noUncheckedIndexedAccess.html)
- [TypeScript: Choosing compiler options](https://www.typescriptlang.org/docs/handbook/modules/guides/choosing-compiler-options.html)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
