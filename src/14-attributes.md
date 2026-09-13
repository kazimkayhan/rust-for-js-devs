# Attributes

_RBE chapter root maps here. Every nested leaf below has the full 5-part teaching block._

### 14.1 Attributes

**What it is**

An attribute is metadata applied to some module, crate or item. This metadata
can be used to/for:
<!-- TODO: Link these to their respective examples -->
* [conditional compilation of code][cfg]
* [set crate name, version and type (binary or library)][crate]
* disable [lints][lint] (warnings)
* enable compiler features (macros, glob imports, etc.)
* link to a foreign library
* mark functions as unit tests
* mark functions that will be part of a benchmark
* [attribute like macros][macros]
Attributes look like `#[outer_attribute]` or `#![inner_attribute]`,
with the difference between them being where they apply. * `#[outer_attribute]` applies to the [item][item] immediately
  following it. Some examples of items are: a function, a module
  declaration, a constant, a structure, an enum.

**JS mental model**

Attributes `#[…]` annotate items — like decorators / annotations / pragmas.

**Rust example**

```rust
#![allow(unused_variables)]

  fn main() {
      let x = 3; // This would normally warn about an unused variable.
  }
```

```rust
#[attribute(value, value2)]


#[attribute(value, value2, value3,
            value4, value5)]
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- `#[attr]` applies to the next item; `#![attr]` applies to the current crate/module.

_RBE source: `attribute.md`_

---

### 14.1.1 dead_code

**What it is**

The compiler warns about unused functions/items (`dead_code`). During learning you may allow it with `#[allow(dead_code)]` on an item, or `#![allow(dead_code)]` for the whole file/crate. In real projects, prefer fixing the warning.

**JS mental model**

`#[allow(dead_code)]` silences unused warnings — like eslint-disable for unused vars.

**Rust example**

```rust
fn used_function() {}

// `#[allow(dead_code)]` is an attribute that disables the `dead_code` lint
#[allow(dead_code)]
fn unused_function() {}

fn noisy_unused_function() {}
// FIXME ^ Add an attribute to suppress the warning

fn main() {
    used_function();
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Prefer using/exporting the item or prefix `_` when appropriate.
- Dead code warnings help catch mistakes.

_RBE source: `attribute/unused.md`_

---

### 14.1.2 Crates

**What it is**

Crate attributes use the inner form `#![…]` at the top of `main.rs`/`lib.rs`. Historically they set crate name/type; today Cargo.toml usually owns that, but lint settings and feature-related attrs still appear as crate attributes.

**JS mental model**

Crate-level attrs set crate name/type etc. Like package-level config living in source.

**Rust example**

```rust
// This crate is a library
#![crate_type = "lib"]
// The library is named "rary"
#![crate_name = "rary"]

pub fn public_function() {
    println!("called rary's `public_function()`");
}

fn private_function() {
    println!("called rary's `private_function()`");
}

pub fn indirect_access() {
    print!("called rary's `indirect_access()`, that\n> ");

    private_function();
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Most crate settings now live in `Cargo.toml`; some attrs remain useful (`#![allow…]`).

_RBE source: `attribute/crate.md`_

---

### 14.1.3 cfg

**What it is**

`cfg` means configuration. `#[cfg(condition)]` keeps an item only when the condition is true at compile time. False cfg items are erased — they do not exist in that build.

**JS mental model**

`#[cfg(…)]` conditionally compiles code — like `#ifdef` or bundler `process.env.NODE_ENV` branches removed at build time.

**Rust example**

```rust
// This function only gets compiled if the target OS is linux
#[cfg(target_os = "linux")]
fn are_you_on_linux() {
    println!("You are running linux!");
}

// And this function only gets compiled if the target OS is *not* linux
#[cfg(not(target_os = "linux"))]
fn are_you_on_linux() {
    println!("You are *not* running linux!");
}

fn main() {
    are_you_on_linux();

    println!("Are you sure?");
    if cfg!(target_os = "linux") {
        println!("Yes. It's definitely linux!");
    } else {
        println!("Yes. It's definitely *not* linux!");
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Test both sides of a cfg.
- Common cfgs: `target_os`, `debug_assertions`, `feature = "…"`.

_RBE source: `attribute/cfg.md`_

---

#### 14.1.3.1 Custom

**What it is**

You can invent custom cfg predicates (e.g. `some_condition`) and enable them with `rustc --cfg some_condition` or Cargo config. Custom cfg is powerful but Cargo **features** are the friendlier mechanism for optional functionality.

**JS mental model**

Custom cfg flags are like defining your own compile-time feature constants.

**Rust example**

```rust
#[cfg(some_condition)]
fn conditional_function() {
    println!("condition met!");
}

fn main() {
    conditional_function();
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Pass `--cfg my_flag` via RUSTFLAGS or `.cargo/config.toml`.
- For user options prefer Cargo features over ad-hoc cfg.

_RBE source: `attribute/cfg/custom.md`_

---
