# Variable bindings, mutability, scope, shadowing

_RBE chapter root maps here. Every nested leaf below has the full 5-part teaching block._

### 6.1 Variable Bindings

**What it is**

Rust provides type safety via static typing. Variable bindings can be type
annotated when declared. However, in most cases, the compiler will be able
to infer the type of the variable from the context, heavily reducing the
annotation burden. Values (like literals) can be bound to variables, using the `let` binding.

**JS mental model**

`let` exists in both languages, but Rust bindings are **immutable by default**.

**Rust example**

```rust
fn main() {
    let an_integer = 1u32;
    let a_boolean = true;
    let unit = ();

    // copy `an_integer` into `copied_integer`
    let copied_integer = an_integer;

    println!("An integer: {}", copied_integer);
    println!("A boolean: {}", a_boolean);
    println!("Meet the unit value: {:?}", unit);

    // The compiler warns about unused variable bindings; these warnings can
    // be silenced by prefixing the variable name with an underscore
    let _unused_variable = 3u32;

    let noisy_unused_variable = 2u32;
    // FIXME ^ Prefix with an underscore to suppress the warning
    // Please note that warnings may not be shown in a browser
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Immutability is default — add `mut` only when needed.
- Unused bindings warn (`_` prefix silences).

_RBE source: `variable_bindings.md`_

---

### 6.1.1 Mutability

**What it is**

Variable bindings are immutable by default, but this can be overridden using
the `mut` modifier. The compiler will throw a detailed diagnostic about mutability errors.

**JS mental model**

`let mut x` ≈ `let x` you plan to reassign. Without `mut`, reassignment is a compile error.

**Rust example**

```rust
fn main() {
    let _immutable_binding = 1;
    let mut mutable_binding = 1;

    println!("Before mutation: {}", mutable_binding);

    // Ok
    mutable_binding += 1;

    println!("After mutation: {}", mutable_binding);

    // Error! Cannot assign a new value to an immutable variable
    _immutable_binding += 1;
}
```

**JS equivalent**

```javascript
let x = 1; // reassignment allowed
const y = 1; // reassignment forbidden
```

**Watch out**

- `mut` is about the binding, not deep freeze of all interior data (see interior mutability later).

_RBE source: `variable_bindings/mut.md`_

---

### 6.1.2 Scope and Shadowing

**What it is**

Variable bindings have a scope, and are constrained to live in a *block*. A
block is a collection of statements enclosed by braces `{}`. Also, [variable shadowing][variable-shadow] is allowed. [variable-shadow]: https://en.wikipedia.org/wiki/Variable_shadowing.

**JS mental model**

Block scopes `{ }` like JS, plus **shadowing**: `let x = …; let x = …;` is allowed and common.

**Rust example**

```rust
fn main() {
    // This binding lives in the main function
    let long_lived_binding = 1;

    // This is a block, and has a smaller scope than the main function
    {
        // This binding only exists in this block
        let short_lived_binding = 2;

        println!("inner short: {}", short_lived_binding);
    }
    // End of the block

    // Error! `short_lived_binding` doesn't exist in this scope
    println!("outer short: {}", short_lived_binding);
    // FIXME ^ Comment out this line

    println!("outer long: {}", long_lived_binding);
}
```

```rust
fn main() {
    let shadowed_binding = 1;

    {
        println!("before being shadowed: {}", shadowed_binding);

        // This binding *shadows* the outer one
        let shadowed_binding = "abc";

        println!("shadowed in inner block: {}", shadowed_binding);
    }
    println!("outside inner block: {}", shadowed_binding);

    // This binding *shadows* the previous binding
    let shadowed_binding = 2;
    println!("shadowed in outer block: {}", shadowed_binding);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Shadowing ≠ mutation. New binding can even change type.
- Inner scope ends → inner bindings drop.

_RBE source: `variable_bindings/scope.md`_

---

### 6.1.3 Declare first

**What it is**

It is possible to declare variable bindings first and initialize them later, but all variable bindings must be initialized before they are used: the compiler forbids use of uninitialized variable bindings, as it would lead to undefined behavior. It is not common to declare a variable binding and initialize it later in the function. It is more difficult for a reader to find the initialization when initialization is separated from declaration. It is common to declare and initialize a variable binding near where the variable will be used.

**JS mental model**

You can `let x;` and assign later once — unusual vs JS `let x;`.

**Rust example**

```rust
fn main() {
    // Declare a variable binding
    let a_binding;

    {
        let x = 2;

        // Initialize the binding
        a_binding = x * x;
    }

    println!("a binding: {}", a_binding);

    let another_binding;

    // Error! Use of uninitialized binding
    println!("another binding: {}", another_binding);
    // FIXME ^ Comment out this line

    another_binding = 1;

    println!("another binding: {}", another_binding);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Must assign before read.
- Still must be definitely assigned on all paths.

_RBE source: `variable_bindings/declare.md`_

---

### 6.1.4 Freezing

**What it is**

When data is bound by the same name immutably, it also *freezes*. *Frozen* data can't be
modified until the immutable binding goes out of scope:.

**JS mental model**

While a value is immutably borrowed, you cannot mutate it through the owner — temporary “freeze.”

**Rust example**

```rust
fn main() {
    let mut _mutable_integer = 7i32;

    {
        // Shadowing by immutable `_mutable_integer`
        let _mutable_integer = _mutable_integer;

        // Error! `_mutable_integer` is frozen in this scope
        _mutable_integer = 50;
        // FIXME ^ Comment out this line

        // `_mutable_integer` goes out of scope
    }

    // Ok! `_mutable_integer` is not frozen in this scope
    _mutable_integer = 3;
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- This is the borrow checker protecting you from JS-style aliasing bugs.

_RBE source: `variable_bindings/freeze.md`_

---
