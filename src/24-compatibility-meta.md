# Compatibility & meta

_RBE chapter root maps here. Every nested leaf below has the full 5-part teaching block._

### 24.1 Compatibility

**What it is**

The Rust language is evolving rapidly, and because of this certain compatibility
issues can arise, despite efforts to ensure forwards-compatibility wherever
possible.

**JS mental model**

Compatibility tools help when keywords change across editions.

**Rust example**

```rust
fn main() {
    let r#type = 1;
    println!("{}", r#type);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Editions are opt-in per crate and interoperable.

_RBE source: `compatibility.md`_

---

### 24.1.1 Raw identifiers

**What it is**

Raw identifiers are written r#keyword. They let you name something with a word that is normally reserved (for example match, async, crate). This helps compatibility when older APIs collide with newer keywords.

**JS mental model**

r#match lets you use a keyword as an identifier — escape hatch when a keyword is reserved.

**Rust example**

```rust
extern crate foo;

fn main() {
    foo::r#try();
}
```

```rust
extern crate foo;

fn main() {
    foo::try();
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Useful when binding to external APIs that used a now-keyword name.
- Rare in new code.

_RBE source: `compatibility/raw_identifiers.md`_

---

### 24.1.2 Meta

**What it is**

Some topics aren't exactly relevant to how your program runs but provide you
tooling or infrastructure support which just makes things better for
everyone. These topics include:
- [Documentation][doc]: Generate library documentation for users via the included
  `rustdoc`. - [Playground][playground]: Integrate the Rust Playground in your documentation. [doc]: meta/doc.md
[playground]: meta/playground.md.

**JS mental model**

Meta topics: documenting crates and using the Playground.

**Rust example**

```rust
//! Crate-level docs use //!
//! Run: cargo doc --open

fn main() {}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Good docs are part of the Rust culture.

_RBE source: `meta.md`_

---

### 24.1.2.1 Documentation

**What it is**

Rust documentation comments use /// (or //! for modules). cargo doc generates an HTML site for your crate and dependencies. Markdown is supported. This is the normal way to publish API docs.

**JS mental model**

/// doc comments + cargo doc — like JSDoc + a standard site generator built-in.

**Rust example**

```rust
#[doc(inline)]
pub use bar::Bar;

/// bar docs
pub mod bar {
    /// the docs for Bar
    pub struct Bar;
}
```

```rust
// Example from the futures-rs library
#[doc(hidden)]
pub use self::async_await::*;
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Document public items.
- Examples in docs are tested.
- cargo doc --open.

_RBE source: `meta/doc.md`_

---

### 24.1.2.2 Playground

**What it is**

The Rust Playground (play.rust-lang.org) runs Rust in the browser/sandbox. You can try examples, share links, and switch toolchains. Handy when you do not have rustc locally or want to show a tiny demo.

**JS mental model**

The Rust Playground is an online compiler — like a REPL/sandbox for sharing snippets without installing.

**Rust example**

```rust
fn main() {
    println!("Hello World!");
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Great for minimal reproductions.
- Has stable/beta/nightly channels.

_RBE source: `meta/playground.md`_

---
