# Expressions

_RBE chapter root maps here. Every nested leaf below has the full 5-part teaching block._

### 9.1 Expressions

**What it is**

Rust programs are mostly statements and expressions. Variable bindings are statements. Blocks `{ ... }` are expressions: the last line without a semicolon is the block’s value. If you add a semicolon on the last line, the block returns the unit value `()` instead. This means `if`, `match`, and blocks can all produce values you assign with `let`.

**JS mental model**

Almost everything is an expression that returns a value — including `if` and blocks. JS statements vs expressions are blurrier here on purpose.

**Rust example**

```rust
fn main() {
    let x = 5u32;

    let y = {
        let x_squared = x * x;
        let x_cubed = x_squared * x;

        // This expression will be assigned to `y`
        x_cubed + x_squared + x
    };

    let z = {
        // The semicolon suppresses this expression and `()` is assigned to `z`
        2 * x;
    };

    println!("x is {:?}", x);
    println!("y is {:?}", y);
    println!("z is {:?}", z);
}
```

```rust
fn main() {
    // variable binding
    let x = 5;

    // expression;
    x;
    x + 1;
    15;
}
```

**JS equivalent**

```javascript
// JS: blocks with values need tricks
const y = (() => { const x = 5; return x * x; })();
```

**Watch out**

- A block’s value is its last expression **without** a semicolon.
- Semicolon turns an expression into a statement returning `()`.

_RBE source: `expression.md`_

---
