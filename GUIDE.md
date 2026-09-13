# Rust from JavaScript: Zero to Hero

> A complete, super-simple tutorial for JavaScript (ES6+) developers learning Rust from scratch.
> Curriculum aligned with [Rust by Example](https://doc.rust-lang.org/rust-by-example/) (RBE).
> **Every nested leaf** in the RBE `SUMMARY.md` has its own subsection below (What / JS model / Rust / JS / Watch out).
> Edition **2021+** idioms. Prefer a long complete guide over a short skim.

---

## Table of contents

1. [Welcome & how to use this guide](#1-welcome--how-to-use-this-guide)
2. [Install Rust + first `cargo new`](#2-install-rust--first-cargo-new-map-npmnode)
3. [Hello World, comments & formatting](#3-hello-world-comments-formatting)
4. [Primitives](#4-primitives)
5. [Custom types: struct, enum, constants](#5-custom-types-struct-enum-constants)
6. [Variable bindings, mutability, scope, shadowing](#6-variable-bindings-mutability-scope-shadowing)
7. [Types, casting, inference, aliases](#7-types-casting-inference-aliases)
8. [Conversion](#8-conversion)
9. [Expressions](#9-expressions)
10. [Flow of Control](#10-flow-of-control)
11. [Functions, methods, closures, HOFs](#11-functions-methods-closures-hofs)
12. [Modules & visibility](#12-modules-visibility)
13. [Crates & Cargo](#13-crates-cargo)
14. [Attributes](#14-attributes)
15. [Generics](#15-generics)
16. [Scoping rules: ownership, borrowing, lifetimes](#16-scoping-rules-ownership-borrowing-lifetimes)
17. [Traits](#17-traits)
18. [Macros](#18-macros)
19. [Error handling](#19-error-handling)
20. [Std library types](#20-std-library-types)
21. [Std misc](#21-std-misc)
22. [Testing](#22-testing)
23. [Unsafe operations](#23-unsafe-operations)
24. [Compatibility & meta](#24-compatibility-meta)
25. [Zero-to-hero practice path](#25-zero-to-hero-practice-path--checklist)
26. [Quick JS↔Rust cheatsheet](#26-quick-jsrust-cheatsheet-appendix)

Coverage checklist: [`COVERAGE.md`](./COVERAGE.md) (every SUMMARY leaf → DONE).

---

## 1. Welcome & how to use this guide

### Plain English

Rust is a systems language that gives you **speed like C/C++** and **memory safety without a garbage collector**. JavaScript runs with a GC and is dynamically typed. Rust is **statically typed**, **compiled**, and enforces rules at compile time so many entire classes of bugs never ship.

This guide follows **every chapter and nested leaf** of **Rust by Example** (see `COVERAGE.md`). For each idea you get:

1. Plain English
2. A JS mental model / analogy
3. Rust code
4. JS equivalent (when useful)
5. Common pitfalls for JS developers

### JS mental model

| JavaScript | Rust |
|---|---|
| `node` / V8 runtime | Compiled binary (or `cargo run`) |
| `npm` / `yarn` / `pnpm` | `cargo` |
| `package.json` | `Cargo.toml` |
| `node_modules` | crates from crates.io (cached in `~/.cargo`) |
| Dynamic types | Static types checked at compile time |
| GC cleans memory | **Ownership** frees memory when values go out of scope |
| `null` / `undefined` | Prefer `Option<T>` — no null by default |
| `try/catch` + thrown Errors | `Result<T, E>` + `?` operator |
| Prototypes / classes | `struct` + `impl` + `trait` |
| `async/await` (Promises) | `async/await` (Futures) — similar surface, different runtime |

### How to study

1. Install Rust (section 2).
2. Create one practice crate: `cargo new rust_playground`.
3. Read a section, type the examples, **break them** on purpose, read the compiler errors (they are excellent).
4. Treat the **ownership** chapter as the boss fight — revisit it often.
5. Keep [https://doc.rust-lang.org/rust-by-example/](https://doc.rust-lang.org/rust-by-example/) open as the official twin.

### Official sources used

- [Rust by Example](https://doc.rust-lang.org/rust-by-example/)
- Full print page / local extract of RBE curriculum (`rbe-SUMMARY.md`, `rbe-print.html`)
- Modern Rust edition **2021** (default for new projects)

---

## 2. Install Rust + first `cargo new` (map npm/node)

### Plain English

You install a **toolchain** (`rustc` compiler + `cargo` package manager + `rustup` version manager). Then you create projects with Cargo the way you create Node projects with npm.

### JS mental model

| Task | JavaScript | Rust |
|---|---|---|
| Install runtime/toolchain | Install Node (nvm) | `rustup` installs rustc + cargo |
| New project | `npm init` / `npx create-…` | `cargo new my_app` |
| Add dependency | `npm install lodash` | `cargo add serde` (or edit `Cargo.toml`) |
| Run | `node index.js` / `npm start` | `cargo run` |
| Build | bundlers / nothing | `cargo build` / `cargo build --release` |
| Test | `npm test` | `cargo test` |
| Docs | JSDoc / TypeDoc | `cargo doc --open` |
| Format | Prettier | `cargo fmt` |
| Lint | ESLint | `cargo clippy` |

### Install

```bash
# Linux / macOS / WSL
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source "$HOME/.cargo/env"

rustc --version
cargo --version
```

On Windows, use the installer from [https://rustup.rs](https://rustup.rs).

Update later with:

```bash
rustup update
```

### First project

```bash
cargo new hello_rust
cd hello_rust
cargo run
```

What you get:

```text
hello_rust/
├── Cargo.toml          # like package.json
└── src/
    └── main.rs         # like index.js entry
```

`Cargo.toml` starter:

```toml
[package]
name = "hello_rust"
version = "0.1.0"
edition = "2021"

[dependencies]
```

### Library vs binary

```bash
cargo new my_lib --lib   # like publishing an npm package
cargo new my_bin         # default: binary with main()
```

### Pitfalls for JS devs

- There is **no REPL** as primary workflow (though `evcxr` exists). Prefer small programs + `cargo run`.
- `cargo run` compiles first — first run is slower; later runs use incremental compile.
- You do **not** commit a giant `node_modules` equivalent; `Cargo.lock` is for binaries/apps (commit it for apps).
- Edition (`2015` / `2018` / `2021` / `2024`) is **not** a breaking language version for your deps — it is per-crate syntax mode.

---

## 1b. RBE Introduction leaf (full teaching block)

### 1.0 RBE Introduction leaf

**What it is**

Rust is a systems language focused on safety, speed, and concurrency. This guide walks every Rust by Example topic in simple words for JS developers.

**JS mental model**

Think of Rust as “TypeScript’s strictness + C’s speed + no garbage collector.” You compile to a binary instead of running on V8.

**Rust example**

```rust
fn main() {
    println!("Hello, Rust from JavaScript land!");
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Rust has no `null` by default — use `Option`.
- Many bugs that are runtime in JS are compile errors in Rust.

_RBE source: `index.md`_

---

## 3. Hello World, comments & formatting

_RBE chapter root maps here. Every nested leaf below has the full 5-part teaching block._

### 3.1 Hello World

**What it is**

Hello World is the smallest complete Rust program. It prints text to the terminal using the `println!` macro.

**JS mental model**

`println!` is like `console.log`, but it is a **macro** (note the `!`) and formats at compile time.

**Rust example**

```rust
// This is a comment, and is ignored by the compiler.
// You can test this code by clicking the "Run" button over there ->
// or if you prefer to use your keyboard, you can use the "Ctrl + Enter"
// shortcut.

// This code is editable, feel free to hack it!
// You can always return to the original code by clicking the "Reset" button ->

// This is the main function.
fn main() {
    // Statements here are executed when the compiled binary is called.

    // Print text to the console.
    println!("Hello World!");
}
```

**JS equivalent**

```javascript
console.log("Hello World!");
```

**Watch out**

- Forgetting `!` on `println` will not compile.
- Rust programs need a `fn main()` entry point like a script’s top-level code.

_RBE source: `hello.md`_

---

### 3.1.1 Comments

**What it is**

Any program requires comments, and Rust supports
a few different varieties:
Regular Comments
These are ignored by the compiler:
* **Line comments**: Start with `//` and continue to the end of the line
* **Block comments**: Enclosed in `/* ... */` and can span multiple lines
Documentation Comments (Doc Comments) which are parsed into HTML library [documentation][docs]:
 - `///` - Generates docs for the item that follows it
- `//!` - Generates docs for the enclosing item (typically used at the top of a file or module)
See also:
[Library documentation][docs]
[docs]: ../meta/doc.md.

**JS mental model**

Same idea as JS `//` and `/* */`. Rust also has `///` and `//!` for docs that become HTML docs.

**Rust example**

```rust
fn main() {
    // Line comments start with two slashes.
    // Everything after the slashes is ignored by the compiler.

    // Example: This line won't execute
    // println!("Hello, world!");

    // Try removing the slashes above and running the code again.

    /*
      Block comments are useful for temporarily disabling code.
      They can also be nested: /* like this */ which makes it easy
      to comment out large sections quickly.
     */

    /*
     * Note: The asterisk column on the left is just for style - 
     * it's not required by the language.
     */

    // Block comments make it easy to toggle code on/off by adding
    // or removing just one slash:

    /* <- Add a '/' here to uncomment the entire block below

    println!("Now");
    println!("everything");
    println!("executes!");
    // Line comments inside remain unaffected

    // */

    // Block comments can also be used within expressions:
    let x = 5 + /* 90 + */ 5;
    println!("Is `x` 10 or 100? x = {}", x);
}
```

**JS equivalent**

```javascript
// line comment
/* block comment */
```

**Watch out**

- `///` documents the next item; `//!` documents the enclosing module/crate.
- Doc comments are markdown.

_RBE source: `hello/comment.md`_

---

### 3.1.2 Formatted print

**What it is**

Printing is handled by a series of [`macros`][macros] defined in
[`std::fmt`][fmt] some of which are:
* `format!`: write formatted text to [`String`][string]
* `print!`: same as `format!` but the text is printed to the console
  (io::stdout). * `println!`: same as `print!` but a newline is appended. * `eprint!`: same as `print!` but the text is printed to the standard error
  (io::stderr). * `eprintln!`: same as `eprint!` but a newline is appended.

**JS mental model**

`console.log(a, b)` vs Rust format strings: `println!("{} {}", a, b)`. Positional and named args exist too.

**Rust example**

```rust
fn main() {
    // In general, the `{}` will be automatically replaced with any
    // arguments. These will be stringified.
    println!("{} days", 31);

    // Positional arguments can be used. Specifying an integer inside `{}`
    // determines which additional argument will be replaced. Arguments start
    // at 0 immediately after the format string.
    println!("{0}, this is {1}. {1}, this is {0}", "Alice", "Bob");

    // As can named arguments.
    println!("{subject} {verb} {object}",
             object="the lazy dog",
             subject="the quick brown fox",
             verb="jumps over");

    // Different formatting can be invoked by specifying the format character
    // after a `:`.
    println!("Base 10:               {}",   69420); // 69420
    println!("Base 2 (binary):       {:b}", 69420); // 10000111100101100
    println!("Base 8 (octal):        {:o}", 69420); // 207454
    println!("Base 16 (hexadecimal): {:x}", 69420); // 10f2c

    // You can right-justify text with a specified width. This will
    // output "    1". (Four white spaces and a "1", for a total width of 5.)
    println!("{number:>5}", number=1);

    // You can pad numbers with extra zeroes,
    println!("{number:0>5}", number=1); // 00001
    // and left-adjust by flipping the sign. This will output "10000".
    println!("{number:0<5}", number=1); // 10000

    // You can use named arguments in the format specifier by appending a `$`.
    println!("{number:0>width$}", number=1, width=5);

    // Rust even checks to make sure the correct number of arguments are used.
    println!("My name is {0}, {1} {0}", "Bond");
    // FIXME ^ Add the missing argument: "James"

    // Only types that implement fmt::Display can be formatted with `{}`. User-
    // defined types do not implement fmt::Display by default.

    #[allow(dead_code)] // disable `dead_code` which warn against unused module
    struct Structure(i32);

    // This will not compile because `Structure` does not implement
    // fmt::Display.
    // println!("This struct `{}` won't print...", Structure(3));
    // TODO ^ Try uncommenting this line

    // For Rust 1.58 and above, you can directly capture the argument from a
    // surrounding variable. Just like the above, this will output
    // "    1", 4 white spaces and a "1".
    let number: f64 = 1.0;
    let width: usize = 5;
    println!("{number:>width$}");
}
```

**JS equivalent**

```javascript
console.log(`hello ${name}`);
```

**Watch out**

- Format arity is checked at compile time — wrong number of args = error.
- `print!` has no newline; `println!` does.

_RBE source: `hello/print.md`_

---

#### 3.1.2.1 Debug

**What it is**

All types which want to use `std::fmt` formatting `traits` require an
implementation to be printable. Automatic implementations are only provided
for types such as in the `std` library. All others *must* be manually
implemented somehow. The `fmt::Debug` `trait` makes this very straightforward.

**JS mental model**

`{:?}` is like a built-in rough `JSON.stringify` for debugging. Derive `Debug` to opt in.

**Rust example**

```rust
// Derive the `fmt::Debug` implementation for `Structure`. `Structure`
// is a structure which contains a single `i32`.
#[derive(Debug)]
struct Structure(i32);

// Put a `Structure` inside of the structure `Deep`. Make it printable
// also.
#[derive(Debug)]
struct Deep(Structure);

fn main() {
    // Printing with `{:?}` is similar to with `{}`.
    println!("{:?} months in a year.", 12);
    println!("{1:?} {0:?} is the {actor:?} name.",
             "Slater",
             "Christian",
             actor="actor's");

    // `Structure` is printable!
    println!("Now {:?} will print!", Structure(3));

    // The problem with `derive` is there is no control over how
    // the results look. What if I want this to just show a `7`?
    println!("Now {:?} will print!", Deep(Structure(7)));
}
```

```rust
// This structure cannot be printed either with `fmt::Display` or
// with `fmt::Debug`.
struct UnPrintable(i32);

// The `derive` attribute automatically creates the implementation
// required to make this `struct` printable with `fmt::Debug`.
#[derive(Debug)]
struct DebugPrintable(i32);
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- `Debug` is for programmers; `Display` is for end users.
- Many std types already implement `Debug`.

_RBE source: `hello/print/print_debug.md`_

---

#### 3.1.2.2 Display

**What it is**

`fmt::Debug` hardly looks compact and clean, so it is often advantageous to
customize the output appearance. This is done by manually implementing
[`fmt::Display`][fmt], which uses the `{}` print marker. Implementing it
looks like this:
`fmt::Display` may be cleaner than `fmt::Debug` but this presents
a problem for the `std` library. How should ambiguous types be displayed?

**JS mental model**

Implementing `Display` is like writing a custom `toString()` / `util.inspect` for users.

**Rust example**

```rust
use std::fmt; // Import `fmt`

// A structure holding two numbers. `Debug` will be derived so the results can
// be contrasted with `Display`.
#[derive(Debug)]
struct MinMax(i64, i64);

// Implement `Display` for `MinMax`.
impl fmt::Display for MinMax {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        // Use `self.number` to refer to each positional data point.
        write!(f, "({}, {})", self.0, self.1)
    }
}

// Define a structure where the fields are nameable for comparison.
#[derive(Debug)]
struct Point2D {
    x: f64,
    y: f64,
}

// Similarly, implement `Display` for `Point2D`.
impl fmt::Display for Point2D {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        // Customize so only `x` and `y` are denoted.
        write!(f, "x: {}, y: {}", self.x, self.y)
    }
}

fn main() {
    let minmax = MinMax(0, 14);

    println!("Compare structures:");
    println!("Display: {}", minmax);
    println!("Debug: {:?}", minmax);

    let big_range =   MinMax(-300, 300);
    let small_range = MinMax(-3, 3);

    println!("The big range is {big} and the small is {small}",
             small = small_range,
             big = big_range);

    let point = Point2D { x: 3.3, y: 7.2 };

    println!("Compare points:");
    println!("Display: {}", point);
    println!("Debug: {:?}", point);

    // The following line would not compile: both `Debug` and `Display`
    // were implemented, but `{:b}` requires `fmt::Binary` to be
    // implemented, which it hasn't been for `Point2D`.
    // println!("What does Point2D look like in binary: {:b}?", point);
}
```

```rust
// Import (via `use`) the `fmt` module to make it available.
use std::fmt;

// Define a structure for which `fmt::Display` will be implemented. This is
// a tuple struct named `Structure` that contains an `i32`.
struct Structure(i32);

// To use the `{}` marker, the trait `fmt::Display` must be implemented
// manually for the type.
impl fmt::Display for Structure {
    // This trait requires `fmt` with this exact signature.
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        // Write strictly the first element into the supplied output
        // stream: `f`. Returns `fmt::Result` which indicates whether the
        // operation succeeded or failed. Note that `write!` uses syntax which
        // is very similar to `println!`.
        write!(f, "{}", self.0)
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- `Display` uses `{}`; `Debug` uses `{:?}`.
- You cannot `derive(Display)` — implement it yourself.

_RBE source: `hello/print/print_display.md`_

---

##### 3.1.2.2.1 Testcase: List

**What it is**

Implementing `fmt::Display` for a structure where the elements must each be
handled sequentially is tricky. The problem is that each `write!` generates a
`fmt::Result`. Proper handling of this requires dealing with *all* the
results. Rust provides the `?` operator for exactly this purpose.

**JS mental model**

Like writing a custom stringifier for an array of values.

**Rust example**

```rust
use std::fmt; // Import the `fmt` module.

// Define a structure named `List` containing a `Vec`.
struct List(Vec<i32>);

impl fmt::Display for List {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        // Create a reference to the Vec<i32> stored in the List struct.
        let vec = &self.0;

        write!(f, "[")?;

        // Iterate over `v` in `vec` while enumerating the iteration
        // index in `index`.
        for (index, v) in vec.iter().enumerate() {
            // For every element except the first, add a comma.
            // Use the ? operator to return on errors.
            if index != 0 { write!(f, ", ")?; }
            write!(f, "{}", v)?;
        }

        // Close the opened bracket and return a fmt::Result value.
        write!(f, "]")
    }
}

fn main() {
    let v = List(vec![1, 2, 3]);
    println!("{}", v);
}
```

```rust
// Try `write!` to see if it errors. If it errors, return
// the error. Otherwise continue.
write!(f, "{}", value)?;
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Inside `fmt`, use `write!` to compose output.
- Return `fmt::Result`.

_RBE source: `hello/print/print_display/testcase_list.md`_

---

#### 3.1.2.3 Formatting

**What it is**

We've seen that formatting is specified via a *format string*:
* `format!("{}", foo)` -> `"3735928559"`
* `format!("0x{:X}", foo)` -> [`"0xDEADBEEF"`][deadbeef]
* `format!("0o{:o}", foo)` -> `"0o33653337357"`
The same variable (`foo`) can be formatted differently depending on which
*argument type* is used: `X` vs `o` vs *unspecified*. This formatting functionality is implemented via traits, and there is one trait
for each argument type. The most common formatting trait is `Display`, which
handles cases where the argument type is left unspecified: `{}` for instance. You can view a [full list of formatting traits][fmt_traits] and their argument
types in the [`std::fmt`][fmt] documentation.

**JS mental model**

Similar to `printf` / template literals with padding: `{:>5}`, `{:.2}`, hex, binary, etc.

**Rust example**

```rust
use std::fmt::{self, Formatter, Display};

struct City {
    name: &'static str,
    // Latitude
    lat: f32,
    // Longitude
    lon: f32,
}

impl Display for City {
    // `f` is a buffer, and this method must write the formatted string into it.
    fn fmt(&self, f: &mut Formatter) -> fmt::Result {
        let lat_c = if self.lat >= 0.0 { 'N' } else { 'S' };
        let lon_c = if self.lon >= 0.0 { 'E' } else { 'W' };

        // `write!` is like `format!`, but it will write the formatted string
        // into a buffer (the first argument).
        write!(f, "{}: {:.3}°{} {:.3}°{}",
               self.name, self.lat.abs(), lat_c, self.lon.abs(), lon_c)
    }
}

#[derive(Debug)]
struct Color {
    red: u8,
    green: u8,
    blue: u8,
}

fn main() {
    for city in [
        City { name: "Dublin", lat: 53.347778, lon: -6.259722 },
        City { name: "Oslo", lat: 59.95, lon: 10.75 },
        City { name: "Vancouver", lat: 49.25, lon: -123.1 },
    ] {
        println!("{}", city);
    }
    for color in [
        Color { red: 128, green: 255, blue: 90 },
        Color { red: 0, green: 3, blue: 254 },
        Color { red: 0, green: 0, blue: 0 },
    ] {
        // Switch this to use {} once you've added an implementation
        // for fmt::Display.
        println!("{:?}", color);
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Compile-time checked format strings catch typos early.
- Named parameters: `println!("{name}", name=value)`.

_RBE source: `hello/print/fmt.md`_

---

## 4. Primitives

_RBE chapter root maps here. Every nested leaf below has the full 5-part teaching block._

### 4.1 Primitives

**What it is**

Rust provides access to a wide variety of `primitives`. A sample includes:
Scalar Types
* Signed integers: `i8`, `i16`, `i32`, `i64`, `i128` and `isize` (pointer size)
* Unsigned integers: `u8`, `u16`, `u32`, `u64`, `u128` and `usize` (pointer
  size)
* Floating point: `f32`, `f64`
* `char` Unicode scalar values like `'a'`, `'α'` and `'∞'` (4 bytes each)
* `bool` either `true` or `false`
* The unit type `()`, whose only possible value is an empty tuple: `()`
Despite the value of a unit type being a tuple, it is not considered a compound
type because it does not contain multiple values. Compound Types
* Arrays like `[1, 2, 3]`
* Tuples like `(1, true)`
Variables can always be *type annotated*. Numbers may additionally be annotated
via a *suffix* or *by default*.

**JS mental model**

JS has loose number/string/boolean. Rust has many precise numeric types (`i32`, `u64`, `f64`, …) and distinguishes owned strings from string slices.

**Rust example**

```rust
fn main() {
    // Variables can be type annotated.
    let logical: bool = true;

    let a_float: f64 = 1.0;  // Regular annotation
    let an_integer   = 5i32; // Suffix annotation

    // Or a default will be used.
    let default_float   = 3.0; // `f64`
    let default_integer = 7;   // `i32`

    // A type can also be inferred from context.
    let mut inferred_type = 12; // Type i64 is inferred from another line.
    inferred_type = 4294967296i64;

    // A mutable variable's value can be changed.
    let mut mutable = 12; // Mutable `i32`
    mutable = 21;

    // Error! The type of a variable can't be changed.
    mutable = true;

    // Variables can be overwritten with shadowing.
    let mutable = true;

    /* Compound types - Array and Tuple */

    // Array signature consists of Type T and length as [T; length].
    let my_array: [i32; 5] = [1, 2, 3, 4, 5];

    // Tuple is a collection of values of different types
    // and is constructed using parentheses ().
    let my_tuple = (5u32, 1u8, true, -5.04f32);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Pick integer sizes on purpose (`i32` is a common default).
- Overflow debug-panics; release wraps (or use checked_*).

_RBE source: `primitives.md`_

---

### 4.1.1 Literals and operators

**What it is**

Integers `1`, floats `1.2`, characters `'a'`, strings `"abc"`, booleans `true`
and the unit type `()` can be expressed using literals. Integers can, alternatively, be expressed using hexadecimal, octal or binary
notation using these prefixes respectively: `0x`, `0o` or `0b`. Underscores can be inserted in numeric literals to improve readability, e.g. `1_000` is the same as `1000`, and `0.000_001` is the same as `0.000001`.

**JS mental model**

`0x`, `0o`, `0b` like JS; type suffixes (`42u32`) have no JS equivalent — JS numbers are mostly IEEE floats.

**Rust example**

```rust
fn main() {
    // Integer addition
    println!("1 + 2 = {}", 1u32 + 2);

    // Integer subtraction
    println!("1 - 2 = {}", 1i32 - 2);
    // TODO ^ Try changing `1i32` to `1u32` to see why the type is important

    // Scientific notation
    println!("1e4 is {}, -2.5e-3 is {}", 1e4, -2.5e-3);

    // Short-circuiting boolean logic
    println!("true AND false is {}", true && false);
    println!("true OR false is {}", true || false);
    println!("NOT true is {}", !true);

    // Bitwise operations
    println!("0011 AND 0101 is {:04b}", 0b0011u32 & 0b0101);
    println!("0011 OR 0101 is {:04b}", 0b0011u32 | 0b0101);
    println!("0011 XOR 0101 is {:04b}", 0b0011u32 ^ 0b0101);
    println!("1 << 5 is {}", 1u32 << 5);
    println!("0x80 >> 2 is 0x{:x}", 0x80u32 >> 2);

    // Use underscores to improve readability!
    println!("One million is written as {}", 1_000_000u32);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- `1_000` underscores are for readability.
- Integer division truncates toward zero.

_RBE source: `primitives/literals.md`_

---

### 4.1.2 Tuples

**What it is**

A tuple is a collection of values of different types. Tuples are constructed
using parentheses `()`, and each tuple itself is a value with type signature
`(T1, T2, ...)`, where `T1`, `T2` are the types of its members. Functions can
use tuples to return multiple values, as tuples can hold any number of values. *Recap*: Add the `fmt::Display` trait to the `Matrix` struct in the above
   example, so that if you switch from printing the debug format `{:?}` to the
   display format `{}`, you see the following output:
   
   You may want to refer back to the example for [print display][print_display].

**JS mental model**

Like a fixed-length array / temporary object: `(a, b)`. Pattern match instead of indexing by name.

**Rust example**

```rust
// Tuples can be used as function arguments and as return values.
fn reverse(pair: (i32, bool)) -> (bool, i32) {
    // `let` can be used to bind the members of a tuple to variables.
    let (int_param, bool_param) = pair;

    (bool_param, int_param)
}

// The following struct is for the activity.
#[derive(Debug)]
struct Matrix(f32, f32, f32, f32);

fn main() {
    // A tuple with a bunch of different types.
    let long_tuple = (1u8, 2u16, 3u32, 4u64,
                      -1i8, -2i16, -3i32, -4i64,
                      0.1f32, 0.2f64,
                      'a', true);

    // Values can be extracted from the tuple using tuple indexing.
    println!("Long tuple first value: {}", long_tuple.0);
    println!("Long tuple second value: {}", long_tuple.1);

    // Tuples can be tuple members.
    let tuple_of_tuples = ((1u8, 2u16, 2u32), (4u64, -1i8), -2i16);

    // Tuples are printable.
    println!("tuple of tuples: {:?}", tuple_of_tuples);

    // But long Tuples (more than 12 elements) cannot be printed.
    //let too_long_tuple = (1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13);
    //println!("Too long tuple: {:?}", too_long_tuple);
    // TODO ^ Uncomment the above 2 lines to see the compiler error

    let pair = (1, true);
    println!("Pair is {:?}", pair);

    println!("The reversed pair is {:?}", reverse(pair));

    // To create one element tuples, the comma is required to tell them apart
    // from a literal surrounded by parentheses.
    println!("One element tuple: {:?}", (5u32,));
    println!("Just an integer: {:?}", (5u32));

    // Tuples can be destructured to create bindings.
    let tuple = (1, "hello", 4.5, true);

    let (a, b, c, d) = tuple;
    println!("{:?}, {:?}, {:?}, {:?}", a, b, c, d);

    let matrix = Matrix(1.1, 1.2, 2.1, 2.2);
    println!("{:?}", matrix);
}
```

```rust
println!("Matrix:\n{}", matrix);
   println!("Transpose:\n{}", transpose(matrix));
```

**JS equivalent**

```javascript
const tuple = /** @type {const} */ ([1, "hi"]);
const [a, b] = tuple;
```

**Watch out**

- Tuple fields are positional `.0`, `.1`.
- Unit `()` is like `undefined` as a real value (empty tuple).

_RBE source: `primitives/tuples.md`_

---

### 4.1.3 Arrays and Slices

**What it is**

An array is a collection of objects of the same type `T`, stored in contiguous
memory. Arrays are created using brackets `[]`, and their length, which is known
at compile time, is part of their type signature `[T; length]`. Slices are similar to arrays, but their length is not known at compile time. Instead, a slice is a two-word object; the first word is a pointer to the data,
the second word is the length of the slice.

**JS mental model**

JS arrays are dynamic. Rust `[T; N]` is fixed length on the stack; slices `&[T]` are views (like a typed subarray view).

**Rust example**

```rust
use std::mem;

// This function borrows a slice.
fn analyze_slice(slice: &[i32]) {
    println!("First element of the slice: {}", slice[0]);
    println!("The slice has {} elements", slice.len());
}

fn main() {
    // Fixed-size array (type signature is superfluous).
    let xs: [i32; 5] = [1, 2, 3, 4, 5];

    // All elements can be initialized to the same value.
    let ys: [i32; 500] = [0; 500];

    // Indexing starts at 0.
    println!("First element of the array: {}", xs[0]);
    println!("Second element of the array: {}", xs[1]);

    // `len` returns the count of elements in the array.
    println!("Number of elements in array: {}", xs.len());

    // Arrays are stack allocated.
    println!("Array occupies {} bytes", mem::size_of_val(&xs));

    // Arrays can be automatically borrowed as slices.
    println!("Borrow the whole array as a slice.");
    analyze_slice(&xs);

    // Slices can point to a section of an array.
    // They are of the form [starting_index..ending_index].
    // `starting_index` is the first position in the slice.
    // `ending_index` is one more than the last position in the slice.
    println!("Borrow a section of the array as a slice.");
    analyze_slice(&ys[1 .. 4]);

    // Example of empty slice `&[]`:
    let empty_array: [u32; 0] = [];
    assert_eq!(&empty_array, &[]);
    assert_eq!(&empty_array, &[][..]); // Same but more verbose

    // Arrays can be safely accessed using `.get`, which returns an
    // `Option`. This can be matched as shown below, or used with
    // `.expect()` if you would like the program to exit with a nice
    // message instead of happily continue.
    for i in 0..xs.len() + 1 { // Oops, one element too far!
        match xs.get(i) {
            Some(xval) => println!("{}: {}", i, xval),
            None => println!("Slow down! {} is too far!", i),
        }
    }

    // Out of bound indexing on array with constant value causes compile time error.
    //println!("{}", xs[5]);
    // Out of bound indexing on slice causes runtime error.
    //println!("{}", xs[..][5]);
}
```

**JS equivalent**

```javascript
const arr = [1, 2, 3]; // length can change
const view = arr.slice(0, 2);
```

**Watch out**

- Out-of-bounds index **panics** (no `undefined`).
- Use `Vec<T>` when length must grow.

_RBE source: `primitives/array.md`_

---

## 5. Custom types: struct, enum, constants

_RBE chapter root maps here. Every nested leaf below has the full 5-part teaching block._

### 5.1 Custom Types

**What it is**

Rust custom data types are formed mainly through the two keywords:
* `struct`: define a structure
* `enum`: define an enumeration
Constants can also be created via the `const` and `static` keywords.

**JS mental model**

JS objects/classes vs Rust `struct` + `enum`. Enums are tagged unions (like TS discriminated unions).

**Rust example**

```rust
struct Point { x: i32, y: i32 }
enum Msg { Quit, Move { x: i32, y: i32 } }

fn main() {
    let p = Point { x: 1, y: 2 };
    let _m = Msg::Quit;
    println!("{}, {}", p.x, p.y);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Fields are private by default outside the module.
- Prefer enums over magic strings/numbers.

_RBE source: `custom_types.md`_

---

### 5.1.1 Structures

**What it is**

There are three types of structures ("structs") that can be created using the
`struct` keyword:
* Tuple structs, which are, basically, named tuples. * The classic [C structs][c_struct]
* Unit structs, which are field-less, are useful for generics. Add a function `rect_area` which calculates the area of a `Rectangle` (try
   using nested destructuring). Add a function `square` which takes a `Point` and a `f32` as arguments, and
   returns a `Rectangle` with its top left corner on the point, and a width and
   height corresponding to the `f32`.

**JS mental model**

Like a class/object shape without inheritance. Methods live in `impl` blocks.

**Rust example**

```rust
// An attribute to hide warnings for unused code.
#![allow(dead_code)]

#[derive(Debug)]
struct Person {
    name: String,
    age: u8,
}

// A unit struct
struct Unit;

// A tuple struct
struct Pair(i32, f32);

// A struct with two fields
struct Point {
    x: f32,
    y: f32,
}

// Structs can be reused as fields of another struct
struct Rectangle {
    // A rectangle can be specified by where the top left and bottom right
    // corners are in space.
    top_left: Point,
    bottom_right: Point,
}

fn main() {
    // Create struct with field init shorthand
    let name = String::from("Peter");
    let age = 27;
    let peter = Person { name, age };

    // Print debug struct
    println!("{:?}", peter);

    // Instantiate a `Point`
    let point: Point = Point { x: 5.2, y: 0.4 };
    let another_point: Point = Point { x: 10.3, y: 0.2 };

    // Access the fields of the point
    println!("point coordinates: ({}, {})", point.x, point.y);

    // Make a new point by using struct update syntax to use the fields of our
    // other one
    let bottom_right = Point { x: 10.3, ..another_point };

    // `bottom_right.y` will be the same as `another_point.y` because we used that field
    // from `another_point`
    println!("second point: ({}, {})", bottom_right.x, bottom_right.y);

    // Destructure the point using a `let` binding
    let Point { x: left_edge, y: top_edge } = point;

    let _rectangle = Rectangle {
        // struct instantiation is an expression too
        top_left: Point { x: left_edge, y: top_edge },
        bottom_right: bottom_right,
    };

    // Instantiate a unit struct
    let _unit = Unit;

    // Instantiate a tuple struct
    let pair = Pair(1, 0.1);

    // Access the fields of a tuple struct
    println!("pair contains {:?} and {:?}", pair.0, pair.1);

    // Destructure a tuple struct
    let Pair(integer, decimal) = pair;

    println!("pair contains {:?} and {:?}", integer, decimal);
}
```

**JS equivalent**

```javascript
const p = { x: 1, y: 2 }; // no class required
```

**Watch out**

- Update syntax: `Point { x: 1, ..old }` like object spread but move/copy aware.
- Tuple structs and unit structs exist too.

_RBE source: `custom_types/structs.md`_

---

### 5.1.2 Enums

**What it is**

The `enum` keyword allows the creation of a type which may be one of a few
different variants. Any variant which is valid as a `struct` is also valid in
an `enum`. Type aliases
If you use a type alias, you can refer to each enum variant via its alias. This might be useful if the enum's name is too long or too generic, and you
want to rename it.

**JS mental model**

TS discriminated unions / algebraic data types. Each variant can carry different data.

**Rust example**

```rust
// Create an `enum` to classify a web event. Note how both
// names and type information together specify the variant:
// `PageLoad != PageUnload` and `KeyPress(char) != Paste(String)`.
// Each is different and independent.
enum WebEvent {
    // An `enum` variant may either be `unit-like`,
    PageLoad,
    PageUnload,
    // like tuple structs,
    KeyPress(char),
    Paste(String),
    // or c-like structures.
    Click { x: i64, y: i64 },
}

// A function which takes a `WebEvent` enum as an argument and
// returns nothing.
fn inspect(event: WebEvent) {
    match event {
        WebEvent::PageLoad => println!("page loaded"),
        WebEvent::PageUnload => println!("page unloaded"),
        // Destructure `c` from inside the `enum` variant.
        WebEvent::KeyPress(c) => println!("pressed '{}'.", c),
        WebEvent::Paste(s) => println!("pasted \"{}\".", s),
        // Destructure `Click` into `x` and `y`.
        WebEvent::Click { x, y } => {
            println!("clicked at x={}, y={}.", x, y);
        },
    }
}

fn main() {
    let pressed = WebEvent::KeyPress('x');
    // `to_owned()` creates an owned `String` from a string slice.
    let pasted  = WebEvent::Paste("my text".to_owned());
    let click   = WebEvent::Click { x: 20, y: 80 };
    let load    = WebEvent::PageLoad;
    let unload  = WebEvent::PageUnload;

    inspect(pressed);
    inspect(pasted);
    inspect(click);
    inspect(load);
    inspect(unload);
}
```

```rust
enum VeryVerboseEnumOfThingsToDoWithNumbers {
    Add,
    Subtract,
}

// Creates a type alias
type Operations = VeryVerboseEnumOfThingsToDoWithNumbers;

fn main() {
    // We can refer to each variant via its alias, not its long and inconvenient
    // name.
    let x = Operations::Add;
}
```

**JS equivalent**

```javascript
/** @typedef {{type:'Quit'}|{type:'Move',x:number,y:number}} Msg */
```

**Watch out**

- Must handle all variants in `match` (or use `_`).
- `Option` and `Result` are enums.

_RBE source: `custom_types/enum.md`_

---

#### 5.1.2.1 use

**What it is**

The `use` declaration can be used to avoid typing the full module path to access a name:
See also:
[`match`][match] and [`use`][use]
[use]: ../../mod/use.md
[match]: ../../flow_control/match.md.

**JS mental model**

`use Enum::Variant` is like importing an enum member so you can write `Variant` instead of `Enum::Variant`.

**Rust example**

```rust
// An attribute to hide warnings for unused code.
#![allow(dead_code)]

enum Stage {
    Beginner,
    Advanced,
}

enum Role {
    Student,
    Teacher,
}

fn main() {
    // Explicitly `use` each name so they are available without
    // manual scoping.
    use Stage::{Beginner, Advanced};
    // Automatically `use` each name inside `Role`.
    use Role::*;

    // Equivalent to `Stage::Beginner`.
    let stage = Beginner;
    // Equivalent to `Role::Student`.
    let role = Student;

    match stage {
        // Note the lack of scoping because of the explicit `use` above.
        Beginner => println!("Beginners are starting their learning journey!"),
        Advanced => println!("Advanced learners are mastering their subjects..."),
    }

    match role {
        // Note again the lack of scoping.
        Student => println!("Students are acquiring knowledge!"),
        Teacher => println!("Teachers are spreading knowledge!"),
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- `use` is compile-time name import, not runtime require.

_RBE source: `custom_types/enum/enum_use.md`_

---

#### 5.1.2.2 C-like

**What it is**

`enum` can also be used as C-like enums. See also:
[casting][cast]
[cast]: ../../types/cast.md.

**JS mental model**

Like a numeric enum in TS (`enum Color { Red = 0 }`).

**Rust example**

```rust
// An attribute to hide warnings for unused code.
#![allow(dead_code)]

// enum with implicit discriminator (starts at 0)
enum Number {
    Zero,
    One,
    Two,
}

// enum with explicit discriminator
enum Color {
    Red = 0xff0000,
    Green = 0x00ff00,
    Blue = 0x0000ff,
}

fn main() {
    // `enums` can be cast as integers.
    println!("zero is {}", Number::Zero as i32);
    println!("one is {}", Number::One as i32);

    println!("roses are #{:06x}", Color::Red as u32);
    println!("violets are #{:06x}", Color::Blue as u32);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Cast with `as` carefully.
- Prefer richer enums with data when possible.

_RBE source: `custom_types/enum/c_like.md`_

---

#### 5.1.2.3 Testcase: linked-list

**What it is**

A common way to implement a linked-list is via `enums`:
See also:
[`Box`][box] and [methods][methods]
[box]: ../../std/box.md
[methods]: ../../fn/methods.md.

**JS mental model**

Building a linked list with `enum List { Cons(i32, Box<List>), Nil }` — recursion needs `Box` because size must be known.

**Rust example**

```rust
use crate::List::*;

enum List {
    // Cons: Tuple struct that wraps an element and a pointer to the next node
    Cons(u32, Box<List>),
    // Nil: A node that signifies the end of the linked list
    Nil,
}

// Methods can be attached to an enum
impl List {
    // Create an empty list
    fn new() -> List {
        // `Nil` has type `List`
        Nil
    }

    // Consume a list, and return the same list with a new element at its front
    fn prepend(self, elem: u32) -> List {
        // `Cons` also has type List
        Cons(elem, Box::new(self))
    }

    // Return the length of the list
    fn len(&self) -> u32 {
        // `self` has to be matched, because the behavior of this method
        // depends on the variant of `self`
        // `self` has type `&List`, and `*self` has type `List`, matching on a
        // concrete type `T` is preferred over a match on a reference `&T`
        // after Rust 2018 you can use self here and tail (with no ref) below as well,
        // rust will infer &s and ref tail.
        // See https://doc.rust-lang.org/edition-guide/rust-2018/ownership-and-lifetimes/default-match-bindings.html
        match *self {
            // Can't take ownership of the tail, because `self` is borrowed;
            // instead take a reference to the tail
            // And it's a non-tail recursive call which may cause stack overflow for long lists.
            Cons(_, ref tail) => 1 + tail.len(),
            // Base Case: An empty list has zero length
            Nil => 0
        }
    }

    // Return representation of the list as a (heap allocated) string
    fn stringify(&self) -> String {
        match *self {
            Cons(head, ref tail) => {
                // `format!` is similar to `print!`, but returns a heap
                // allocated string instead of printing to the console
                format!("{}, {}", head, tail.stringify())
            },
            Nil => {
                format!("Nil")
            },
        }
    }
}

fn main() {
    // Create an empty linked list
    let mut list = List::new();

    // Prepend some elements
    list = list.prepend(1);
    list = list.prepend(2);
    list = list.prepend(3);

    // Show the final state of the list
    println!("linked list has length: {}", list.len());
    println!("{}", list.stringify());
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Recursive types need indirection (`Box`).
- In real code prefer `Vec` over hand-rolled lists.

_RBE source: `custom_types/enum/testcase_linked_list.md`_

---

### 5.1.3 constants

**What it is**

Rust has two different types of constants which can be declared in any scope
including global. Both require explicit type annotation:
* `const`: An unchangeable value (the common case). * `static`: A possibly mutable variable with [`'static`][static] lifetime. The static lifetime is inferred and does not have to be specified.

**JS mental model**

`const` is like a frozen compile-time constant. `static` is a long-lived global (careful with mut).

**Rust example**

```rust
// Globals are declared outside all other scopes.
static LANGUAGE: &str = "Rust";
const THRESHOLD: i32 = 10;

fn is_big(n: i32) -> bool {
    // Access constant in some function
    n > THRESHOLD
}

fn main() {
    let n = 16;

    // Access constant in the main thread
    println!("This is {}", LANGUAGE);
    println!("The threshold is {}", THRESHOLD);
    println!("{} is {}", n, if is_big(n) { "big" } else { "small" });

    // Error! Cannot modify a `const`.
    THRESHOLD = 5;
    // FIXME ^ Comment out this line
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- `const` can be inlined; `static` has a fixed address.
- `static mut` is unsafe — avoid.

_RBE source: `custom_types/constants.md`_

---

## 6. Variable bindings, mutability, scope, shadowing

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

## 7. Types, casting, inference, aliases

_RBE chapter root maps here. Every nested leaf below has the full 5-part teaching block._

### 7.1 Types

**What it is**

Rust provides several mechanisms to change or define the type of primitive and
user defined types. The following sections cover:
* [Casting] between primitive types
* Specifying the desired type of [literals]
* Using [type inference]
* [Aliasing] types
[Casting]: types/cast.md
[literals]: types/literals.md
[type inference]: types/inference.md
[Aliasing]: types/alias.md.

**JS mental model**

JS types are runtime tags. Rust types are checked at compile time and often erased at runtime (zero-cost).

**Rust example**

```rust
fn main() {
    let x: i32 = 5;
    let y = x as i64;
    println!("{x} {y}");
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Trust the compiler — fix type errors instead of casting blindly.

_RBE source: `types.md`_

---

### 7.1.1 Casting

**What it is**

Rust provides no implicit type conversion (coercion) between primitive types. But, explicit type conversion (casting) can be performed using the `as` keyword. Rules for converting between integral types follow C conventions generally,
except in cases where C has undefined behavior. The behavior of all casts
between integral types is well defined in Rust.

**JS mental model**

`as` casts are explicit (like Number(x) but more dangerous for integers). No implicit widening in many cases.

**Rust example**

```rust
// Suppress all errors from casts which overflow.
#![allow(overflowing_literals)]

fn main() {
    let decimal = 65.4321_f32;

    // Error! No implicit conversion
    let integer: u8 = decimal;
    // FIXME ^ Comment out this line

    // Explicit conversion
    let integer = decimal as u8;
    let character = integer as char;

    // Error! There are limitations in conversion rules.
    // A float cannot be directly converted to a char.
    let character = decimal as char;
    // FIXME ^ Comment out this line

    println!("Casting: {} -> {} -> {}", decimal, integer, character);

    // when casting any value to an unsigned type, T,
    // T::MAX + 1 is added or subtracted until the value
    // fits into the new type ONLY when the #![allow(overflowing_literals)]
    // lint is specified like above. Otherwise there will be a compiler error.

    // 1000 already fits in a u16
    println!("1000 as a u16 is: {}", 1000 as u16);

    // 1000 - 256 - 256 - 256 = 232
    // Under the hood, the first 8 least significant bits (LSB) are kept,
    // while the rest towards the most significant bit (MSB) get truncated.
    println!("1000 as a u8 is : {}", 1000 as u8);
    // -1 + 256 = 255
    println!("  -1 as a u8 is : {}", (-1i8) as u8);

    // For positive numbers, this is the same as the modulus
    println!("1000 mod 256 is : {}", 1000 % 256);

    // When casting to a signed type, the (bitwise) result is the same as
    // first casting to the corresponding unsigned type. If the most significant
    // bit of that value is 1, then the value is negative.

    // Unless it already fits, of course.
    println!(" 128 as a i16 is: {}", 128 as i16);

    // In boundary case 128 value in 8-bit two's complement representation is -128
    println!(" 128 as a i8 is : {}", 128 as i8);

    // repeating the example above
    // 1000 as u8 -> 232
    println!("1000 as a u8 is : {}", 1000 as u8);
    // and the value of 232 in 8-bit two's complement representation is -24
    println!(" 232 as a i8 is : {}", 232 as i8);

    // Since Rust 1.45, the `as` keyword performs a *saturating cast*
    // when casting from float to int. If the floating point value exceeds
    // the upper bound or is less than the lower bound, the returned value
    // will be equal to the bound crossed.

    // 300.0 as u8 is 255
    println!(" 300.0 as u8 is : {}", 300.0_f32 as u8);
    // -100.0 as u8 is 0
    println!("-100.0 as u8 is : {}", -100.0_f32 as u8);
    // nan as u8 is 0
    println!("   nan as u8 is : {}", f32::NAN as u8);

    // This behavior incurs a small runtime cost and can be avoided
    // with unsafe methods, however the results might overflow and
    // return **unsound values**. Use these methods wisely:
    unsafe {
        // 300.0 as u8 is 44
        println!(" 300.0 as u8 is : {}", 300.0_f32.to_int_unchecked::<u8>());
        // -100.0 as u8 is 156
        println!("-100.0 as u8 is : {}", (-100.0_f32).to_int_unchecked::<u8>());
        // nan as u8 is 0
        println!("   nan as u8 is : {}", f32::NAN.to_int_unchecked::<u8>());
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Casting can truncate. Prefer `From`/`TryFrom`.
- Float→int truncates toward zero.

_RBE source: `types/cast.md`_

---

### 7.1.2 Literals

**What it is**

Numeric literals can be type annotated by adding the type as a suffix. As an example,
to specify that the literal `42` should have the type `i32`, write `42i32`. The type of unsuffixed numeric literals will depend on how they are used. If no
constraint exists, the compiler will use `i32` for integers, and `f64` for
floating-point numbers.

**JS mental model**

Type suffixes (`42i32`) and inference from usage — TS-like inference but stricter.

**Rust example**

```rust
fn main() {
    // Suffixed literals, their types are known at initialization
    let x = 1u8;
    let y = 2u32;
    let z = 3f32;

    // Unsuffixed literals, their types depend on how they are used
    let i = 1;
    let f = 1.0;

    // `size_of_val` returns the size of a variable in bytes
    println!("size of `x` in bytes: {}", std::mem::size_of_val(&x));
    println!("size of `y` in bytes: {}", std::mem::size_of_val(&y));
    println!("size of `z` in bytes: {}", std::mem::size_of_val(&z));
    println!("size of `i` in bytes: {}", std::mem::size_of_val(&i));
    println!("size of `f` in bytes: {}", std::mem::size_of_val(&f));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- If inference fails, add a type annotation on `let` or a suffix.

_RBE source: `types/literals.md`_

---

### 7.1.3 Inference

**What it is**

The type inference engine is pretty smart. It does more than looking at the
type of the value expression
during an initialization. It also looks at how the variable is used afterwards
to infer its type. Here's an advanced example of type inference:
No type annotation of variables was needed, the compiler is happy and so is the
programmer!

**JS mental model**

Compiler fills in types from context — similar to TS inference, but must be unambiguous.

**Rust example**

```rust
fn main() {
    // Because of the annotation, the compiler knows that `elem` has type u8.
    let elem = 5u8;

    // Create an empty vector (a growable array).
    let mut vec = Vec::new();
    // At this point the compiler doesn't know the exact type of `vec`, it
    // just knows that it's a vector of something (`Vec<_>`).

    // Insert `elem` in the vector.
    vec.push(elem);
    // Aha! Now the compiler knows that `vec` is a vector of `u8`s (`Vec<u8>`)
    // TODO ^ Try commenting out the `vec.push(elem)` line

    println!("{:?}", vec);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Sometimes annotate the collection: `Vec::<i32>::new()` turbofish.

_RBE source: `types/inference.md`_

---

### 7.1.4 Aliasing

**What it is**

The `type` statement can be used to give a new name to an existing type. Types
must have `UpperCamelCase` names, or the compiler will raise a warning. The
exception to this rule are the primitive types: `usize`, `f32`, etc. The main use of aliases is to reduce boilerplate; for example the `io::Result<T>` type
is an alias for the `Result<T, io::Error>` type.

**JS mental model**

`type Name = Existing;` is like `type` aliases in TS — no new type, just a name.

**Rust example**

```rust
// `NanoSecond`, `Inch`, and `U64` are new names for `u64`.
type NanoSecond = u64;
type Inch = u64;
type U64 = u64;

fn main() {
    // `NanoSecond` = `Inch` = `U64` = `u64`.
    let nanoseconds: NanoSecond = 5 as u64;
    let inches: Inch = 2 as U64;

    // Note that type aliases *don't* provide any extra type safety, because
    // aliases are *not* new types
    println!("{} nanoseconds + {} inches = {} unit?",
             nanoseconds,
             inches,
             nanoseconds + inches);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Aliases do not create type safety; newtype idiom does (see Generics).

_RBE source: `types/alias.md`_

---

## 8. Conversion

_RBE chapter root maps here. Every nested leaf below has the full 5-part teaching block._

### 8.1 Conversion

**What it is**

Primitive types can be converted to each other through [casting]. Rust addresses conversion between custom types (i.e., `struct` and `enum`)
by the use of [traits]. The generic
conversions will use the [`From`] and [`Into`] traits. However there are more
specific ones for the more common cases, in particular when converting to and
from `String`s.

**JS mental model**

Prefer traits `From`/`Into`/`TryFrom` over ad-hoc convert functions — like well-known symbols / protocols.

**Rust example**

```rust
fn main() {
    let s = String::from("hello");
    let n: i32 = "42".parse().unwrap();
    println!("{s} {n}");
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Implement `From`; get `Into` for free.
- Fallible conversions use `TryFrom`.

_RBE source: `conversion.md`_

---

### 8.1.1 From and Into

**What it is**

The [`From`] and [`Into`] traits are inherently linked, and this is actually part of
its implementation. If you are able to convert type A from type B, then it
should be easy to believe that we should be able to convert type B to type A. `From`
The [`From`] trait allows for a type to define how to create itself from another
type, so providing a very simple mechanism for converting between several
types. There are numerous implementations of this trait within the standard
library for conversion of primitive and common types.

**JS mental model**

`From::from(x)` / `.into()` — like having a standard `from()` factory convention.

**Rust example**

```rust
use std::convert::Into;

#[derive(Debug)]
struct Number {
    value: i32,
}

impl Into<Number> for i32 {
    fn into(self) -> Number {
        Number { value: self }
    }
}

fn main() {
    let int = 5;
    // Try removing the type annotation
    let num: Number = int.into();
    println!("My number is {:?}", num);
}
```

```rust
use std::convert::From;

#[derive(Debug)]
struct Number {
    value: i32,
}

// Define `From`
impl From<i32> for Number {
    fn from(item: i32) -> Self {
        Number { value: item }
    }
}

fn main() {
    let int = 5;
    // use `Into`
    let num: Number = int.into();
    println!("My number is {:?}", num);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Blanket impl: `From<T> for U` implies `Into<U> for T`.

_RBE source: `conversion/from_into.md`_

---

### 8.1.2 TryFrom and TryInto

**What it is**

Similar to [`From` and `Into`][from-into], [`TryFrom`] and [`TryInto`] are
generic traits for converting between types. Unlike `From`/`Into`, the
`TryFrom`/`TryInto` traits are used for fallible conversions, and as such,
return [`Result`]s. [from-into]: from_into.html
[`TryFrom`]: https://doc.rust-lang.org/std/convert/trait.TryFrom.html
[`TryInto`]: https://doc.rust-lang.org/std/convert/trait.TryInto.html
[`Result`]: https://doc.rust-lang.org/std/result/enum.Result.html.

**JS mental model**

Like a `from` that can fail — returns `Result`.

**Rust example**

```rust
use std::convert::TryFrom;
use std::convert::TryInto;

#[derive(Debug, PartialEq)]
struct EvenNumber(i32);

impl TryFrom<i32> for EvenNumber {
    type Error = ();

    fn try_from(value: i32) -> Result<Self, Self::Error> {
        if value % 2 == 0 {
            Ok(EvenNumber(value))
        } else {
            Err(())
        }
    }
}

fn main() {
    // TryFrom

    assert_eq!(EvenNumber::try_from(8), Ok(EvenNumber(8)));
    assert_eq!(EvenNumber::try_from(5), Err(()));

    // TryInto

    let result: Result<EvenNumber, ()> = 8i32.try_into();
    assert_eq!(result, Ok(EvenNumber(8)));
    let result: Result<EvenNumber, ()> = 5i32.try_into();
    assert_eq!(result, Err(()));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Use for parsing / narrowing that might be invalid.

_RBE source: `conversion/try_from_try_into.md`_

---

### 8.1.3 To and from Strings

**What it is**

Converting to String
To convert any type to a `String` is as simple as implementing the [`ToString`]
trait for the type. Rather than doing so directly, you should implement the
[`fmt::Display`][Display] trait which automatically provides [`ToString`] and
also allows printing the type as discussed in the section on [`print!`][print]. Parsing a String
It's useful to convert strings into many types, but one of the more common string
operations is to convert them from string to number. The idiomatic approach to
this is to use the [`parse`] function and either to arrange for type inference or
to specify the type to parse using the 'turbofish' syntax.

**JS mental model**

`to_string()` / `Display` vs `String::from` / `parse::<T>()` — like `String(x)` and `JSON.parse` but typed.

**Rust example**

```rust
use std::num::ParseIntError;
use std::str::FromStr;

#[derive(Debug)]
struct Circle {
    radius: i32,
}

impl FromStr for Circle {
    type Err = ParseIntError;
    fn from_str(s: &str) -> Result<Self, Self::Err> {
        match s.trim().parse() {
            Ok(num) => Ok(Circle{ radius: num }),
            Err(e) => Err(e),
        }
    }
}

fn main() {
    let radius = "    3 ";
    let circle: Circle = radius.parse().unwrap();
    println!("{:?}", circle);
}
```

```rust
use std::fmt;

struct Circle {
    radius: i32
}

impl fmt::Display for Circle {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "Circle of radius {}", self.radius)
    }
}

fn main() {
    let circle = Circle { radius: 6 };
    println!("{}", circle.to_string());
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- `&str` → `String` allocates. Avoid repeated `to_string` in hot loops.
- `parse` needs a type hint.

_RBE source: `conversion/string.md`_

---

## 9. Expressions

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

## 10. Flow of Control

_RBE chapter root maps here. Every nested leaf below has the full 5-part teaching block._

### 10.1 Flow of Control

**What it is**

An integral part of any programming language are ways to modify control flow:
`if`/`else`, `for`, and others. Let's talk about them in Rust.

**JS mental model**

Same control flow vocabulary as JS, plus powerful `match` and loop labels.

**Rust example**

```rust
fn main() {
    let n = 5;
    if n > 0 { println!("positive"); }
    for i in 0..3 { println!("{i}"); }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- `if` is an expression — both branches must match types when used as a value.

_RBE source: `flow_control.md`_

---

### 10.1.1 if/else

**What it is**

Branching with `if`-`else` is similar to other languages. Unlike many of them,
the boolean condition doesn't need to be surrounded by parentheses, and each
condition is followed by a block. `if`-`else` conditionals are expressions,
and, all branches must return the same type.

**JS mental model**

Like JS ternary / if, but can return values: `let x = if c {1} else {2};`

**Rust example**

```rust
fn main() {
    let n = 5;

    if n < 0 {
        print!("{} is negative", n);
    } else if n > 0 {
        print!("{} is positive", n);
    } else {
        print!("{} is zero", n);
    }

    let big_n =
        if n < 10 && n > -10 {
            println!(", and is a small number, increase ten-fold");

            // This expression returns an `i32`.
            10 * n
        } else {
            println!(", and is a big number, halve the number");

            // This expression must return an `i32` as well.
            n / 2
            // TODO ^ Try suppressing this expression with a semicolon.
        };
    //   ^ Don't forget to put a semicolon here! All `let` bindings need it.

    println!("{} -> {}", n, big_n);
}
```

**JS equivalent**

```javascript
const n = x > 0 ? "pos" : "non-pos";
```

**Watch out**

- No truthy/falsy — condition must be `bool`.
- Parentheses around conditions are optional (and uncommon).

_RBE source: `flow_control/if_else.md`_

---

### 10.1.2 loop

**What it is**

Rust provides a `loop` keyword to indicate an infinite loop. The `break` statement can be used to exit a loop at anytime, whereas the
`continue` statement can be used to skip the rest of the iteration and start a
new one.

**JS mental model**

Infinite loop with `loop { }` — clearer than `while(true)`.

**Rust example**

```rust
fn main() {
    let mut count = 0u32;

    println!("Let's count until infinity!");

    // Infinite loop
    loop {
        count += 1;

        if count == 3 {
            println!("three");

            // Skip the rest of this iteration
            continue;
        }

        println!("{}", count);

        if count == 5 {
            println!("OK, that's enough");

            // Exit this loop
            break;
        }
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Use `break` / `continue`.
- Can `break value` to return a value from the loop.

_RBE source: `flow_control/loop.md`_

---

#### 10.1.2.1 Nesting and labels

**What it is**

It's possible to `break` or `continue` outer loops when dealing with nested
loops. In these cases, the loops must be annotated with some `'label`, and the
label must be passed to the `break`/`continue` statement.

**JS mental model**

Labeled loops: `'outer: loop` like labeled statements in JS (`outer: while…`).

**Rust example**

```rust
#![allow(unreachable_code, unused_labels)]

fn main() {
    'outer: loop {
        println!("Entered the outer loop");

        'inner: loop {
            println!("Entered the inner loop");

            // This would break only the inner loop
            //break;

            // This breaks the outer loop
            break 'outer;
        }

        println!("This point will never be reached");
    }

    println!("Exited the outer loop");
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Labels start with a quote: `'label`.
- `break 'outer` exits the labeled loop.

_RBE source: `flow_control/loop/nested.md`_

---

#### 10.1.2.2 Returning from loops

**What it is**

One of the uses of a `loop` is to retry an operation until it succeeds. If the
operation returns a value though, you might need to pass it to the rest of the
code: put it after the `break`, and it will be returned by the `loop`
expression.

**JS mental model**

`break expr` makes the `loop` expression equal `expr` — no JS equivalent built-in.

**Rust example**

```rust
fn main() {
    let mut counter = 0;

    let result = loop {
        counter += 1;

        if counter == 10 {
            break counter * 2;
        }
    };

    assert_eq!(result, 20);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Only `loop` (not `while`/`for`) can break with a value in stable idioms shown here.

_RBE source: `flow_control/loop/return.md`_

---

### 10.1.3 while

**What it is**

The `while` keyword can be used to run a loop while a condition is true. Let's write the infamous [FizzBuzz][fizzbuzz] using a `while` loop. [fizzbuzz]: https://en.wikipedia.org/wiki/Fizz_buzz.

**JS mental model**

Same idea as JS `while`.

**Rust example**

```rust
fn main() {
    // A counter variable
    let mut n = 1;

    // Loop while `n` is less than 101
    while n < 101 {
        if n % 15 == 0 {
            println!("fizzbuzz");
        } else if n % 3 == 0 {
            println!("fizz");
        } else if n % 5 == 0 {
            println!("buzz");
        } else {
            println!("{}", n);
        }

        // Increment counter
        n += 1;
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Prefer iterators/`for` when walking collections.

_RBE source: `flow_control/while.md`_

---

### 10.1.4 for and range

**What it is**

for and range
The `for in` construct can be used to iterate through an `Iterator`. One of the easiest ways to create an iterator is to use the range
notation `a..b`. This yields values from `a` (inclusive) to `b`
(exclusive) in steps of one. Let's write FizzBuzz using `for` instead of `while`.

**JS mental model**

`for x in xs` iterates by value/into_iter; use `.iter()` / `.iter_mut()` like choosing for-of vs indices.

**Rust example**

```rust
fn main() {
    let names = vec!["Bob", "Frank", "Ferris"];

    for name in names.into_iter() {
        match name {
            "Ferris" => println!("There is a rustacean among us!"),
            _ => println!("Hello {}", name),
        }
    }

    // `names` has been 'moved' and can no longer be used.
    // Try uncommenting the line below to see the compiler error:
    // println!("names: {:?}", names);
}
```

```rust
fn main() {
    let names = vec!["Bob", "Frank", "Ferris"];

    for name in names.iter() {
        match name {
            &"Ferris" => println!("There is a rustacean among us!"),
            // TODO ^ Try deleting the & and matching just "Ferris"
            _ => println!("Hello {}", name),
        }
    }

    println!("names: {:?}", names);
}
```

**JS equivalent**

```javascript
for (const n of [1,2,3]) console.log(n);
for (let i = 0; i < 5; i++) {}
```

**Watch out**

- `for i in 0..n` is a range (end exclusive).
- `0..=n` is inclusive end.

_RBE source: `flow_control/for.md`_

---

### 10.1.5 match

**What it is**

Rust provides pattern matching via the `match` keyword, which can be used like
a C `switch`. The first matching arm is evaluated and all possible values must be
covered.

**JS mental model**

Superpowered `switch` + destructuring. Exhaustiveness checked — missing cases are compile errors.

**Rust example**

```rust
fn main() {
    let number = 13;
    // TODO ^ Try different values for `number`

    println!("Tell me about {}", number);
    match number {
        // Match a single value
        1 => println!("One!"),
        // Match several values
        2 | 3 | 5 | 7 | 11 => println!("This is a prime"),
        // TODO ^ Try adding 13 to the list of prime values
        // Match an inclusive range
        13..=19 => println!("A teen"),
        // Handle the rest of cases
        _ => println!("Ain't special"),
        // TODO ^ Try commenting out this catch-all arm
    }

    let boolean = true;
    // Match is an expression too
    let binary = match boolean {
        // The arms of a match must cover all the possible values
        false => 0,
        true => 1,
        // TODO ^ Try commenting out one of these arms
    };

    println!("{} -> {}", boolean, binary);
}
```

**JS equivalent**

```javascript
switch (n) {
  case 1: break;
  default: break;
} // no exhaustiveness check
```

**Watch out**

- Patterns are not fall-through.
- Use `|` for or-patterns; `guards` for extra bool checks.

_RBE source: `flow_control/match.md`_

---

#### 10.1.5.1 Destructuring

**What it is**

A `match` block can destructure items in a variety of ways. * [Destructuring Tuples][tuple]
* [Destructuring Arrays and Slices][slice]
* [Destructuring Enums][enum]
* [Destructuring Pointers][refs]
* [Destructuring Structures][struct]
[enum]: destructuring/destructure_enum.md
[refs]: destructuring/destructure_pointers.md
[struct]: destructuring/destructure_structures.md
[tuple]: destructuring/destructure_tuple.md
[slice]: destructuring/destructure_slice.md
See also:
The Rust Reference for Destructuring.

**JS mental model**

Destructure inside match like nested object/array destructuring in JS `switch` — but safer and deeper.

**Rust example**

```rust
fn main() {
    let pair = (0, -2);
    match pair {
        (0, y) => println!("y={y}"),
        (x, 0) => println!("x={x}"),
        _ => println!("other"),
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Every variant/shape you care about gets its own arm.

_RBE source: `flow_control/match/destructuring.md`_

---

##### 10.1.5.1.1 tuples

**What it is**

Tuples can be destructured in a `match` as follows:
See also:
Tuples.

**JS mental model**

Like `const [a,b] = tuple` inside a match arm.

**Rust example**

```rust
fn main() {
    let triple = (0, -2, 3);
    // TODO ^ Try different values for `triple`

    println!("Tell me about {:?}", triple);
    // Match can be used to destructure a tuple
    match triple {
        // Destructure the second and third elements
        (0, y, z) => println!("First is `0`, `y` is {:?}, and `z` is {:?}", y, z),
        (1, ..)  => println!("First is `1` and the rest doesn't matter"),
        (.., 2)  => println!("last is `2` and the rest doesn't matter"),
        (3, .., 4)  => println!("First is `3`, last is `4`, and the rest doesn't matter"),
        // `..` can be used to ignore the rest of the tuple
        _      => println!("It doesn't matter what they are"),
        // `_` means don't bind the value to a variable
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- `_` ignores a part; `..` ignores the rest in longer patterns.

_RBE source: `flow_control/match/destructuring/destructure_tuple.md`_

---

##### 10.1.5.1.2 arrays/slices

**What it is**

Like tuples, arrays and slices can be destructured this way:
See also:
Arrays and Slices and Binding for `@` sigil.

**JS mental model**

Match on array/slice shapes: `[a, b, ..]` similar to rest syntax.

**Rust example**

```rust
fn main() {
    // Try changing the values in the array, or make it a slice!
    let array = [1, -2, 6];

    match array {
        // Binds the second and the third elements to the respective variables
        [0, second, third] =>
            println!("array[0] = 0, array[1] = {}, array[2] = {}", second, third),

        // Single values can be ignored with _
        [1, _, third] => println!(
            "array[0] = 1, array[2] = {} and array[1] was ignored",
            third
        ),

        // You can also bind some and ignore the rest
        [-1, second, ..] => println!(
            "array[0] = -1, array[1] = {} and all the other ones were ignored",
            second
        ),
        // The code below would not compile
        // [-1, second] => ...

        // Or store them in another array/slice (the type depends on
        // that of the value that is being matched against)
        [3, second, tail @ ..] => println!(
            "array[0] = 3, array[1] = {} and the other elements were {:?}",
            second, tail
        ),

        // Combining these patterns, we can, for example, bind the first and
        // last values, and store the rest of them in a single array
        [first, middle @ .., last] => println!(
            "array[0] = {}, middle = {:?}, array[2] = {}",
            first, middle, last
        ),
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Length-sensitive patterns are checked.

_RBE source: `flow_control/match/destructuring/destructure_slice.md`_

---

##### 10.1.5.1.3 enums

**What it is**

An `enum` is destructured similarly:
See also:
[`#[allow(...)]`][allow], [color models][color_models] and [`enum`][enum]
[allow]: ../../../attribute/unused.md
[color_models]: https://en.wikipedia.org/wiki/Color_model
[enum]: ../../../custom_types/enum.md.

**JS mental model**

Like matching a TS discriminated union: `Msg::Move { x, y } => …`.

**Rust example**

```rust
// `allow` required to silence warnings because only
// one variant is used.
#[allow(dead_code)]
enum Color {
    // These 3 are specified solely by their name.
    Red,
    Blue,
    Green,
    // These likewise tie `u32` tuples to different names: color models.
    RGB(u32, u32, u32),
    HSV(u32, u32, u32),
    HSL(u32, u32, u32),
    CMY(u32, u32, u32),
    CMYK(u32, u32, u32, u32),
}

fn main() {
    let color = Color::RGB(122, 17, 40);
    // TODO ^ Try different variants for `color`

    println!("What color is it?");
    // An `enum` can be destructured using a `match`.
    match color {
        Color::Red   => println!("The color is Red!"),
        Color::Blue  => println!("The color is Blue!"),
        Color::Green => println!("The color is Green!"),
        Color::RGB(r, g, b) =>
            println!("Red: {}, green: {}, and blue: {}!", r, g, b),
        Color::HSV(h, s, v) =>
            println!("Hue: {}, saturation: {}, value: {}!", h, s, v),
        Color::HSL(h, s, l) =>
            println!("Hue: {}, saturation: {}, lightness: {}!", h, s, l),
        Color::CMY(c, m, y) =>
            println!("Cyan: {}, magenta: {}, yellow: {}!", c, m, y),
        Color::CMYK(c, m, y, k) =>
            println!("Cyan: {}, magenta: {}, yellow: {}, key (black): {}!",
                c, m, y, k),
        // Don't need another arm because all variants have been examined
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- After move-out of data from an enum, ownership rules apply.

_RBE source: `flow_control/match/destructuring/destructure_enum.md`_

---

##### 10.1.5.1.4 pointers/ref

**What it is**

For pointers, a distinction needs to be made between destructuring
and dereferencing as they are different concepts which are used
differently from languages like C/C++. * Dereferencing uses `*`
* Destructuring uses `&`, `ref`, and `ref mut`
See also:
The ref pattern.

**JS mental model**

Matching references uses `ref` / `ref mut` or `&` patterns — no JS pointer concept.

**Rust example**

```rust
fn main() {
    // Assign a reference of type `i32`. The `&` signifies there
    // is a reference being assigned.
    let reference = &4;

    match reference {
        // If `reference` is pattern matched against `&val`, it results
        // in a comparison like:
        // `&i32`
        // `&val`
        // ^ We see that if the matching `&`s are dropped, then the `i32`
        // should be assigned to `val`.
        &val => println!("Got a value via destructuring: {:?}", val),
    }

    // To avoid the `&`, you dereference before matching.
    match *reference {
        val => println!("Got a value via dereferencing: {:?}", val),
    }

    // What if you don't start with a reference? `reference` was a `&`
    // because the right side was already a reference. This is not
    // a reference because the right side is not one.
    let _not_a_reference = 3;

    // Rust provides `ref` for exactly this purpose. It modifies the
    // assignment so that a reference is created for the element; this
    // reference is assigned.
    let ref _is_a_reference = 3;

    // Accordingly, by defining 2 values without references, references
    // can be retrieved via `ref` and `ref mut`.
    let value = 5;
    let mut mut_value = 6;

    // Use `ref` keyword to create a reference.
    match value {
        ref r => println!("Got a reference to a value: {:?}", r),
    }

    // Use `ref mut` similarly.
    match mut_value {
        ref mut m => {
            // Got a reference. Gotta dereference it before we can
            // add anything to it.
            *m += 10;
            println!("We added 10. `mut_value`: {:?}", m);
        },
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Prefer modern `&` patterns; know `ref` when reading older code.
- Deref matching can be subtle — read compiler suggestions.

_RBE source: `flow_control/match/destructuring/destructure_pointers.md`_

---

##### 10.1.5.1.5 structs

**What it is**

Similarly, a `struct` can be destructured as shown:
See also:
Structs.

**JS mental model**

Object destructuring: `Person { name, age } => …`.

**Rust example**

```rust
fn main() {
    struct Foo {
        x: (u32, u32),
        y: u32,
    }

    // Try changing the values in the struct to see what happens
    let foo = Foo { x: (1, 2), y: 3 };

    match foo {
        Foo { x: (1, b), y } => println!("First of x is 1, b = {},  y = {} ", b, y),

        // you can destructure structs and rename the variables,
        // the order is not important
        Foo { y: 2, x: i } => println!("y is 2, i = {:?}", i),

        // and you can also ignore some variables:
        Foo { y, .. } => println!("y = {}, we don't care about x", y),
        // this will give an error: pattern does not mention field `x`
        //Foo { y } => println!("y = {}", y),
    }

    let faa = Foo { x: (1, 2), y: 3 };

    // You do not need a match block to destructure structs:
    let Foo { x : x0, y: y0 } = faa;
    println!("Outside: x0 = {x0:?}, y0 = {y0}");

    // Destructuring works with nested structs as well:
    struct Bar {
        foo: Foo,
    }

    let bar = Bar { foo: faa };
    let Bar { foo: Foo { x: nested_x, y: nested_y } } = bar;
    println!("Nested: nested_x = {nested_x:?}, nested_y = {nested_y:?}");
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- `..` ignores remaining fields.
- Rename: `name: n`.

_RBE source: `flow_control/match/destructuring/destructure_structures.md`_

---

#### 10.1.5.2 Guards

**What it is**

A `match` *guard* can be added to filter the arm. Note that the compiler won't take guard conditions into account when checking
if all patterns are covered by the match expression. See also:
Tuples
Enums.

**JS mental model**

Match arm + `if condition` — like `case` with an extra if.

**Rust example**

```rust
#[allow(dead_code)]
enum Temperature {
    Celsius(i32),
    Fahrenheit(i32),
}

fn main() {
    let temperature = Temperature::Celsius(35);
    // ^ TODO try different values for `temperature`

    match temperature {
        Temperature::Celsius(t) if t > 30 => println!("{}C is above 30 Celsius", t),
        // The `if condition` part ^ is a guard
        Temperature::Celsius(t) => println!("{}C is equal to or below 30 Celsius", t),

        Temperature::Fahrenheit(t) if t > 86 => println!("{}F is above 86 Fahrenheit", t),
        Temperature::Fahrenheit(t) => println!("{}F is equal to or below 86 Fahrenheit", t),
    }
}
```

```rust
fn main() {
    let number: u8 = 4;

    match number {
        i if i == 0 => println!("Zero"),
        i if i > 0 => println!("Greater than zero"),
        // _ => unreachable!("Should never happen."),
        // TODO ^ uncomment to fix compilation
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Guards cannot introduce new bindings the same way patterns do — bind first, then guard.

_RBE source: `flow_control/match/guard.md`_

---

#### 10.1.5.3 Binding

**What it is**

Indirectly accessing a variable makes it impossible to branch and use that
variable without re-binding. `match` provides the `@` sigil for binding values to
names:
You can also use binding to "destructure" `enum` variants, such as `Option`:
See also:
[`functions`][functions], [`enums`][enums] and [`Option`][option]
[functions]: ../../fn.md
[enums]: ../../custom_types/enum.md
[option]: ../../std/option.md.

**JS mental model**

`@` binds while also matching a pattern: `n @ 1..=5` — bind the value and check range.

**Rust example**

```rust
// A function `age` which returns a `u32`.
fn age() -> u32 {
    15
}

fn main() {
    println!("Tell me what type of person you are");

    match age() {
        0             => println!("I haven't celebrated my first birthday yet"),
        // Could `match` 1 ..= 12 directly but then what age
        // would the child be?
        // Could `match` n and use an `if` guard, but would
        // not contribute to exhaustiveness checks.
        // (Although in this case that would not matter since
        // a "catch-all" pattern is present at the bottom)
        // Instead, bind to `n` for the sequence of 1 ..= 12.
        // Now the age can be reported.
        n @ 1  ..= 12 => println!("I'm a child of age {:?}", n),
        n @ 13 ..= 19 => println!("I'm a teen of age {:?}", n),
        // A similar binding can be done when matching several values.
        n @ (1 | 7 | 15 | 13) => println!("I'm a teen of age {:?}", n),
        // Nothing bound. Return the result.
        n             => println!("I'm an old person of age {:?}", n),
    }
}
```

```rust
fn some_number() -> Option<u32> {
    Some(42)
}

fn main() {
    match some_number() {
        // Got `Some` variant, match if its value, bound to `n`,
        // is equal to 42.
        // Could also use `Some(42)` and print `"The Answer: 42!"`
        // but that would require changing `42` in 2 spots should
        // you ever wish to change it.
        // Could also use `Some(n) if n == 42` and print `"The Answer: {n}!"`
        // but that would not contribute to exhaustiveness checks.
        // (Although in this case that would not matter since
        // the next arm is a "catch-all" pattern)
        Some(n @ 42) => println!("The Answer: {}!", n),
        // Match any other number.
        Some(n)      => println!("Not interesting... {}", n),
        // Match anything else (`None` variant).
        _            => (),
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Useful when you need the whole value and a sub-pattern.

_RBE source: `flow_control/match/binding.md`_

---

### 10.1.6 if let

**What it is**

For some use cases, when matching enums, `match` is awkward. For example:
`if let` is cleaner for this use case and in addition allows various
failure options to be specified:
In the same way, `if let` can be used to match any enum value:
Another benefit is that `if let` allows us to match non-parameterized enum variants. This is true even in cases where the enum doesn't implement or derive `PartialEq`. In such cases `if Foo::Bar == a` would fail to compile, because instances of the enum cannot be equated, however `if let` will continue to work.

**JS mental model**

Sugar for matching one pattern: `if let Some(x) = opt { }` ≈ `if (opt != null)`.

**Rust example**

```rust
fn main() {
    // All have type `Option<i32>`
    let number = Some(7);
    let letter: Option<i32> = None;
    let emoticon: Option<i32> = None;

    // The `if let` construct reads: "if `let` destructures `number` into
    // `Some(i)`, evaluate the block (`{}`).
    if let Some(i) = number {
        println!("Matched {:?}!", i);
    }

    // If you need to specify a failure, use an else:
    if let Some(i) = letter {
        println!("Matched {:?}!", i);
    } else {
        // Destructure failed. Change to the failure case.
        println!("Didn't match a number. Let's go with a letter!");
    }

    // Provide an altered failing condition.
    let i_like_letters = false;

    if let Some(i) = emoticon {
        println!("Matched {:?}!", i);
    // Destructure failed. Evaluate an `else if` condition to see if the
    // alternate failure branch should be taken:
    } else if i_like_letters {
        println!("Didn't match a number. Let's go with a letter!");
    } else {
        // The condition evaluated false. This branch is the default:
        println!("I don't like letters. Let's go with an emoticon :)!");
    }
}
```

```rust
// Our example enum
enum Foo {
    Bar,
    Baz,
    Qux(u32)
}

fn main() {
    // Create example variables
    let a = Foo::Bar;
    let b = Foo::Baz;
    let c = Foo::Qux(100);

    // Variable a matches Foo::Bar
    if let Foo::Bar = a {
        println!("a is foobar");
    }

    // Variable b does not match Foo::Bar
    // So this will print nothing
    if let Foo::Bar = b {
        println!("b is foobar");
    }

    // Variable c matches Foo::Qux which has a value
    // Similar to Some() in the previous example
    if let Foo::Qux(value) = c {
        println!("c is {}", value);
    }

    // Binding also works with `if let`
    if let Foo::Qux(value @ 100) = c {
        println!("c is one hundred");
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Use when `match` would have one useful arm + `_ => {}`.

_RBE source: `flow_control/if_let.md`_

---

### 10.1.7 let-else

**What it is**

> 🛈 stable since: rust 1.65
>
> 🛈 you can target specific edition by compiling like this
> `rustc --edition=2021 main.rs`
With `let`-`else`, a refutable pattern can match and bind variables
in the surrounding scope like a normal `let`, or else diverge (e.g. `break`,
`return`, `panic!`) when the pattern doesn't match. The scope of name bindings is the main thing that makes this different from
`match` or `if let`-`else` expressions. You could previously approximate these
patterns with an unfortunate bit of repetition and an outer `let`:
See also:
[option][option], [match][match], [if let][if_let] and the [let-else RFC][let_else_rfc].

**JS mental model**

`let Some(x) = opt else { return };` — early-exit sugar like a guard clause.

**Rust example**

```rust
use std::str::FromStr;
#
fn get_count_item(s: &str) -> (u64, &str) {
    let mut it = s.split(' ');
    let (count_str, item) = match (it.next(), it.next()) {
        (Some(count_str), Some(item)) => (count_str, item),
        _ => panic!("Can't segment count item pair: '{s}'"),
    };
    let count = if let Ok(count) = u64::from_str(count_str) {
        count
    } else {
        panic!("Can't parse integer: '{count_str}'");
    };
    (count, item)
}
#
assert_eq!(get_count_item("3 chairs"), (3, "chairs"));
```

```rust
use std::str::FromStr;

fn get_count_item(s: &str) -> (u64, &str) {
    let mut it = s.split(' ');
    let (Some(count_str), Some(item)) = (it.next(), it.next()) else {
        panic!("Can't segment count item pair: '{s}'");
    };
    let Ok(count) = u64::from_str(count_str) else {
        panic!("Can't parse integer: '{count_str}'");
    };
    (count, item)
}

fn main() {
    assert_eq!(get_count_item("3 chairs"), (3, "chairs"));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- `else` branch must diverge (`return`/`break`/`panic`).

_RBE source: `flow_control/let_else.md`_

---

### 10.1.8 while let

**What it is**

Similar to `if let`, `while let` can make awkward `match` sequences
more tolerable. Consider the following sequence that increments `i`:
Using `while let` makes this sequence much nicer:
See also:
[`enum`][enum], [`Option`][option], and the [RFC][while_let_rfc]
[enum]: ../custom_types/enum.md
[option]: ../std/option.md
[while_let_rfc]: https://github.com/rust-lang/rfcs/pull/214.

**JS mental model**

Loop while a pattern matches — great for popping stacks/iterators.

**Rust example**

```rust
fn main() {
    // Make `optional` of type `Option<i32>`
    let mut optional = Some(0);

    // This reads: "while `let` destructures `optional` into
    // `Some(i)`, evaluate the block (`{}`). Else `break`.
    while let Some(i) = optional {
        if i > 9 {
            println!("Greater than 9, quit!");
            optional = None;
        } else {
            println!("`i` is `{:?}`. Try again.", i);
            optional = Some(i + 1);
        }
        // ^ Less rightward drift and doesn't require
        // explicitly handling the failing case.
    }
    // ^ `if let` had additional optional `else`/`else if`
    // clauses. `while let` does not have these.
}
```

```rust
// Make `optional` of type `Option<i32>`
let mut optional = Some(0);

// Repeatedly try this test.
loop {
    match optional {
        // If `optional` destructures, evaluate the block.
        Some(i) => {
            if i > 9 {
                println!("Greater than 9, quit!");
                optional = None;
            } else {
                println!("`i` is `{:?}`. Try again.", i);
                optional = Some(i + 1);
            }
            // ^ Requires 3 indentations!
        },
        // Quit the loop when the destructure fails:
        _ => { break; }
        // ^ Why should this be required? There must be a better way!
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Common with `iter.next()` / channels.

_RBE source: `flow_control/while_let.md`_

---

## 11. Functions, methods, closures, HOFs

_RBE chapter root maps here. Every nested leaf below has the full 5-part teaching block._

### 11.1 Functions

**What it is**

Functions are declared using the `fn` keyword. Its arguments are type
annotated, just like variables, and, if the function returns a value, the
return type must be specified after an arrow `->`. The final expression in the function will be used as return value. Alternatively, the `return` statement can be used to return a value earlier
from within the function, even from inside loops or `if` statements.

**JS mental model**

Functions are like JS functions but with typed params/returns. No default params — use `Option` or builders.

**Rust example**

```rust
// Unlike C/C++, there's no restriction on the order of function definitions
fn main() {
    // We can use this function here, and define it somewhere later
    fizzbuzz_to(100);
}

// Function that returns a boolean value
fn is_divisible_by(lhs: u32, rhs: u32) -> bool {
    // Corner case, early return
    if rhs == 0 {
        return false;
    }

    // This is an expression, the `return` keyword is not necessary here
    lhs % rhs == 0
}

// Functions that "don't" return a value, actually return the unit type `()`
fn fizzbuzz(n: u32) -> () {
    if is_divisible_by(n, 15) {
        println!("fizzbuzz");
    } else if is_divisible_by(n, 3) {
        println!("fizz");
    } else if is_divisible_by(n, 5) {
        println!("buzz");
    } else {
        println!("{}", n);
    }
}

// When a function returns `()`, the return type can be omitted from the
// signature
fn fizzbuzz_to(n: u32) {
    for n in 1..=n {
        fizzbuzz(n);
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Return type after `->`.
- Last expression without `;` is the return value.

_RBE source: `fn.md`_

---

### 11.1.1 Methods

**What it is**

Some functions are connected to a particular type. These come in two forms:
associated functions, and methods. Associated functions are functions that
are defined on a type generally, while methods are associated functions that are
called on a particular instance of a type.

**JS mental model**

`impl Type { fn method(&self) }` ≈ class methods. Associated fns like `Type::new` ≈ static methods.

**Rust example**

```rust
struct Point {
    x: f64,
    y: f64,
}

// Implementation block, all `Point` associated functions & methods go in here
impl Point {
    // This is an "associated function" because this function is associated with
    // a particular type, that is, Point.
    //
    // Associated functions don't need to be called with an instance.
    // These functions are generally used like constructors.
    fn origin() -> Point {
        Point { x: 0.0, y: 0.0 }
    }

    // Another associated function, taking two arguments:
    fn new(x: f64, y: f64) -> Point {
        Point { x: x, y: y }
    }
}

struct Rectangle {
    p1: Point,
    p2: Point,
}

impl Rectangle {
    // This is a method
    // `&self` is sugar for `self: &Self`, where `Self` is the type of the
    // caller object. In this case `Self` = `Rectangle`
    fn area(&self) -> f64 {
        // `self` gives access to the struct fields via the dot operator
        let Point { x: x1, y: y1 } = self.p1;
        let Point { x: x2, y: y2 } = self.p2;

        // `abs` is a `f64` method that returns the absolute value of the
        // caller
        ((x1 - x2) * (y1 - y2)).abs()
    }

    fn perimeter(&self) -> f64 {
        let Point { x: x1, y: y1 } = self.p1;
        let Point { x: x2, y: y2 } = self.p2;

        2.0 * ((x1 - x2).abs() + (y1 - y2).abs())
    }

    // This method requires the caller object to be mutable
    // `&mut self` desugars to `self: &mut Self`
    fn translate(&mut self, x: f64, y: f64) {
        self.p1.x += x;
        self.p2.x += x;

        self.p1.y += y;
        self.p2.y += y;
    }
}

// `Pair` owns resources: two heap allocated integers
struct Pair(Box<i32>, Box<i32>);

impl Pair {
    // This method "consumes" the resources of the caller object
    // `self` desugars to `self: Self`
    fn destroy(self) {
        // Destructure `self`
        let Pair(first, second) = self;

        println!("Destroying Pair({}, {})", first, second);

        // `first` and `second` go out of scope and get freed
    }
}

fn main() {
    let rectangle = Rectangle {
        // Associated functions are called using double colons
        p1: Point::origin(),
        p2: Point::new(3.0, 4.0),
    };

    // Methods are called using the dot operator
    // Note that the first argument `&self` is implicitly passed, i.e.
    // `rectangle.perimeter()` === `Rectangle::perimeter(&rectangle)`
    println!("Rectangle perimeter: {}", rectangle.perimeter());
    println!("Rectangle area: {}", rectangle.area());

    let mut square = Rectangle {
        p1: Point::origin(),
        p2: Point::new(1.0, 1.0),
    };

    // Error! `rectangle` is immutable, but this method requires a mutable
    // object
    //rectangle.translate(1.0, 0.0);
    // TODO ^ Try uncommenting this line

    // Okay! Mutable objects can call mutable methods
    square.translate(1.0, 1.0);

    let pair = Pair(Box::new(1), Box::new(2));

    pair.destroy();

    // Error! Previous `destroy` call "consumed" `pair`
    //pair.destroy();
    // TODO ^ Try uncommenting this line
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- `&self` / `&mut self` / `self` choose borrow vs move.
- No implicit `this`.

_RBE source: `fn/methods.md`_

---

### 11.1.2 Closures

**What it is**

Closures are functions that can capture the enclosing environment. For
example, a closure that captures the `x` variable:
The syntax and capabilities of closures make them very convenient for
on the fly usage. Calling a closure is exactly like calling a function. However, both input and return types *can* be inferred and input
variable names *must* be specified.

**JS mental model**

Arrow functions: `|x| x + 1`. Can capture environment.

**Rust example**

```rust
fn main() {
    let outer_var = 42;

    // A regular function can't refer to variables in the enclosing environment
    //fn function(i: i32) -> i32 { i + outer_var }
    // TODO: uncomment the line above and see the compiler error. The compiler
    // suggests that we define a closure instead.

    // Closures are anonymous, here we are binding them to references.
    // Annotation is identical to function annotation but is optional
    // as are the `{}` wrapping the body. These nameless functions
    // are assigned to appropriately named variables.
    let closure_annotated = |i: i32| -> i32 { i + outer_var };
    let closure_inferred  = |i     |          i + outer_var  ;

    // Call the closures.
    println!("closure_annotated: {}", closure_annotated(1));
    println!("closure_inferred: {}", closure_inferred(1));
    // Once closure's type has been inferred, it cannot be inferred again with another type.
    //println!("cannot reuse closure_inferred with another type: {}", closure_inferred(42i64));
    // TODO: uncomment the line above and see the compiler error.

    // A closure taking no arguments which returns an `i32`.
    // The return type is inferred.
    let one = || 1;
    println!("closure returning one: {}", one());

}
```

**JS equivalent**

```javascript
const add = (a, b) => a + b;
```

**Watch out**

- Closures have unique anonymous types.
- Often passed as `impl Fn…` or boxed `Box<dyn Fn…>`.

_RBE source: `fn/closures.md`_

---

#### 11.1.2.1 Capturing

**What it is**

Closures are inherently flexible and will do what the functionality requires
to make the closure work without annotation. This allows capturing to
flexibly adapt to the use case, sometimes moving and sometimes borrowing. Closures can capture variables:
* by reference: `&T`
* by mutable reference: `&mut T`
* by value: `T`
They preferentially capture variables by reference and only go lower when
required. Using `move` before vertical pipes forces closure
to take ownership of captured variables:
See also:
[`Box`][box] and [`std::mem::drop`][drop]
[box]: ../../std/box.md
[drop]: https://doc.rust-lang.org/std/mem/fn.drop.html.

**JS mental model**

Captures by reference by default; `move` forces ownership into the closure (like binding vars before async).

**Rust example**

```rust
fn main() {
    use std::mem;

    let color = String::from("green");

    // A closure to print `color` which immediately borrows (`&`) `color` and
    // stores the borrow and closure in the `print` variable. It will remain
    // borrowed until `print` is used the last time.
    //
    // `println!` only requires arguments by immutable reference so it doesn't
    // impose anything more restrictive.
    let print = || println!("`color`: {}", color);

    // Call the closure using the borrow.
    print();

    // `color` can be borrowed immutably again, because the closure only holds
    // an immutable reference to `color`.
    let _reborrow = &color;
    print();

    // A move or reborrow is allowed after the final use of `print`
    let _color_moved = color;


    let mut count = 0;
    // A closure to increment `count` could take either `&mut count` or `count`
    // but `&mut count` is less restrictive so it takes that. Immediately
    // borrows `count`.
    //
    // A `mut` is required on `inc` because a `&mut` is stored inside. Thus,
    // calling the closure mutates `count` which requires a `mut`.
    let mut inc = || {
        count += 1;
        println!("`count`: {}", count);
    };

    // Call the closure using a mutable borrow.
    inc();

    // The closure still mutably borrows `count` because it is called later.
    // An attempt to reborrow will lead to an error.
    // let _reborrow = &count;
    // ^ TODO: try uncommenting this line.
    inc();

    // The closure no longer needs to borrow `&mut count`. Therefore, it is
    // possible to reborrow without an error
    let _count_reborrowed = &mut count;


    // A non-copy type.
    let movable = Box::new(3);

    // `mem::drop` requires `T` so this must take by value. A copy type
    // would copy into the closure leaving the original untouched.
    // A non-copy must move and so `movable` immediately moves into
    // the closure.
    let consume = || {
        println!("`movable`: {:?}", movable);
        mem::drop(movable);
    };

    // `consume` consumes the variable so this can only be called once.
    consume();
    // consume();
    // ^ TODO: Try uncommenting this line.
}
```

```rust
fn main() {
    // `Vec` has non-copy semantics.
    let haystack = vec![1, 2, 3];

    let contains = move |needle| haystack.contains(needle);

    println!("{}", contains(&1));
    println!("{}", contains(&4));

    // println!("There're {} elements in vec", haystack.len());
    // ^ Uncommenting above line will result in compile-time error
    // because borrow checker doesn't allow re-using variable after it
    // has been moved.

    // Removing `move` from closure's signature will cause closure
    // to borrow _haystack_ variable immutably, hence _haystack_ is still
    // available and uncommenting above line will not cause an error.
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- `move` is common when spawning threads.
- Capture mode inferred from usage.

_RBE source: `fn/closures/capture.md`_

---

#### 11.1.2.2 As input parameters

**What it is**

While Rust chooses how to capture variables on the fly mostly without type
annotation, this ambiguity is not allowed when writing functions. When
taking a closure as an input parameter, the closure's complete type must be
annotated using one of a few `traits`, and they're determined by what the
closure does with captured value. In order of decreasing restriction,
they are:
* `Fn`: the closure uses the captured value by reference (`&T`)
* `FnMut`: the closure uses the captured value by mutable reference (`&mut T`)
* `FnOnce`: the closure uses the captured value by value (`T`)
On a variable-by-variable basis, the compiler will capture variables in the
least restrictive manner possible. For instance, consider a parameter annotated as `FnOnce`.

**JS mental model**

`Fn` / `FnMut` / `FnOnce` — how the closure may use captures. Like knowing if a callback is sync-pure vs mutating.

**Rust example**

```rust
// A function which takes a closure as an argument and calls it.
// <F> denotes that F is a "Generic type parameter"
fn apply<F>(f: F) where
    // The closure takes no input and returns nothing.
    F: FnOnce() {
    // ^ TODO: Try changing this to `Fn` or `FnMut`.

    f();
}

// A function which takes a closure and returns an `i32`.
fn apply_to_3<F>(f: F) -> i32 where
    // The closure takes an `i32` and returns an `i32`.
    F: Fn(i32) -> i32 {

    f(3)
}

fn main() {
    use std::mem;

    let greeting = "hello";
    // A non-copy type.
    // `to_owned` creates owned data from borrowed one
    let mut farewell = "goodbye".to_owned();

    // Capture 2 variables: `greeting` by reference and
    // `farewell` by value.
    let diary = || {
        // `greeting` is by reference: requires `Fn`.
        println!("I said {}.", greeting);

        // Mutation forces `farewell` to be captured by
        // mutable reference. Now requires `FnMut`.
        farewell.push_str("!!!");
        println!("Then I screamed {}.", farewell);
        println!("Now I can sleep. zzzzz");

        // Manually calling drop forces `farewell` to
        // be captured by value. Now requires `FnOnce`.
        mem::drop(farewell);
    };

    // Call the function which applies the closure.
    apply(diary);

    // `double` satisfies `apply_to_3`'s trait bound
    let double = |x| 2 * x;

    println!("3 doubled: {}", apply_to_3(double));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- `FnOnce` can run once (consumes captures).
- Prefer the least restrictive bound you need? Actually take the most general that still works — usually `FnOnce` as a parameter bound is most accepting.

_RBE source: `fn/closures/input_parameters.md`_

---

#### 11.1.2.3 Type anonymity

**What it is**

Closures succinctly capture variables from enclosing scopes. Does this have
any consequences? Observe how using a closure as a function
parameter requires [generics], which is necessary because of how they are
defined:
When a closure is defined, the compiler implicitly creates a new
anonymous structure to store the captured variables inside, meanwhile
implementing the functionality via one of the `traits`: `Fn`, `FnMut`, or
`FnOnce` for this unknown type. This type is assigned to the variable which
is stored until calling.

**JS mental model**

Each closure has a distinct unnameable type — you talk about them via traits (`impl Fn`).

**Rust example**

```rust
// `F` must implement `Fn` for a closure which takes no
// inputs and returns nothing - exactly what is required
// for `print`.
fn apply<F>(f: F) where
    F: Fn() {
    f();
}

fn main() {
    let x = 7;

    // Capture `x` into an anonymous type and implement
    // `Fn` for it. Store it in `print`.
    let print = || println!("{}", x);

    apply(print);
}
```

```rust
// `F` must be generic.
fn apply<F>(f: F) where
    F: FnOnce() {
    f();
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- You cannot write the concrete closure type name — use `impl Fn` or generics.

_RBE source: `fn/closures/anonymity.md`_

---

#### 11.1.2.4 Input functions

**What it is**

Since closures may be used as arguments, you might wonder if the same can be said
about functions. If you declare a function that takes a
closure as parameter, then any function that satisfies the trait bound of that
closure can be passed as a parameter. As an additional note, the `Fn`, `FnMut`, and `FnOnce` `traits` dictate how
a closure captures variables from the enclosing scope. See also:
[`Fn`][fn], [`FnMut`][fn_mut], and [`FnOnce`][fn_once]
[fn]: https://doc.rust-lang.org/std/ops/trait.Fn.html
[fn_mut]: https://doc.rust-lang.org/std/ops/trait.FnMut.html
[fn_once]: https://doc.rust-lang.org/std/ops/trait.FnOnce.html.

**JS mental model**

Function pointers `fn(i32)->i32` are like passing a bare function; closures that do not capture coerce to `fn`.

**Rust example**

```rust
// Define a function which takes a generic `F` argument
// bounded by `Fn`, and calls it
fn call_me<F: Fn()>(f: F) {
    f();
}

// Define a wrapper function satisfying the `Fn` bound
fn function() {
    println!("I'm a function!");
}

fn main() {
    // Define a closure satisfying the `Fn` bound
    let closure = || println!("I'm a closure!");

    call_me(closure);
    call_me(function);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Capturing closures are not `fn` pointers.

_RBE source: `fn/closures/input_functions.md`_

---

#### 11.1.2.5 As output parameters

**What it is**

Closures as input parameters are possible, so returning closures as
output parameters should also be possible. However, anonymous
closure types are, by definition, unknown, so we have to use
`impl Trait` to return them. The valid traits for returning a closure are:
* `Fn`
* `FnMut`
* `FnOnce`
Beyond this, the `move` keyword must be used, which signals that all captures
occur by value. This is required because any captures by reference would be
dropped as soon as the function exited, leaving invalid references in the
closure.

**JS mental model**

Returning closures requires `impl Fn` or `Box<dyn Fn>` because types are anonymous.

**Rust example**

```rust
fn create_fn() -> impl Fn() {
    let text = "Fn".to_owned();

    move || println!("This is a: {}", text)
}

fn create_fnmut() -> impl FnMut() {
    let text = "FnMut".to_owned();

    move || println!("This is a: {}", text)
}

fn create_fnonce() -> impl FnOnce() {
    let text = "FnOnce".to_owned();

    move || println!("This is a: {}", text)
}

fn main() {
    let fn_plain = create_fn();
    let mut fn_mut = create_fnmut();
    let fn_once = create_fnonce();

    fn_plain();
    fn_mut();
    fn_once();
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- `move` often needed so the closure owns what it returns with.

_RBE source: `fn/closures/output_parameters.md`_

---

#### 11.1.2.6 Examples in std

**What it is**

This section contains a few examples of using closures from the `std` library.

**JS mental model**

Std iterators take closures everywhere — like array methods `some`, `find`, `map`.

**Rust example**

```rust
fn main() {
    let v = vec![1, 2, 3];
    assert!(v.iter().any(|&x| x == 2));
    assert_eq!(v.iter().find(|&&x| x > 1), Some(&2));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Iterator adapters are lazy until consumed.

_RBE source: `fn/closures/closure_examples.md`_

---

##### 11.1.2.6.1 Iterator::any

**What it is**

`Iterator::any` is a function which when passed an iterator, will return
`true` if any element satisfies the predicate. Its
signature:
See also:
[`std::iter::Iterator::any`][any]
[any]: https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.any.

**JS mental model**

`.any(|x| …)` ≡ `array.some(…)`

**Rust example**

```rust
fn main() {
    let vec1 = vec![1, 2, 3];
    let vec2 = vec![4, 5, 6];

    // `iter()` for vecs yields `&i32`. Destructure to `i32`.
    println!("2 in vec1: {}", vec1.iter()     .any(|&x| x == 2));
    // `into_iter()` for vecs yields `i32`. No destructuring required.
    println!("2 in vec2: {}", vec2.into_iter().any(|x| x == 2));

    // `iter()` only borrows `vec1` and its elements, so they can be used again
    println!("vec1 len: {}", vec1.len());
    println!("First element of vec1 is: {}", vec1[0]);
    // `into_iter()` does move `vec2` and its elements, so they cannot be used again
    // println!("First element of vec2 is: {}", vec2[0]);
    // println!("vec2 len: {}", vec2.len());
    // TODO: uncomment two lines above and see compiler errors.

    let array1 = [1, 2, 3];
    let array2 = [4, 5, 6];

    // `iter()` for arrays yields `&i32`.
    println!("2 in array1: {}", array1.iter()     .any(|&x| x == 2));
    // `into_iter()` for arrays yields `i32`.
    println!("2 in array2: {}", array2.into_iter().any(|x| x == 2));
}
```

```rust
pub trait Iterator {
    // The type being iterated over.
    type Item;

    // `any` takes `&mut self` meaning the caller may be borrowed
    // and modified, but not consumed.
    fn any<F>(&mut self, f: F) -> bool where
        // `FnMut` meaning any captured variable may at most be
        // modified, not consumed. `Self::Item` is the closure parameter type,
        // which is determined by the iterator (e.g., `&T` for `.iter()`,
        // `T` for `.into_iter()`).
        F: FnMut(Self::Item) -> bool;
}
```

**JS equivalent**

```javascript
[2,3,4].some(x => x > 5); // false
```

**Watch out**

- Stops early when true found.

_RBE source: `fn/closures/closure_examples/iter_any.md`_

---

##### 11.1.2.6.2 Searching through iterators

**What it is**

`Iterator::find` is a function which iterates over an iterator and searches for the
first value which satisfies some condition. If none of the values satisfy the
condition, it returns `None`. Its signature:
`Iterator::find` gives you a reference to the item. But if you want the _index_ of the
item, use `Iterator::position`.

**JS mental model**

`.find(|x| …)` ≡ `array.find(…)` returning `Option`

**Rust example**

```rust
fn main() {
    let vec1 = vec![1, 2, 3];
    let vec2 = vec![4, 5, 6];

    // `vec1.iter()` yields `&i32`.
    let mut iter = vec1.iter();
    // `vec2.into_iter()` yields `i32`.
    let mut into_iter = vec2.into_iter();

    // `iter()` yields `&i32`, and `find` passes `&Item` to the predicate.
    // Since `Item = &i32`, the closure argument has type `&&i32`,
    // which we pattern-match to dereference down to `i32`.
    println!("Find 2 in vec1: {:?}", iter.find(|&&x| x == 2));
    
    // `into_iter()` yields `i32`, and `find` passes `&Item` to the predicate.
    // Since `Item = i32`, the closure argument has type `&i32`,
    // which we pattern-match to dereference down to `i32`.
    println!("Find 2 in vec2: {:?}", into_iter.find(|&x| x == 2));

    let array1 = [1, 2, 3];
    let array2 = [4, 5, 6];

    // `array1.iter()` yields `&i32`, and `find` passes `&Item` to the
    // predicate. Since `Item = &i32`, the closure argument has type `&&i32`.
    println!("Find 2 in array1: {:?}", array1.iter().find(|&&x| x == 2));
    // `array2.into_iter()` yields `i32` (since Rust 2021 edition), and
    // `find` passes `&Item` to the predicate. Since `Item = i32`, the
    // closure argument has type `&i32`.
    println!("Find 2 in array2: {:?}", array2.into_iter().find(|&x| x == 2));
}
```

```rust
fn main() {
    let vec = vec![1, 9, 3, 3, 13, 2];

    // `position` passes the iterator’s `Item` by value to the predicate.
    // `vec.iter()` yields `&i32`, so the predicate receives `&i32`,
    // which we pattern-match to dereference to `i32`.
    let index_of_first_even_number = vec.iter().position(|&x| x % 2 == 0);
    assert_eq!(index_of_first_even_number, Some(5));

    // `vec.into_iter()` yields `i32`, so the predicate receives `i32` directly.
    let index_of_first_negative_number = vec.into_iter().position(|x| x < 0);
    assert_eq!(index_of_first_negative_number, None);
}
```

**JS equivalent**

```javascript
[2,3,4].find(x => x > 2); // 3
```

**Watch out**

- Returns `Option<&T>` for `.iter()`.

_RBE source: `fn/closures/closure_examples/iter_find.md`_

---

### 11.1.3 Higher Order Functions

**What it is**

Rust provides Higher Order Functions (HOF). These are functions that
take one or more functions and/or produce a more useful function. HOFs
and lazy iterators give Rust its functional flavor. [Option][option]
and
[Iterator][iter]
implement their fair share of HOFs.

**JS mental model**

Higher-order functions: functions that take/return functions — same idea as JS HOFs.

**Rust example**

```rust
fn is_odd(n: u32) -> bool {
    n % 2 == 1
}

fn main() {
    println!("Find the sum of all the numbers with odd squares under 1000");
    let upper = 1000;

    // Imperative approach
    // Declare accumulator variable
    let mut acc = 0;
    // Iterate: 0, 1, 2, ... to infinity
    for n in 0.. {
        // Square the number
        let n_squared = n * n;

        if n_squared >= upper {
            // Break loop if exceeded the upper limit
            break;
        } else if is_odd(n_squared) {
            // Accumulate value, if it's odd
            acc += n;
        }
    }
    println!("imperative style: {}", acc);

    // Functional approach
    let sum: u32 =
        (0..).take_while(|&n| n * n < upper) // Below upper limit
             .filter(|&n| is_odd(n * n))     // That are odd
             .sum();                         // Sum them
    println!("functional style: {}", sum);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Combine with iterators for functional style without JS GC costs.

_RBE source: `fn/hof.md`_

---

### 11.1.4 Diverging functions

**What it is**

Diverging functions never return. They are marked using `!`, which is an empty type. As opposed to all the other types, this one cannot be instantiated, because the
set of all possible values this type can have is empty. Note that, it is
different from the `()` type, which has exactly one possible value.

**JS mental model**

`-> !` never returns (panic/infinite loop). Like a function that always throws.

**Rust example**

```rust
fn main() {
    fn sum_odd_numbers(up_to: u32) -> u32 {
        let mut acc = 0;
        for i in 0..up_to {
            // Notice that the return type of this match expression must be u32
            // because of the type of the "addition" variable.
            let addition: u32 = match i%2 == 1 {
                // The "i" variable is of type u32, which is perfectly fine.
                true => i,
                // On the other hand, the "continue" expression does not return
                // u32, but it is still fine, because it never returns and therefore
                // does not violate the type requirements of the match expression.
                false => continue,
            };
            acc += addition;
        }
        acc
    }
    println!("Sum of odd numbers up to 9 (excluding): {}", sum_odd_numbers(9));
}
```

```rust
#![feature(never_type)]

fn main() {
    let x: ! = panic!("This call never returns.");
    println!("You will never see this line!");
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- `!` can coerce to any type in type-checking (never-type).

_RBE source: `fn/diverging.md`_

---

## 12. Modules & visibility

_RBE chapter root maps here. Every nested leaf below has the full 5-part teaching block._

### 12.1 Modules

**What it is**

Rust provides a powerful module system that can be used to hierarchically split
code in logical units (modules), and manage visibility (public/private) between
them. A module is a collection of items: functions, structs, traits, `impl` blocks,
and even other modules.

**JS mental model**

Modules ≈ ES modules / folders, but with privacy (`pub`) enforced by the compiler.

**Rust example**

```rust
mod network {
    pub fn connect() {
        println!("connected");
    }
}

fn main() {
    network::connect();
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- `mod foo;` loads `foo.rs` or `foo/mod.rs`.
- Paths use `::`.

_RBE source: `mod.md`_

---

### 12.1.1 Visibility

**What it is**

By default, the items in a module have private visibility, but this can be
overridden with the `pub` modifier. Only the public items of a module can be
accessed from outside the module scope.

**JS mental model**

Default private like JS module scope; `pub` exports. Finer: `pub(crate)`, `pub(super)`.

**Rust example**

```rust
// A module named `my_mod`
mod my_mod {
    // Items in modules default to private visibility.
    fn private_function() {
        println!("called `my_mod::private_function()`");
    }

    // Use the `pub` modifier to override default visibility.
    pub fn function() {
        println!("called `my_mod::function()`");
    }

    // Items can access other items in the same module,
    // even when private.
    pub fn indirect_access() {
        print!("called `my_mod::indirect_access()`, that\n> ");
        private_function();
    }

    // Modules can also be nested
    pub mod nested {
        pub fn function() {
            println!("called `my_mod::nested::function()`");
        }

        #[allow(dead_code)]
        fn private_function() {
            println!("called `my_mod::nested::private_function()`");
        }

        // Functions declared using `pub(in path)` syntax are only visible
        // within the given path. `path` must be a parent or ancestor module
        pub(in crate::my_mod) fn public_function_in_my_mod() {
            print!("called `my_mod::nested::public_function_in_my_mod()`, that\n> ");
            public_function_in_nested();
        }

        // Functions declared using `pub(self)` syntax are only visible within
        // the current module, which is the same as leaving them private
        pub(self) fn public_function_in_nested() {
            println!("called `my_mod::nested::public_function_in_nested()`");
        }

        // Functions declared using `pub(super)` syntax are only visible within
        // the parent module
        pub(super) fn public_function_in_super_mod() {
            println!("called `my_mod::nested::public_function_in_super_mod()`");
        }
    }

    pub fn call_public_function_in_my_mod() {
        print!("called `my_mod::call_public_function_in_my_mod()`, that\n> ");
        nested::public_function_in_my_mod();
        print!("> ");
        nested::public_function_in_super_mod();
    }

    // pub(crate) makes functions visible only within the current crate
    pub(crate) fn public_function_in_crate() {
        println!("called `my_mod::public_function_in_crate()`");
    }

    // Nested modules follow the same rules for visibility
    mod private_nested {
        #[allow(dead_code)]
        pub fn function() {
            println!("called `my_mod::private_nested::function()`");
        }

        // Private parent items will still restrict the visibility of a child item,
        // even if it is declared as visible within a bigger scope.
        #[allow(dead_code)]
        pub(crate) fn restricted_function() {
            println!("called `my_mod::private_nested::restricted_function()`");
        }
    }
}

fn function() {
    println!("called `function()`");
}

fn main() {
    // Modules allow disambiguation between items that have the same name.
    function();
    my_mod::function();

    // Public items, including those inside nested modules, can be
    // accessed from outside the parent module.
    my_mod::indirect_access();
    my_mod::nested::function();
    my_mod::call_public_function_in_my_mod();

    // pub(crate) items can be called from anywhere in the same crate
    my_mod::public_function_in_crate();

    // pub(in path) items can only be called from within the module specified
    // Error! function `public_function_in_my_mod` is private
    //my_mod::nested::public_function_in_my_mod();
    // TODO ^ Try uncommenting this line

    // Private items of a module cannot be directly accessed, even if
    // nested in a public module:

    // Error! `private_function` is private
    //my_mod::private_function();
    // TODO ^ Try uncommenting this line

    // Error! `private_function` is private
    //my_mod::nested::private_function();
    // TODO ^ Try uncommenting this line

    // Error! `private_nested` is a private module
    //my_mod::private_nested::function();
    // TODO ^ Try uncommenting this line

    // Error! `private_nested` is a private module
    //my_mod::private_nested::restricted_function();
    // TODO ^ Try uncommenting this line
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Children see parent’s private items; siblings do not without `pub`.

_RBE source: `mod/visibility.md`_

---

### 12.1.2 Struct visibility

**What it is**

Structs have an extra level of visibility with their fields. The visibility
defaults to private, and can be overridden with the `pub` modifier. This
visibility only matters when a struct is accessed from outside the module
where it is defined, and has the goal of hiding information (encapsulation). See also:
[generics][generics] and [methods][methods]
[generics]: ../generics.md
[methods]: ../fn/methods.md.

**JS mental model**

Struct can be public while fields stay private — encapsulation like classes with private fields.

**Rust example**

```rust
mod my {
    // A public struct with a public field of generic type `T`
    pub struct OpenBox<T> {
        pub contents: T,
    }

    // A public struct with a private field of generic type `T`
    pub struct ClosedBox<T> {
        contents: T,
    }

    impl<T> ClosedBox<T> {
        // A public constructor method
        pub fn new(contents: T) -> ClosedBox<T> {
            ClosedBox {
                contents: contents,
            }
        }
    }
}

fn main() {
    // Public structs with public fields can be constructed as usual
    let open_box = my::OpenBox { contents: "public information" };

    // and their fields can be normally accessed.
    println!("The open box contains: {}", open_box.contents);

    // Public structs with private fields cannot be constructed using field names.
    // Error! `ClosedBox` has private fields
    //let closed_box = my::ClosedBox { contents: "classified information" };
    // TODO ^ Try uncommenting this line

    // However, structs with private fields can be created using
    // public constructors
    let _closed_box = my::ClosedBox::new("classified information");

    // and the private fields of a public struct cannot be accessed.
    // Error! The `contents` field is private
    //println!("The closed box contains: {}", _closed_box.contents);
    // TODO ^ Try uncommenting this line
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Public struct + private fields needs constructors/getters.

_RBE source: `mod/struct_visibility.md`_

---

### 12.1.3 The use declaration

**What it is**

The `use` declaration can be used to bind a full path to a new name, for easier
access. It is often used like this:
You can use the `as` keyword to bind imports to a different name:
You can also use `pub use` to re-export an item from a module, so it can be
accessed through the module's public interface:.

**JS mental model**

`use` is like named `import { x } from …` — brings paths into scope.

**Rust example**

```rust
// Bind the `deeply::nested::function` path to `other_function`.
use deeply::nested::function as other_function;

fn function() {
    println!("called `function()`");
}

mod deeply {
    pub mod nested {
        pub fn function() {
            println!("called `deeply::nested::function()`");
        }
    }
}

fn main() {
    // Easier access to `deeply::nested::function`
    other_function();

    println!("Entering block");
    {
        // This is equivalent to `use deeply::nested::function as function`.
        // This `function()` will shadow the outer one.
        use crate::deeply::nested::function;

        // `use` bindings have a local scope. In this case, the
        // shadowing of `function()` is only in this block.
        function();

        println!("Leaving block");
    }

    function();
}
```

```rust
mod deeply {
    pub mod nested {
        pub fn function() {
            println!("called `deeply::nested::function()`");
        }
    }
}

mod cool {
    pub use crate::deeply::nested::function;
}

fn main() {
    cool::function();
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- `use a::b as c` renames.
- Re-export with `pub use`.

_RBE source: `mod/use.md`_

---

### 12.1.4 super and self

**What it is**

The `super` and `self` keywords can be used in the path to remove ambiguity
when accessing items and to prevent unnecessary hardcoding of paths.

**JS mental model**

`super::` = parent module; `self::` = current. Like `../` and `./` in imports.

**Rust example**

```rust
fn function() {
    println!("called `function()`");
}

mod cool {
    pub fn function() {
        println!("called `cool::function()`");
    }
}

mod my {
    fn function() {
        println!("called `my::function()`");
    }

    mod cool {
        pub fn function() {
            println!("called `my::cool::function()`");
        }
    }

    pub fn indirect_call() {
        // Let's access all the functions named `function` from this scope!
        print!("called `my::indirect_call()`, that\n> ");

        // The `self` keyword refers to the current module scope - in this case `my`.
        // Calling `self::function()` and calling `function()` directly both give
        // the same result, because they refer to the same function.
        self::function();
        function();

        // We can also use `self` to access another module inside `my`:
        self::cool::function();

        // The `super` keyword refers to the parent scope (outside the `my` module).
        super::function();

        // This will bind to the `cool::function` in the *crate* scope.
        // In this case the crate scope is the outermost scope.
        {
            use crate::cool::function as root_function;
            root_function();
        }
    }
}

fn main() {
    my::indirect_call();
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- `crate::` starts from the crate root.

_RBE source: `mod/super.md`_

---

### 12.1.5 File hierarchy

**What it is**

Modules can be mapped to a file/directory hierarchy. Let's break down the
[visibility example][visibility] in files:
In `split.rs`:
In `my.rs`:
In `my/nested.rs`:
In `my/inaccessible.rs`:
Let's check that things still work as before:
[visibility]: visibility.md.

**JS mental model**

Split files across folders — like organizing a Node package into files.

**Rust example**

```rust
// Similarly `mod inaccessible` and `mod nested` will locate the
// `inaccessible.rs` and `nested.rs` files and insert them here under their
// respective modules
mod inaccessible;
pub mod nested;

pub fn function() {
    println!("called `my::function()`");
}

fn private_function() {
    println!("called `my::private_function()`");
}

pub fn indirect_access() {
    print!("called `my::indirect_access()`, that\n> ");

    private_function();
}
```

```rust
// This declaration will look for a file named `my.rs` and will
// insert its contents inside a module named `my` under this scope
mod my;

fn function() {
    println!("called `function()`");
}

fn main() {
    my::function();

    function();

    my::indirect_access();

    my::nested::function();
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Edition 2018+: `mod foo;` looks for `foo.rs` or `foo/mod.rs`.

_RBE source: `mod/split.md`_

---

## 13. Crates & Cargo

_RBE chapter root maps here. Every nested leaf below has the full 5-part teaching block._

### 13.1 Crates

**What it is**

A crate is a compilation unit in Rust. Whenever `rustc some_file.rs` is called,
`some_file.rs` is treated as the *crate file*. If `some_file.rs` has `mod`
declarations in it, then the contents of the module files would be inserted in
places where `mod` declarations in the crate file are found, *before* running
the compiler over it. In other words, modules do *not* get compiled
individually, only crates get compiled.

**JS mental model**

A crate is one compilation unit (library or binary). A Cargo package can have multiple targets. Crates.io ≈ npm.

**Rust example**

```rust
fn main() {
    println!("binary crate root has main");
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Binary crate has `main`; library crate exposes `pub` API.

_RBE source: `crates.md`_

---

### 13.1.1 Creating a Library

**What it is**

A library crate packages reusable functions/types. With rustc you can compile a file as `--crate-type=lib` to get a `lib*.rlib`. With Cargo, `cargo new --lib` sets up `src/lib.rs`. Only `pub` items are usable from other crates.

**JS mental model**

Creating a `.rlib` library is like publishing a reusable npm package (without the registry step yet).

**Rust example**

```rust
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

- `cargo new --lib mylib` is the modern path.
- Items must be `pub` to be usable outside.

_RBE source: `crates/lib.md`_

---

### 13.1.2 Using a Library

**What it is**

To use a library, the compiler must know where it is. With rustc you pass `--extern name=libname.rlib`. With Cargo you add the dependency in `Cargo.toml` and then `use name::item;` in code. Path dependencies are like local file: dependencies in npm.

**JS mental model**

Linking/using a library is like `npm install` + `import` — with Cargo you add a path/crates.io dependency and `use` it.

**Rust example**

```rust
// extern crate rary; // May be required for Rust 2015 edition or earlier

fn main() {
    rary::public_function();

    // Error! `private_function` is private
    //rary::private_function();

    rary::indirect_access();
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Edition 2018+ rarely needs `extern crate`.
- Name in Cargo.toml can differ from Rust identifier (use `-` vs `_`).

_RBE source: `crates/using_lib.md`_

---

### 13.1.3 Cargo

**What it is**

`cargo` is the official Rust package management tool. It has lots of really
useful features to improve code quality and developer velocity! These include
- Dependency management and integration with crates.io (the
  official Rust package registry)
- Awareness of unit tests
- Awareness of benchmarks
This chapter will go through some quick basics, but you can find the
comprehensive docs in The Cargo Book.

**JS mental model**

Cargo = npm + yarn-ish lockfile + scripts built-in (`build`, `test`, `run`).

**Rust example**

```toml
[package]
name = "my_project"
version = "0.1.0"
edition = "2021"

[dependencies]
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- `Cargo.lock` should be committed for binaries.
- Use `cargo add` to edit deps.

_RBE source: `cargo.md`_

---

### 13.1.3.1 Dependencies

**What it is**

Cargo downloads and builds dependencies listed under `[dependencies]` in `Cargo.toml`. You can depend on crates.io versions, git repos, or local paths. Running `cargo build` or `cargo run` resolves and fetches as needed — like npm install on demand.

**JS mental model**

`[dependencies]` ≡ `dependencies` in package.json. Versions use semver. Also path/git deps.

**Rust example**

```sh
A binary
cargo new foo

A library
cargo new --lib bar
```

```txt
.
├── bar
│   ├── Cargo.toml
│   └── src
│       └── lib.rs
└── foo
    ├── Cargo.toml
    └── src
        └── main.rs
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- `cargo add serde` is easiest.
- Features enable optional functionality.
- Distinguish `[dev-dependencies]` and `[build-dependencies]`.

_RBE source: `cargo/deps.md`_

---

### 13.1.3.2 Conventions

**What it is**

Cargo expects conventional paths: package root `Cargo.toml`, code in `src/`, integration tests in `tests/`, examples in `examples/`, benchmarks in `benches/`. Following conventions means less config.

**JS mental model**

Folder conventions: `src/`, `tests/`, `examples/`, `benches/` — like expecting `src/` and `__tests__/`.

**Rust example**

```txt
foo
├── Cargo.toml
└── src
    └── main.rs
```

```txt
foo
├── Cargo.toml
└── src
    ├── main.rs
    └── bin
        └── my_other_bin.rs
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- `src/main.rs` binary; `src/lib.rs` library.
- Integration tests go in `tests/*.rs`.

_RBE source: `cargo/conventions.md`_

---

### 13.1.3.3 Tests

**What it is**

Cargo runs unit, integration, and doc tests with `cargo test`. It builds a test harness for you. Filter tests by name; use `#[ignore]` for expensive tests.

**JS mental model**

`cargo test` ≈ `npm test`, but discovers `#[test]` functions automatically.

**Rust example**

```rust
#[cfg(test)]
mod tests {
    // Import the necessary modules
    use std::fs::OpenOptions;
    use std::io::Write;

    // This test writes to a file
    #[test]
    fn test_file() {
        // Opens the file ferris.txt or creates one if it doesn't exist.
        let mut file = OpenOptions::new()
            .append(true)
            .create(true)
            .open("ferris.txt")
            .expect("Failed to open ferris.txt");

        // Print "Ferris" 5 times.
        for _ in 0..5 {
            file.write_all("Ferris\n".as_bytes())
                .expect("Could not write to ferris.txt");
        }
    }

    // This test tries to write to the same file
    #[test]
    fn test_file_also() {
        // Opens the file ferris.txt or creates one if it doesn't exist.
        let mut file = OpenOptions::new()
            .append(true)
            .create(true)
            .open("ferris.txt")
            .expect("Failed to open ferris.txt");

        // Print "Corro" 5 times.
        for _ in 0..5 {
            file.write_all("Corro\n".as_bytes())
                .expect("Could not write to ferris.txt");
        }
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Unit tests often live in the same file under `#[cfg(test)] mod tests`.
- Run one test with `cargo test name`.

_RBE source: `cargo/test.md`_

---

### 13.1.3.4 Build Scripts

**What it is**

A build script is a Rust file named `build.rs` at the package root. Cargo compiles and runs it before building your crate. Use it to generate code, find system libraries, or set cfg flags. Communication back to Cargo is via special `println!("cargo:…")` lines.

**JS mental model**

`build.rs` runs before compile — like a controlled prebuild script / node-gyp step for codegen and linking.

**Rust example**

```toml
[package]
...
build = "build.rs"
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Print `cargo:rerun-if-changed=…` instructions.
- Do not do network in build.rs without good reason.
- Use `[build-dependencies]` for crates used only by build.rs.

_RBE source: `cargo/build_scripts.md`_

---

## 14. Attributes

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

## 15. Generics

_RBE chapter root maps here. Every nested leaf below has the full 5-part teaching block._

### 15.1 Generics

**What it is**

*Generics* is the topic of generalizing types and functionalities to broader
cases. This is extremely useful for reducing code duplication in many ways,
but can call for rather involved syntax. Namely, being generic requires
taking great care to specify over which types a generic type
is actually considered valid. The simplest and most common use of generics
is for type parameters.

**JS mental model**

Generics are like TypeScript generics <T>, but monomorphized (compiled per type) for speed — no runtime generic objects.

**Rust example**

```rust
// A concrete type `A`.
struct A;

// In defining the type `Single`, the first use of `A` is not preceded by `<A>`.
// Therefore, `Single` is a concrete type, and `A` is defined as above.
struct Single(A);
//            ^ Here is `Single`s first use of the type `A`.

// Here, `<T>` precedes the first use of `T`, so `SingleGen` is a generic type.
// Because the type parameter `T` is generic, it could be anything, including
// the concrete type `A` defined at the top.
struct SingleGen<T>(T);

fn main() {
    // `Single` is concrete and explicitly takes `A`.
    let _s = Single(A);

    // Create a variable `_char` of type `SingleGen<char>`
    // and give it the value `SingleGen('a')`.
    // Here, `SingleGen` has a type parameter explicitly specified.
    let _char: SingleGen<char> = SingleGen('a');

    // `SingleGen` can also have a type parameter implicitly specified:
    let _t    = SingleGen(A); // Uses `A` defined at the top.
    let _i32  = SingleGen(6); // Uses `i32`.
    let _char = SingleGen('a'); // Uses `char`.
}
```

```rust
fn foo<T>(arg: T) { ... }
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Turbofish ::<> helps when inference fails.
- Bounds say what traits T must implement.

_RBE source: `generics.md`_

---

### 15.1.1 Functions

**What it is**

The same set of rules can be applied to functions: a type `T` becomes
generic when preceded by `<T>`. Using generic functions sometimes requires explicitly specifying type
parameters. This may be the case if the function is called where the return type
is generic, or if the compiler doesn't have enough information to infer
the necessary type parameters. A function call with explicitly specified type parameters looks like:
`fun::<A, B, ...>()`.

**JS mental model**

Generic functions: fn foo<T>(x: T) like function foo<T>(x: T).

**Rust example**

```rust
struct A;          // Concrete type `A`.
struct S(A);       // Concrete type `S`.
struct SGen<T>(T); // Generic type `SGen`.

// The following functions all take ownership of the variable passed into
// them and immediately go out of scope, freeing the variable.

// Define a function `reg_fn` that takes an argument `_s` of type `S`.
// This has no `<T>` so this is not a generic function.
fn reg_fn(_s: S) {}

// Define a function `gen_spec_t` that takes an argument `_s` of type `SGen<T>`.
// It has been explicitly given the type parameter `A`, but because `A` has not
// been specified as a generic type parameter for `gen_spec_t`, it is not generic.
fn gen_spec_t(_s: SGen<A>) {}

// Define a function `gen_spec_i32` that takes an argument `_s` of type `SGen<i32>`.
// It has been explicitly given the type parameter `i32`, which is a specific type.
// Because `i32` is not a generic type, this function is also not generic.
fn gen_spec_i32(_s: SGen<i32>) {}

// Define a function `generic` that takes an argument `_s` of type `SGen<T>`.
// Because `SGen<T>` is preceded by `<T>`, this function is generic over `T`.
fn generic<T>(_s: SGen<T>) {}

fn main() {
    // Using the non-generic functions
    reg_fn(S(A));          // Concrete type.
    gen_spec_t(SGen(A));   // Implicitly specified type parameter `A`.
    gen_spec_i32(SGen(6)); // Implicitly specified type parameter `i32`.

    // Explicitly specified type parameter `char` to `generic()`.
    generic::<char>(SGen('a'));

    // Implicitly specified type parameter `char` to `generic()`.
    generic(SGen('c'));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Caller usually does not write <T> — inference does.

_RBE source: `generics/gen_fn.md`_

---

### 15.1.2 Implementation

**What it is**

Similar to functions, implementations require care to remain generic. See also:
[functions returning references][fn], [`impl`][methods], and [`struct`][structs]
[fn]: ../scope/lifetime/fn.md
[methods]: ../fn/methods.md
[specialization_plans]: https://blog.rust-lang.org/2015/05/11/traits.html#the-future
[structs]: ../custom_types/structs.md.

**JS mental model**

impl<T> Type<T> { ... } — methods for every T, like generic class methods.

**Rust example**

```rust
struct Val {
    val: f64,
}

struct GenVal<T> {
    gen_val: T,
}

// impl of Val
impl Val {
    fn value(&self) -> &f64 {
        &self.val
    }
}

// impl of GenVal for a generic type `T`
impl<T> GenVal<T> {
    fn value(&self) -> &T {
        &self.gen_val
    }
}

fn main() {
    let x = Val { val: 3.0 };
    let y = GenVal { gen_val: 3i32 };

    println!("{}, {}", x.value(), y.value());
}
```

```rust
struct S; // Concrete type `S`
struct GenericVal<T>(T); // Generic type `GenericVal`

// impl of GenericVal where we explicitly specify type parameters:
impl GenericVal<f32> {} // Specify `f32`
impl GenericVal<S> {} // Specify `S` as defined above

// `<T>` Must precede the type to remain generic
impl<T> GenericVal<T> {}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- You can also impl Type<Specific>.
- Separate impl blocks are fine.

_RBE source: `generics/impl.md`_

---

### 15.1.3 Traits

**What it is**

Of course `trait`s can also be generic. Here we define one which reimplements
the `Drop` `trait` as a generic method to `drop` itself and an input. See also:
[`Drop`][Drop], [`struct`][structs], and [`trait`][traits]
[Drop]: https://doc.rust-lang.org/std/ops/trait.Drop.html
[structs]: ../custom_types/structs.md
[traits]: ../trait.md.

**JS mental model**

Traits can be generic too — like generic interfaces.

**Rust example**

```rust
// Non-copyable types.
struct Empty;
struct Null;

// A trait generic over `T`.
trait DoubleDrop<T> {
    // Define a method on the caller type which takes an
    // additional single parameter `T` and does nothing with it.
    fn double_drop(self, _: T);
}

// Implement `DoubleDrop<T>` for any generic parameter `T` and
// caller `U`.
impl<T, U> DoubleDrop<T> for U {
    // This method takes ownership of both passed arguments,
    // deallocating both.
    fn double_drop(self, _: T) {}
}

fn main() {
    let empty = Empty;
    let null  = Null;

    // Deallocate `empty` and `null`.
    empty.double_drop(null);

    //empty;
    //null;
    // ^ TODO: Try uncommenting these lines.
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Associated types are often clearer than extra type params (see later).

_RBE source: `generics/gen_trait.md`_

---

### 15.1.4 Bounds

**What it is**

When working with generics, the type parameters often must use traits as *bounds* to
stipulate what functionality a type implements. For example, the following
example uses the trait `Display` to print and so it requires `T` to be bound
by `Display`; that is, `T` *must* implement `Display`. Bounding restricts the generic to types that conform to the bounds. That is:
Another effect of bounding is that generic instances are allowed to access the
[methods] of traits specified in the bounds.

**JS mental model**

T: Display means T must implement Display — like T extends Foo in TS.

**Rust example**

```rust
// A trait which implements the print marker: `{:?}`.
use std::fmt::Debug;

trait HasArea {
    fn area(&self) -> f64;
}

impl HasArea for Rectangle {
    fn area(&self) -> f64 { self.length * self.height }
}

#[derive(Debug)]
struct Rectangle { length: f64, height: f64 }
#[allow(dead_code)]
struct Triangle  { length: f64, height: f64 }

// The generic `T` must implement `Debug`. Regardless
// of the type, this will work properly.
fn print_debug<T: Debug>(t: &T) {
    println!("{:?}", t);
}

// `T` must implement `HasArea`. Any type which meets
// the bound can access `HasArea`'s function `area`.
fn area<T: HasArea>(t: &T) -> f64 { t.area() }

fn main() {
    let rectangle = Rectangle { length: 3.0, height: 4.0 };
    let _triangle = Triangle  { length: 3.0, height: 4.0 };

    print_debug(&rectangle);
    println!("Area: {}", area(&rectangle));

    //print_debug(&_triangle);
    //println!("Area: {}", area(&_triangle));
    // ^ TODO: Try uncommenting these.
    // | Error: Does not implement either `Debug` or `HasArea`.
}
```

```rust
// Define a function `printer` that takes a generic type `T` which
// must implement trait `Display`.
fn printer<T: Display>(t: T) {
    println!("{}", t);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Without bounds you can only do what all types allow (move, drop).
- where clauses help readability for complex bounds.

_RBE source: `generics/bounds.md`_

---

#### 15.1.4.1 Testcase: empty bounds

**What it is**

A consequence of how bounds work is that even if a `trait` doesn't
include any functionality, you can still use it as a bound. `Eq` and
`Copy` are examples of such `trait`s from the `std` library. See also:
[`std::cmp::Eq`][eq], [`std::marker::Copy`][copy], and [`trait`s][traits]
[eq]: https://doc.rust-lang.org/std/cmp/trait.Eq.html
[copy]: https://doc.rust-lang.org/std/marker/trait.Copy.html
[traits]: ../../trait.md.

**JS mental model**

Even an empty trait can be used as a bound to mark capabilities — a type-level tag.

**Rust example**

```rust
struct Cardinal;
struct BlueJay;
struct Turkey;

trait Red {}
trait Blue {}

impl Red for Cardinal {}
impl Blue for BlueJay {}

// These functions are only valid for types which implement these
// traits. The fact that the traits are empty is irrelevant.
fn red<T: Red>(_: &T)   -> &'static str { "red" }
fn blue<T: Blue>(_: &T) -> &'static str { "blue" }

fn main() {
    let cardinal = Cardinal;
    let blue_jay = BlueJay;
    let _turkey   = Turkey;

    // `red()` won't work on a blue jay nor vice versa
    // because of the bounds.
    println!("A cardinal is {}", red(&cardinal));
    println!("A blue jay is {}", blue(&blue_jay));
    //println!("A turkey is {}", red(&_turkey));
    // ^ TODO: Try uncommenting this line.
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Useful for grouping types intentionally.

_RBE source: `generics/bounds/testcase_empty.md`_

---

### 15.1.5 Multiple bounds

**What it is**

Multiple bounds for a single type can be applied with a `+`. Like normal, different types are
separated with `,`. See also:
[`std::fmt`][fmt] and [`trait`s][traits]
[fmt]: ../hello/print.md
[traits]: ../trait.md.

**JS mental model**

T: A + B means both traits — like intersection constraints.

**Rust example**

```rust
use std::fmt::{Debug, Display};

fn compare_prints<T: Debug + Display>(t: &T) {
    println!("Debug: `{:?}`", t);
    println!("Display: `{}`", t);
}

fn compare_types<T: Debug, U: Debug>(t: &T, u: &U) {
    println!("t: `{:?}`", t);
    println!("u: `{:?}`", u);
}

fn main() {
    let string = "words";
    let array = [1, 2, 3];
    let vec = vec![1, 2, 3];

    compare_prints(&string);
    //compare_prints(&array);
    // TODO ^ Try uncommenting this.

    compare_types(&array, &vec);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Order of bounds does not matter.

_RBE source: `generics/multi_bounds.md`_

---

### 15.1.6 Where clauses

**What it is**

A bound can also be expressed using a `where` clause immediately
before the opening `{`, rather than at the type's first mention. Additionally, `where` clauses can apply bounds to arbitrary types,
rather than just to type parameters. Some cases that a `where` clause is useful:
* When specifying generic types and bounds separately is clearer:
* When using a `where` clause is more expressive than using normal syntax. The `impl` in this example cannot be directly expressed without a `where` clause:
See also:
[RFC][where], [`struct`][struct], and [`trait`][trait]
[struct]: ../custom_types/structs.md
[trait]: ../trait.md
[where]: https://github.com/rust-lang/rfcs/blob/master/text/0135-where.md.

**JS mental model**

where moves complex bounds out of <...> for readability.

**Rust example**

```rust
use std::fmt::Debug;

trait PrintInOption {
    fn print_in_option(self);
}

// Because we would otherwise have to express this as `T: Debug` or
// use another method of indirect approach, this requires a `where` clause:
impl<T> PrintInOption for T where
    Option<T>: Debug {
    // We want `Option<T>: Debug` as our bound because that is what's
    // being printed. Doing otherwise would be using the wrong bound.
    fn print_in_option(self) {
        println!("{:?}", Some(self));
    }
}

fn main() {
    let vec = vec![1, 2, 3];

    vec.print_in_option();
}
```

```rust
impl <A: TraitB + TraitC, D: TraitE + TraitF> MyTrait<A, D> for YourType {}

// Expressing bounds with a `where` clause
impl <A, D> MyTrait<A, D> for YourType where
    A: TraitB + TraitC,
    D: TraitE + TraitF {}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Same meaning as inline bounds.

_RBE source: `generics/where.md`_

---

### 15.1.7 New Type Idiom

**What it is**

The `newtype` idiom gives compile time guarantees that the right type of value is supplied
to a program. For example, a function that measures distance in miles, *must* be given
a value of type `Miles`. Uncomment the last print statement to observe that the type supplied must be `Miles`. To obtain the `newtype`'s value as the base type, you may use the tuple or destructuring syntax like so:
See also:
[`structs`][struct]
[struct]: ../custom_types/structs.md.

**JS mental model**

Wrap a type in a tuple struct struct Inches(i32) for type safety — like branded types in TS.

**Rust example**

```rust
struct Miles(f64);

struct Kilometers(f64);

impl Miles {
    pub fn to_kilometers(&self) -> Kilometers {
        Kilometers(self.0 * 1.609344)
    }
}

impl Kilometers {
    pub fn to_miles(&self) -> Miles {
        Miles(self.0 / 1.609344)
    }
}

fn is_a_marathon(distance: &Miles) -> bool {
    distance.0 >= 26.2
}

fn main() {
    let distance = Miles(30.0);
    let distance_km = distance.to_kilometers();
    println!("Is a marathon? {}", is_a_marathon(&distance));
    println!("Is a marathon? {}", is_a_marathon(&distance_km.to_miles()));
    // println!("Is a marathon? {}", is_a_marathon(&distance_km));
}
```

```rust
struct Miles(f64);

fn main() {
    let distance = Miles(42.0);
    let distance_as_primitive_1: f64 = distance.0; // Tuple
    let Miles(distance_as_primitive_2) = distance; // Destructuring
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Newtypes are free at runtime (one field).
- Does not automatically inherit methods — deref or write wrappers.

_RBE source: `generics/new_types.md`_

---

### 15.1.8 Associated items

**What it is**

"Associated Items" refers to a set of rules pertaining to [`item`][items]s
of various types. It is an extension to `trait` generics, and allows
`trait`s to internally define new items. One such item is called an *associated type*, providing simpler usage
patterns when the `trait` is generic over its container type. See also:
[RFC][RFC]
[items]: https://doc.rust-lang.org/reference/items.html
[RFC]: https://github.com/rust-lang/rfcs/blob/master/text/0195-associated-items.md.

**JS mental model**

Associated items are constants/types/functions tied to a trait — like static members of an interface.

**Rust example**

```rust
trait Container {
    type Item;
    fn get(&self) -> Option<&Self::Item>;
}

fn main() {}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Associated types name an output type chosen by the implementor.

_RBE source: `generics/assoc_items.md`_

---

#### 15.1.8.1 The Problem

**What it is**

A `trait` that is generic over its container type has type specification
requirements - users of the `trait` *must* specify all of its generic types. In the example below, the `Contains` `trait` allows the use of the generic
types `A` and `B`. The trait is then implemented for the `Container` type,
specifying `i32` for `A` and `B` so that it can be used with `fn difference()`. Because `Contains` is generic, we are forced to explicitly state *all* of the
generic types for `fn difference()`.

**JS mental model**

Without associated types, container traits need awkward type parameters on the trait itself.

**Rust example**

```rust
struct Container(i32, i32);

// A trait which checks if 2 items are stored inside of container.
// Also retrieves first or last value.
trait Contains<A, B> {
    fn contains(&self, _: &A, _: &B) -> bool; // Explicitly requires `A` and `B`.
    fn first(&self) -> i32; // Doesn't explicitly require `A` or `B`.
    fn last(&self) -> i32;  // Doesn't explicitly require `A` or `B`.
}

impl Contains<i32, i32> for Container {
    // True if the numbers stored are equal.
    fn contains(&self, number_1: &i32, number_2: &i32) -> bool {
        (&self.0 == number_1) && (&self.1 == number_2)
    }

    // Grab the first number.
    fn first(&self) -> i32 { self.0 }

    // Grab the last number.
    fn last(&self) -> i32 { self.1 }
}

// `C` contains `A` and `B`. In light of that, having to express `A` and
// `B` again is a nuisance.
fn difference<A, B, C>(container: &C) -> i32 where
    C: Contains<A, B> {
    container.last() - container.first()
}

fn main() {
    let number_1 = 3;
    let number_2 = 10;

    let container = Container(number_1, number_2);

    println!("Does container contain {} and {}: {}",
        &number_1, &number_2,
        container.contains(&number_1, &number_2));
    println!("First number: {}", container.first());
    println!("Last number: {}", container.last());

    println!("The difference is: {}", difference(&container));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- This motivates associated types in the next page.

_RBE source: `generics/assoc_items/the_problem.md`_

---

#### 15.1.8.2 Associated types

**What it is**

The use of "Associated types" improves the overall readability of code
by moving inner types locally into a trait as *output* types. Syntax
for the `trait` definition is as follows:
Note that functions that use the `trait` `Contains` are no longer required
to express `A` or `B` at all:
Let's rewrite the example from the previous section using associated types:.

**JS mental model**

type Item; inside a trait — implementor picks the concrete type. Like interface with a nested type slot.

**Rust example**

```rust
struct Container(i32, i32);

// A trait which checks if 2 items are stored inside of container.
// Also retrieves first or last value.
trait Contains {
    // Define generic types here which methods will be able to utilize.
    type A;
    type B;

    fn contains(&self, _: &Self::A, _: &Self::B) -> bool;
    fn first(&self) -> i32;
    fn last(&self) -> i32;
}

impl Contains for Container {
    // Specify what types `A` and `B` are. If the `input` type
    // is `Container(i32, i32)`, the `output` types are determined
    // as `i32` and `i32`.
    type A = i32;
    type B = i32;

    // `&Self::A` and `&Self::B` are also valid here.
    fn contains(&self, number_1: &i32, number_2: &i32) -> bool {
        (&self.0 == number_1) && (&self.1 == number_2)
    }
    // Grab the first number.
    fn first(&self) -> i32 { self.0 }

    // Grab the last number.
    fn last(&self) -> i32 { self.1 }
}

fn difference<C: Contains>(container: &C) -> i32 {
    container.last() - container.first()
}

fn main() {
    let number_1 = 3;
    let number_2 = 10;

    let container = Container(number_1, number_2);

    println!("Does container contain {} and {}: {}",
        &number_1, &number_2,
        container.contains(&number_1, &number_2));
    println!("First number: {}", container.first());
    println!("Last number: {}", container.last());

    println!("The difference is: {}", difference(&container));
}
```

```rust
// `A` and `B` are defined in the trait via the `type` keyword.
// (Note: `type` in this context is different from `type` when used for
// aliases).
trait Contains {
    type A;
    type B;

    // Updated syntax to refer to these new types generically.
    fn contains(&self, _: &Self::A, _: &Self::B) -> bool;
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Iterator::Item is the classic example.

_RBE source: `generics/assoc_items/types.md`_

---

### 15.1.9 Phantom type parameters

**What it is**

A phantom type parameter is one that doesn't show up at runtime,
but is checked statically (and only) at compile time. Data types can use extra generic type parameters to act as markers
or to perform type checking at compile time. These extra parameters
hold no storage values, and have no runtime behavior. In the following example, we combine [std::marker::PhantomData]
with the phantom type parameter concept to create tuples containing
different data types.

**JS mental model**

PhantomData<T> marks that a type uses T without storing T — advanced type-level trick.

**Rust example**

```rust
use std::marker::PhantomData;

// A phantom tuple struct which is generic over `A` with hidden parameter `B`.
#[derive(PartialEq)] // Allow equality test for this type.
struct PhantomTuple<A, B>(A, PhantomData<B>);

// A phantom type struct which is generic over `A` with hidden parameter `B`.
#[derive(PartialEq)] // Allow equality test for this type.
struct PhantomStruct<A, B> { first: A, phantom: PhantomData<B> }

// Note: Storage is allocated for generic type `A`, but not for `B`.
//       Therefore, `B` cannot be used in computations.

fn main() {
    // Here, `f32` and `f64` are the hidden parameters.
    // PhantomTuple type specified as `<char, f32>`.
    let _tuple1: PhantomTuple<char, f32> = PhantomTuple('Q', PhantomData);
    // PhantomTuple type specified as `<char, f64>`.
    let _tuple2: PhantomTuple<char, f64> = PhantomTuple('Q', PhantomData);

    // Type specified as `<char, f32>`.
    let _struct1: PhantomStruct<char, f32> = PhantomStruct {
        first: 'Q',
        phantom: PhantomData,
    };
    // Type specified as `<char, f64>`.
    let _struct2: PhantomStruct<char, f64> = PhantomStruct {
        first: 'Q',
        phantom: PhantomData,
    };

    // Compile-time Error! Type mismatch so these cannot be compared:
    // println!("_tuple1 == _tuple2 yields: {}",
    //           _tuple1 == _tuple2);

    // Compile-time Error! Type mismatch so these cannot be compared:
    // println!("_struct1 == _struct2 yields: {}",
    //           _struct1 == _struct2);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Needed for drop check / variance in some APIs.
- Rare in everyday app code.

_RBE source: `generics/phantom.md`_

---

#### 15.1.9.1 Testcase: unit clarification

**What it is**

A useful method of unit conversions can be examined by implementing `Add`
with a phantom type parameter. The `Add` `trait` is examined below:
The whole implementation:
See also:
[Borrowing (`&`)], [Bounds (`X: Y`)], [enum], [impl & self],
[Overloading], [ref], [Traits (`X for Y`)], and [TupleStructs]. [Borrowing (`&`)]: ../../scope/borrow.md
[Bounds (`X: Y`)]: ../../generics/bounds.md
[enum]: ../../custom_types/enum.md
[impl & self]: ../../fn/methods.md
[Overloading]: ../../trait/ops.md
[ref]: ../../scope/borrow/ref.md
[Traits (`X for Y`)]: ../../trait.md
[TupleStructs]: ../../custom_types/structs.md
[std::marker::PhantomData]: https://doc.rust-lang.org/std/marker/struct.PhantomData.html.

**JS mental model**

Phantom types can encode units (mm vs inch) at compile time — like branded number types that cannot mix.

**Rust example**

```rust
use std::ops::Add;
use std::marker::PhantomData;

/// Create void enumerations to define unit types.
#[derive(Debug, Clone, Copy)]
enum Inch {}
#[derive(Debug, Clone, Copy)]
enum Mm {}

/// `Length` is a type with phantom type parameter `Unit`,
/// and is not generic over the length type (that is `f64`).
///
/// `f64` already implements the `Clone` and `Copy` traits.
#[derive(Debug, Clone, Copy)]
struct Length<Unit>(f64, PhantomData<Unit>);

/// The `Add` trait defines the behavior of the `+` operator.
impl<Unit> Add for Length<Unit> {
    type Output = Length<Unit>;

    // add() returns a new `Length` struct containing the sum.
    fn add(self, rhs: Length<Unit>) -> Length<Unit> {
        // `+` calls the `Add` implementation for `f64`.
        Length(self.0 + rhs.0, PhantomData)
    }
}

fn main() {
    // Specifies `one_foot` to have phantom type parameter `Inch`.
    let one_foot:  Length<Inch> = Length(12.0, PhantomData);
    // `one_meter` has phantom type parameter `Mm`.
    let one_meter: Length<Mm>   = Length(1000.0, PhantomData);

    // `+` calls the `add()` method we implemented for `Length<Unit>`.
    //
    // Since `Length` implements `Copy`, `add()` does not consume
    // `one_foot` and `one_meter` but copies them into `self` and `rhs`.
    let two_feet = one_foot + one_foot;
    let two_meters = one_meter + one_meter;

    // Addition works.
    println!("one foot + one_foot = {:?} in", two_feet.0);
    println!("one meter + one_meter = {:?} mm", two_meters.0);

    // Nonsensical operations fail as they should:
    // Compile-time Error: type mismatch.
    //let one_feter = one_foot + one_meter;
}
```

```rust
// This construction would impose: `Self + RHS = Output`
// where RHS defaults to Self if not specified in the implementation.
pub trait Add<RHS = Self> {
    type Output;

    fn add(self, rhs: RHS) -> Self::Output;
}

// `Output` must be `T<U>` so that `T<U> + T<U> = T<U>`.
impl<U> Add for T<U> {
    type Output = T<U>;
    ...
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Prevents mixing incompatible units without runtime cost.

_RBE source: `generics/phantom/testcase_units.md`_

---

## 16. Scoping rules: ownership, borrowing, lifetimes

_RBE chapter root maps here. Every nested leaf below has the full 5-part teaching block._

### 16.1 Scoping rules

**What it is**

Scoping rules decide when values are created and destroyed. Rust uses ownership instead of a garbage collector. When the owner goes out of scope, the value is dropped.

**JS mental model**

This chapter is the big difference from JS: ownership, borrowing, lifetimes. No GC — rules at compile time.

**Rust example**

```rust
fn main() {
    let s = String::from("own");
    let t = s; // move
    // println!("{s}"); // error after move
    println!("{t}");
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Re-read this chapter often.
- Compiler errors here are normal while learning.

_RBE source: `scope.md`_

---

### 16.1.1 RAII

**What it is**

Variables in Rust do more than just hold data in the stack: they also *own*
resources, e.g. `Box<T>` owns memory in the heap. Rust enforces [RAII][raii]
(Resource Acquisition Is Initialization), so whenever an object goes out of
scope, its destructor is called and its owned resources are freed. This behavior shields against *resource leak* bugs, so you'll never have to
manually free memory or worry about memory leaks again!

**JS mental model**

RAII: acquire resource when a value is created, release when it drops — like using/try-finally automatic via scope.

**Rust example**

```rust
// raii.rs
fn create_box() {
    // Allocate an integer on the heap
    let _box1 = Box::new(3i32);

    // `_box1` is destroyed here, and memory gets freed
}

fn main() {
    // Allocate an integer on the heap
    let _box2 = Box::new(5i32);

    // A nested scope:
    {
        // Allocate an integer on the heap
        let _box3 = Box::new(4i32);

        // `_box3` is destroyed here, and memory gets freed
    }

    // Creating lots of boxes just for fun
    // There's no need to manually free memory!
    for _ in 0u32..1_000 {
        create_box();
    }

    // `_box2` is destroyed here, and memory gets freed
}
```

```rust
struct ToDrop;

impl Drop for ToDrop {
    fn drop(&mut self) {
        println!("ToDrop is being dropped");
    }
}

fn main() {
    let x = ToDrop;
    println!("Made a ToDrop!");
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Drop runs deterministically at end of scope.
- Files, locks, memory all use this pattern.

_RBE source: `scope/raii.md`_

---

### 16.1.2 Ownership and moves

**What it is**

Because variables are in charge of freeing their own resources,
**resources can only have one owner**. This prevents resources
from being freed more than once. Note that not all variables own
resources (e.g. When doing assignments (`let x = y`) or passing function arguments by value
(`foo(x)`), the *ownership* of the resources is transferred.

**JS mental model**

Assignment/passing often moves ownership (invalidates the old name) — not a shared reference like JS objects.

**Rust example**

```rust
// This function takes ownership of the heap allocated memory
fn destroy_box(c: Box<i32>) {
    println!("Destroying a box that contains {}", c);

    // `c` is destroyed and the memory freed
}

fn main() {
    // _Stack_ allocated integer
    let x = 5u32;

    // *Copy* `x` into `y` - no resources are moved
    let y = x;

    // Both values can be independently used
    println!("x is {}, and y is {}", x, y);

    // `a` is a pointer to a _heap_ allocated integer
    let a = Box::new(5i32);

    println!("a contains: {}", a);

    // *Move* `a` into `b`
    let b = a;
    // The pointer address of `a` is copied (not the data) into `b`.
    // Both are now pointers to the same heap allocated data, but
    // `b` now owns it.

    // Error! `a` can no longer access the data, because it no longer owns the
    // heap memory
    //println!("a contains: {}", a);
    // TODO ^ Try uncommenting this line

    // This function takes ownership of the heap allocated memory from `b`
    destroy_box(b);

    // Since the heap memory has been freed at this point, this action would
    // result in dereferencing freed memory, but it's forbidden by the compiler
    // Error! Same reason as the previous Error
    //println!("b contains: {}", b);
    // TODO ^ Try uncommenting this line
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- After move, using the old name is a compile error.
- Copy types (numbers) are duplicated instead.

_RBE source: `scope/move.md`_

---

#### 16.1.2.1 Mutability

**What it is**

Mutability of data can be changed when ownership is transferred.

**JS mental model**

You can mutate after taking ownership if the binding is mut.

**Rust example**

```rust
fn main() {
    let immutable_box = Box::new(5u32);

    println!("immutable_box contains {}", immutable_box);

    // Mutability error
    //*immutable_box = 4;

    // *Move* the box, changing the ownership (and mutability)
    let mut mutable_box = immutable_box;

    println!("mutable_box contains {}", mutable_box);

    // Modify the contents of the box
    *mutable_box = 4;

    println!("mutable_box now contains {}", mutable_box);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Ownership transfer can change mutability at the new binding.

_RBE source: `scope/move/mut.md`_

---

#### 16.1.2.2 Partial moves

**What it is**

Within the [destructuring] of a single variable, both `by-move` and
`by-reference` pattern bindings can be used at the same time. Doing
this will result in a _partial move_ of the variable, which means
that parts of the variable will be moved while other parts stay. In
such a case, the parent variable cannot be used afterwards as a
whole, however the parts that are only referenced (and not moved)
can still be used. Note that types that implement the
[`Drop` trait][droptrait] cannot be partially moved from, because
its `drop` method would use it afterwards as a whole.

**JS mental model**

Moving one field out of a struct can partially invalidate the rest — careful.

**Rust example**

```rust
fn main() {
    #[derive(Debug)]
    struct Person {
        name: String,
        age: Box<u8>,
    }

    // Error! cannot move out of a type which implements the `Drop` trait
    //impl Drop for Person {
    //    fn drop(&mut self) {
    //        println!("Dropping the person struct {:?}", self)
    //    }
    //}
    // TODO ^ Try uncommenting these lines

    let person = Person {
        name: String::from("Alice"),
        age: Box::new(20),
    };

    // `name` is moved out of person, but `age` is referenced
    let Person { name, ref age } = person;

    println!("The person's age is {}", age);

    println!("The person's name is {}", name);

    // Error! borrow of partially moved value: `person` partial move occurs
    //println!("The person struct is {:?}", person);

    // `person` cannot be used but `person.age` can be used as it is not moved
    println!("The person's age from person struct is {}", person.age);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Pattern match carefully; use references to avoid partial moves.

_RBE source: `scope/move/partial_move.md`_

---

### 16.1.3 Borrowing

**What it is**

Most of the time, we'd like to access data without taking ownership over
it. To accomplish this, Rust uses a *borrowing* mechanism. Instead of
passing objects by value (`T`), objects can be passed by reference (`&T`). The compiler statically guarantees (via its borrow checker) that references
*always* point to valid objects.

**JS mental model**

Borrowing &T / &mut T is temporary access without taking ownership — like passing a read-only or exclusive view.

**Rust example**

```rust
// This function takes ownership of a box and destroys it
fn eat_box_i32(boxed_i32: Box<i32>) {
    println!("Destroying box that contains {}", boxed_i32);
}

// This function borrows an i32
fn borrow_i32(borrowed_i32: &i32) {
    println!("This int is: {}", borrowed_i32);
}

fn main() {
    // Create a boxed i32 in the heap, and an i32 on the stack
    // Remember: numbers can have arbitrary underscores added for readability
    // 5_i32 is the same as 5i32
    let boxed_i32 = Box::new(5_i32);
    let stacked_i32 = 6_i32;

    // Borrow the contents of the box. Ownership is not taken,
    // so the contents can be borrowed again.
    borrow_i32(&boxed_i32);
    borrow_i32(&stacked_i32);

    {
        // Take a reference to the data contained inside the box
        let _ref_to_i32: &i32 = &boxed_i32;

        // Error!
        // Can't destroy `boxed_i32` while the inner value is borrowed later in scope.
        eat_box_i32(boxed_i32);
        // FIXME ^ Comment out this line

        // Attempt to borrow `_ref_to_i32` after inner value is destroyed
        borrow_i32(_ref_to_i32);
        // `_ref_to_i32` goes out of scope and is no longer borrowed.
    }

    // `boxed_i32` can now give up ownership to `eat_box_i32` and be destroyed
    eat_box_i32(boxed_i32);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Many readers OR one writer — not both.
- Borrows must not outlive the owner.

_RBE source: `scope/borrow.md`_

---

#### 16.1.3.1 Mutability

**What it is**

Mutable data can be mutably borrowed using `&mut T`. This is called
a *mutable reference* and gives read/write access to the borrower. In contrast, `&T` borrows the data via an immutable reference, and
the borrower can read the data but not modify it:
See also:
[`static`][static]
[static]: ../lifetime/static_lifetime.md.

**JS mental model**

&mut T exclusive mutable borrow — no other aliases at the same time.

**Rust example**

```rust
#[allow(dead_code)]
#[derive(Clone, Copy)]
struct Book {
    // `&'static str` is a reference to a string allocated in read only memory
    author: &'static str,
    title: &'static str,
    year: u32,
}

// This function takes a reference to a book
fn borrow_book(book: &Book) {
    println!("I immutably borrowed {} - {} edition", book.title, book.year);
}

// This function takes a reference to a mutable book and changes `year` to 2014
fn new_edition(book: &mut Book) {
    book.year = 2014;
    println!("I mutably borrowed {} - {} edition", book.title, book.year);
}

fn main() {
    // Create an immutable Book named `immutabook`
    let immutabook = Book {
        // string literals have type `&'static str`
        author: "Douglas Hofstadter",
        title: "Gödel, Escher, Bach",
        year: 1979,
    };

    // Create a mutable copy of `immutabook` and call it `mutabook`
    let mut mutabook = immutabook;

    // Immutably borrow an immutable object
    borrow_book(&immutabook);

    // Immutably borrow a mutable object
    borrow_book(&mutabook);

    // Borrow a mutable object as mutable
    new_edition(&mut mutabook);

    // Error! Cannot borrow an immutable object as mutable
    new_edition(&mut immutabook);
    // FIXME ^ Comment out this line
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- This prevents data races and many iterator invalidation bugs.

_RBE source: `scope/borrow/mut.md`_

---

#### 16.1.3.2 Aliasing

**What it is**

Data can be immutably borrowed any number of times, but while immutably
borrowed, the original data can't be mutably borrowed. On the other hand, only
*one* mutable borrow is allowed at a time. The original data can be borrowed
again only *after* the mutable reference has been used for the last time.

**JS mental model**

Aliasing rules: multiple &T OK; &mut T stands alone.

**Rust example**

```rust
struct Point { x: i32, y: i32, z: i32 }

fn main() {
    let mut point = Point { x: 0, y: 0, z: 0 };

    let borrowed_point = &point;
    let another_borrow = &point;

    // Data can be accessed via the references and the original owner
    println!("Point has coordinates: ({}, {}, {})",
                borrowed_point.x, another_borrow.y, point.z);

    // Error! Can't borrow `point` as mutable because it's currently
    // borrowed as immutable.
    // let mutable_borrow = &mut point;
    // TODO ^ Try uncommenting this line

    // The borrowed values are used again here
    println!("Point has coordinates: ({}, {}, {})",
                borrowed_point.x, another_borrow.y, point.z);

    // The immutable references are no longer used for the rest of the code so
    // it is possible to reborrow with a mutable reference.
    let mutable_borrow = &mut point;

    // Change data via mutable reference
    mutable_borrow.x = 5;
    mutable_borrow.y = 2;
    mutable_borrow.z = 1;

    // Error! Can't borrow `point` as immutable because it's currently
    // borrowed as mutable.
    // let y = &point.y;
    // TODO ^ Try uncommenting this line

    // Error! Can't print because `println!` takes an immutable reference.
    // println!("Point Z coordinate is {}", point.z);
    // TODO ^ Try uncommenting this line

    // Ok! Mutable references can be passed as immutable to `println!`
    println!("Point has coordinates: ({}, {}, {})",
                mutable_borrow.x, mutable_borrow.y, mutable_borrow.z);

    // The mutable reference is no longer used for the rest of the code so it
    // is possible to reborrow
    let new_borrowed_point = &point;
    println!("Point now has coordinates: ({}, {}, {})",
             new_borrowed_point.x, new_borrowed_point.y, new_borrowed_point.z);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Interior mutability (RefCell, Mutex) is the escape hatch with runtime checks.

_RBE source: `scope/borrow/alias.md`_

---

#### 16.1.3.3 The ref pattern

**What it is**

When doing pattern matching or destructuring via the `let` binding, the `ref`
keyword can be used to take references to the fields of a struct/tuple. The
example below shows a few instances where this can be useful:.

**JS mental model**

ref in patterns creates a reference to a matched value — older style; & patterns are common now.

**Rust example**

```rust
#[derive(Clone, Copy)]
struct Point { x: i32, y: i32 }

fn main() {
    let c = 'Q';

    // A `ref` borrow on the left side of an assignment is equivalent to
    // an `&` borrow on the right side.
    let ref ref_c1 = c;
    let ref_c2 = &c;

    println!("ref_c1 equals ref_c2: {}", *ref_c1 == *ref_c2);

    let point = Point { x: 0, y: 0 };

    // `ref` is also valid when destructuring a struct.
    let _copy_of_x = {
        // `ref_to_x` is a reference to the `x` field of `point`.
        let Point { x: ref ref_to_x, y: _ } = point;

        // Return a copy of the `x` field of `point`.
        *ref_to_x
    };

    // A mutable copy of `point`
    let mut mutable_point = point;

    {
        // `ref` can be paired with `mut` to take mutable references.
        let Point { x: _, y: ref mut mut_ref_to_y } = mutable_point;

        // Mutate the `y` field of `mutable_point` via a mutable reference.
        *mut_ref_to_y = 1;
    }

    println!("point is ({}, {})", point.x, point.y);
    println!("mutable_point is ({}, {})", mutable_point.x, mutable_point.y);

    // A mutable tuple that includes a pointer
    let mut mutable_tuple = (Box::new(5u32), 3u32);

    {
        // Destructure `mutable_tuple` to change the value of `last`.
        let (_, ref mut last) = mutable_tuple;
        *last = 2u32;
    }

    println!("tuple is {:?}", mutable_tuple);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Useful in match without moving out.

_RBE source: `scope/borrow/ref.md`_

---

### 16.1.4 Lifetimes

**What it is**

A *lifetime* is a construct the compiler (or more specifically, its *borrow
checker*) uses to ensure all borrows are valid. Specifically, a variable's
lifetime begins when it is created and ends when it is destroyed. While
lifetimes and scopes are often referred to together, they are not the same. Take, for example, the case where we borrow a variable via `&`.

**JS mental model**

Lifetimes 'a are names for how long a borrow is valid. Often inferred; sometimes annotated.

**Rust example**

```rust
// Lifetimes are annotated below with lines denoting the creation
// and destruction of each variable.
// `i` has the longest lifetime because its scope entirely encloses
// both `borrow1` and `borrow2`. The duration of `borrow1` compared
// to `borrow2` is irrelevant since they are disjoint.
fn main() {
    let i = 3; // Lifetime for `i` starts. ────────────────┐
    //                                                     │
    { //                                                   │
        let borrow1 = &i; // `borrow1` lifetime starts. ──┐│
        //                                                ││
        println!("borrow1: {}", borrow1); //              ││
    } // `borrow1` ends. ─────────────────────────────────┘│
    //                                                     │
    //                                                     │
    { //                                                   │
        let borrow2 = &i; // `borrow2` lifetime starts. ──┐│
        //                                                ││
        println!("borrow2: {}", borrow2); //              ││
    } // `borrow2` ends. ─────────────────────────────────┘│
    //                                                     │
}   // Lifetime ends. ─────────────────────────────────────┘
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Lifetimes do not create values — they describe relationships.
- JS does not annotate this because GC keeps objects alive.

_RBE source: `scope/lifetime.md`_

---

#### 16.1.4.1 Explicit annotation

**What it is**

The borrow checker uses explicit lifetime annotations to determine
how long references should be valid. In cases where lifetimes are not
elided[^1], Rust requires explicit annotations to determine what the
lifetime of a reference should be. The syntax for explicitly annotating
a lifetime uses an apostrophe character as follows:
Similar to [closures][anonymity], using lifetimes requires generics. Additionally, this lifetime syntax indicates that the lifetime of `foo`
may not exceed that of `'a`.

**JS mental model**

Write <'a> and &'a T when the compiler cannot infer relationships.

**Rust example**

```rust
// `print_refs` takes two references to `i32` which have different
// lifetimes `'a` and `'b`. These two lifetimes must both be at
// least as long as the function `print_refs`.
fn print_refs<'a, 'b>(x: &'a i32, y: &'b i32) {
    println!("x is {} and y is {}", x, y);
}

// A function which takes no arguments, but has a lifetime parameter `'a`.
fn failed_borrow<'a>() {
    let _x = 12;

    // ERROR: `_x` does not live long enough
    let _y: &'a i32 = &_x;
    // Attempting to use the lifetime `'a` as an explicit type annotation
    // inside the function will fail because the lifetime of `&_x` is shorter
    // than that of `_y`. A short lifetime cannot be coerced into a longer one.
}

fn main() {
    // Create variables to be borrowed below.
    let (four, nine) = (4, 9);

    // Borrows (`&`) of both variables are passed into the function.
    print_refs(&four, &nine);
    // Any input which is borrowed must outlive the borrower.
    // In other words, the lifetime of `four` and `nine` must
    // be longer than that of `print_refs`.

    failed_borrow();
    // `failed_borrow` contains no references to force `'a` to be
    // longer than the lifetime of the function, but `'a` is longer.
    // Because the lifetime is never constrained, it defaults to `'static`.
}
```

```rust
foo<'a, 'b>
// `foo` has lifetime parameters `'a` and `'b`
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Start from compiler error suggestions — they are good.

_RBE source: `scope/lifetime/explicit.md`_

---

#### 16.1.4.2 Functions

**What it is**

Ignoring [elision], function signatures with lifetimes have a few constraints:
* any reference *must* have an annotated lifetime. * any reference being returned *must* have the same lifetime as an input or
be `static`. Additionally, note that returning references without input is banned if it
would result in returning references to invalid data. The following example shows
off some valid forms of functions with lifetimes:
See also:
[Functions][fn]
[fn]: ../../fn.md
[elision]: elision.md.

**JS mental model**

Function signatures relate input borrow lifetimes to output borrows.

**Rust example**

```rust
// One input reference with lifetime `'a` which must live
// at least as long as the function.
fn print_one<'a>(x: &'a i32) {
    println!("`print_one`: x is {}", x);
}

// Mutable references are possible with lifetimes as well.
fn add_one<'a>(x: &'a mut i32) {
    *x += 1;
}

// Multiple elements with different lifetimes. In this case, it
// would be fine for both to have the same lifetime `'a`, but
// in more complex cases, different lifetimes may be required.
fn print_multi<'a, 'b>(x: &'a i32, y: &'b i32) {
    println!("`print_multi`: x is {}, y is {}", x, y);
}

// Returning references that have been passed in is acceptable.
// However, the correct lifetime must be returned.
fn pass_x<'a, 'b>(x: &'a i32, _: &'b i32) -> &'a i32 { x }

//fn invalid_output<'a>() -> &'a String { &String::from("foo") }
// The above is invalid: `'a` must live longer than the function.
// Here, `&String::from("foo")` would create a `String`, followed by a
// reference. Then the data is dropped upon exiting the scope, leaving
// a reference to invalid data to be returned.

fn main() {
    let x = 7;
    let y = 9;

    print_one(&x);
    print_multi(&x, &y);

    let z = pass_x(&x, &y);
    print_one(z);

    let mut t = 3;
    add_one(&mut t);
    print_one(&t);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Returning a reference to a local is illegal — dangling pointer prevented.

_RBE source: `scope/lifetime/fn.md`_

---

#### 16.1.4.3 Methods

**What it is**

Methods are annotated similarly to functions:
See also:
[methods]
[methods]: ../../fn/methods.md.

**JS mental model**

Methods can annotate lifetimes on &self and returned references.

**Rust example**

```rust
struct Owner(i32);

impl Owner {
    // Annotate lifetimes as in a standalone function.
    fn add_one<'a>(&'a mut self) { self.0 += 1; }
    fn print<'a>(&'a self) {
        println!("`print`: {}", self.0);
    }
}

fn main() {
    let mut owner = Owner(18);

    owner.add_one();
    owner.print();
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Often elided — write them when returning fields that borrow self.

_RBE source: `scope/lifetime/methods.md`_

---

#### 16.1.4.4 Structs

**What it is**

Annotation of lifetimes in structures are also similar to functions:
See also:
[`struct`s][structs]
[structs]: ../../custom_types/structs.md.

**JS mental model**

Structs that store references need lifetime fields: struct S<'a> { x: &'a str }.

**Rust example**

```rust
// A type `Borrowed` which houses a reference to an
// `i32`. The reference to `i32` must outlive `Borrowed`.
#[derive(Debug)]
struct Borrowed<'a>(&'a i32);

// Similarly, both references here must outlive this structure.
#[derive(Debug)]
struct NamedBorrowed<'a> {
    x: &'a i32,
    y: &'a i32,
}

// An enum which is either an `i32` or a reference to one.
#[derive(Debug)]
enum Either<'a> {
    Num(i32),
    Ref(&'a i32),
}

fn main() {
    let x = 18;
    let y = 15;

    let single = Borrowed(&x);
    let double = NamedBorrowed { x: &x, y: &y };
    let reference = Either::Ref(&x);
    let number    = Either::Num(y);

    println!("x is borrowed in {:?}", single);
    println!("x and y are borrowed in {:?}", double);
    println!("x is borrowed in {:?}", reference);
    println!("y is *not* borrowed in {:?}", number);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- The struct cannot outlive the data it borrows.

_RBE source: `scope/lifetime/struct.md`_

---

#### 16.1.4.5 Traits

**What it is**

Annotation of lifetimes in trait methods basically are similar to functions. Note that `impl` may have annotation of lifetimes too. See also:
[`trait`s][trait]
[trait]: ../../trait.md.

**JS mental model**

Traits can involve lifetimes too — e.g. methods returning borrows.

**Rust example**

```rust
// A struct with annotation of lifetimes.
#[derive(Debug)]
struct Borrowed<'a> {
    x: &'a i32,
}

// Annotate lifetimes to impl.
impl<'a> Default for Borrowed<'a> {
    fn default() -> Self {
        Self {
            x: &10,
        }
    }
}

fn main() {
    let b: Borrowed = Default::default();
    println!("b is {:?}", b);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Object safety / dyn can complicate lifetimes — advanced.

_RBE source: `scope/lifetime/trait.md`_

---

#### 16.1.4.6 Bounds

**What it is**

Just like generic types can be bounded, lifetimes (themselves generic)
use bounds as well. The `:` character has a slightly different meaning here,
but `+` is the same. Note how the following read:
1. `T: 'a`: *All* references in `T` must outlive lifetime `'a`.

**JS mental model**

T: 'a means T must live at least as long as 'a.

**Rust example**

```rust
use std::fmt::Debug; // Trait to bound with.

#[derive(Debug)]
struct Ref<'a, T: 'a>(&'a T);
// `Ref` contains a reference to a generic type `T` that has
// some lifetime `'a` unknown by `Ref`. `T` is bounded such that any
// *references* in `T` must outlive `'a`. Additionally, the lifetime
// of `Ref` may not exceed `'a`.

// A generic function which prints using the `Debug` trait.
fn print<T>(t: T) where
    T: Debug {
    println!("`print`: t is {:?}", t);
}

// Here a reference to `T` is taken where `T` implements
// `Debug` and all *references* in `T` outlive `'a`. In
// addition, `'a` must outlive the function.
fn print_ref<'a, T>(t: &'a T) where
    T: Debug + 'a {
    println!("`print_ref`: t is {:?}", t);
}

fn main() {
    let x = 7;
    let ref_x = Ref(&x);

    print_ref(&ref_x);
    print(ref_x);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Common when storing references alongside generic data.

_RBE source: `scope/lifetime/lifetime_bounds.md`_

---

#### 16.1.4.7 Coercion

**What it is**

A longer lifetime can be coerced into a shorter one
so that it works inside a scope it normally wouldn't work in. This comes in the form of inferred coercion by the Rust compiler,
and also in the form of declaring a lifetime difference:.

**JS mental model**

Longer lifetimes can coerce to shorter ones (subtype-ish).

**Rust example**

```rust
// Here, Rust infers a lifetime that is as short as possible.
// The two references are then coerced to that lifetime.
fn multiply<'a>(first: &'a i32, second: &'a i32) -> i32 {
    first * second
}

// `<'a: 'b, 'b>` reads as lifetime `'a` is at least as long as `'b`.
// Here, we take in an `&'a i32` and return a `&'b i32` as a result of coercion.
fn choose_first<'a: 'b, 'b>(first: &'a i32, _: &'b i32) -> &'b i32 {
    first
}

fn main() {
    let first = 2; // Longer lifetime

    {
        let second = 3; // Shorter lifetime

        println!("The product is {}", multiply(&first, &second));
        println!("{} is the first", choose_first(&first, &second));
    };
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- You can shorten; you cannot invent longer lifetimes.

_RBE source: `scope/lifetime/lifetime_coercion.md`_

---

#### 16.1.4.8 Static

**What it is**

Rust has a few reserved lifetime names. One of those is `'static`. You
might encounter it in two situations:
Both are related but subtly different and this is a common source for
confusion when learning Rust. Here are some examples for each situation:
Reference lifetime
As a reference lifetime `'static` indicates that the data pointed to by
the reference lives for the remaining lifetime of the running program.

**JS mental model**

 'static lives for the whole program — string literals are 'static.

**Rust example**

```rust
// Make a constant with `'static` lifetime.
static NUM: i32 = 18;

// Returns a reference to `NUM` where its `'static`
// lifetime is coerced to that of the input argument.
fn coerce_static<'a>(_: &'a i32) -> &'a i32 {
    &NUM
}

fn main() {
    {
        // Make a `string` literal and print it:
        let static_string = "I'm in read-only memory";
        println!("static_string: {}", static_string);

        // When `static_string` goes out of scope, the reference
        // can no longer be used, but the data remains in the binary.
    }

    {
        // Make an integer to use for `coerce_static`:
        let lifetime_num = 9;

        // Coerce `NUM` to lifetime of `lifetime_num`:
        let coerced_static = coerce_static(&lifetime_num);

        println!("coerced_static: {}", coerced_static);
    }

    println!("NUM: {} stays accessible!", NUM);
}
```

```rust
use std::fmt::Debug;

fn print_it(input: impl Debug + 'static) {
    println!("'static value passed in is: {:?}", input);
}

fn main() {
    // i is owned and contains no references, thus it's 'static:
    let i = 5;
    print_it(i);

    // oops, &i only has the lifetime defined by the scope of
    // main(), so it's not 'static:
    print_it(&i);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Not every &'static needs to be a literal — also leaked/owned globals.
- Do not overuse 'static bounds on generics.

_RBE source: `scope/lifetime/static_lifetime.md`_

---

#### 16.1.4.9 Elision

**What it is**

Some lifetime patterns are overwhelmingly common and so the borrow checker
will allow you to omit them to save typing and to improve readability. This is known as elision. Elision exists in Rust solely because these patterns
are common. The following code shows a few examples of elision.

**JS mental model**

Lifetime rules let you omit lifetimes in common function shapes.

**Rust example**

```rust
// `elided_input` and `annotated_input` essentially have identical signatures
// because the lifetime of `elided_input` is inferred by the compiler:
fn elided_input(x: &i32) {
    println!("`elided_input`: {}", x);
}

fn annotated_input<'a>(x: &'a i32) {
    println!("`annotated_input`: {}", x);
}

// Similarly, `elided_pass` and `annotated_pass` have identical signatures
// because the lifetime is added implicitly to `elided_pass`:
fn elided_pass(x: &i32) -> &i32 { x }

fn annotated_pass<'a>(x: &'a i32) -> &'a i32 { x }

fn main() {
    let x = 3;

    elided_input(&x);
    annotated_input(&x);

    println!("`elided_pass`: {}", elided_pass(&x));
    println!("`annotated_pass`: {}", annotated_pass(&x));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- If elision cannot decide, the compiler asks for explicit annotations.

_RBE source: `scope/lifetime/elision.md`_

---

## 17. Traits

_RBE chapter root maps here. Every nested leaf below has the full 5-part teaching block._

### 17.1 Traits

**What it is**

A `trait` is a collection of methods defined for an unknown type:
`Self`. They can access other methods declared in the same trait. Traits can be implemented for any data type. In the example below,
we define `Animal`, a group of methods.

**JS mental model**

Traits are like interfaces / protocols. Types opt in with impl Trait for Type.

**Rust example**

```rust
struct Sheep { naked: bool, name: &'static str }

trait Animal {
    // Associated function signature; `Self` refers to the implementor type.
    fn new(name: &'static str) -> Self;

    // Method signatures; these will return a string.
    fn name(&self) -> &'static str;
    fn noise(&self) -> &'static str;

    // Traits can provide default method definitions.
    fn talk(&self) {
        println!("{} says {}", self.name(), self.noise());
    }
}

impl Sheep {
    fn is_naked(&self) -> bool {
        self.naked
    }

    fn shear(&mut self) {
        if self.is_naked() {
            // Implementor methods can use the implementor's trait methods.
            println!("{} is already naked...", self.name());
        } else {
            println!("{} gets a haircut!", self.name);

            self.naked = true;
        }
    }
}

// Implement the `Animal` trait for `Sheep`.
impl Animal for Sheep {
    // `Self` is the implementor type: `Sheep`.
    fn new(name: &'static str) -> Sheep {
        Sheep { name: name, naked: false }
    }

    fn name(&self) -> &'static str {
        self.name
    }

    fn noise(&self) -> &'static str {
        if self.is_naked() {
            "baaaaah?"
        } else {
            "baaaaah!"
        }
    }

    // Default trait methods can be overridden.
    fn talk(&self) {
        // For example, we can add some quiet contemplation.
        println!("{} pauses briefly... {}", self.name, self.noise());
    }
}

fn main() {
    // Type annotation is necessary in this case.
    let mut dolly: Sheep = Animal::new("Dolly");
    // TODO ^ Try removing the type annotations.

    dolly.talk();
    dolly.shear();
    dolly.talk();
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Orphan rules limit impls — one of trait/type must be local.
- Default methods allowed.

_RBE source: `trait.md`_

---

### 17.1.1 Derive

**What it is**

The compiler is capable of providing basic implementations for some traits via
the `#[derive]` [attribute][attribute]. These traits can still be
manually implemented if a more complex behavior is required. The following is a list of derivable traits:
* Comparison traits:
  [`Eq`][eq], [`PartialEq`][partial-eq], [`Ord`][ord], [`PartialOrd`][partial-ord]. * [`Clone`][clone], to create `T` from `&T` via a copy.

**JS mental model**

#[derive(Debug, Clone)] auto-implements common traits — like generated methods.

**Rust example**

```rust
// `Centimeters`, a tuple struct that can be compared
#[derive(PartialEq, PartialOrd)]
struct Centimeters(f64);

// `Inches`, a tuple struct that can be printed
#[derive(Debug)]
struct Inches(i32);

impl Inches {
    fn to_centimeters(&self) -> Centimeters {
        let &Inches(inches) = self;

        Centimeters(inches as f64 * 2.54)
    }
}

// `Seconds`, a tuple struct with no additional attributes
struct Seconds(i32);

fn main() {
    let _one_second = Seconds(1);

    // Error: `Seconds` can't be printed; it doesn't implement the `Debug` trait
    //println!("One second looks like: {:?}", _one_second);
    // TODO ^ Try uncommenting this line

    // Error: `Seconds` can't be compared; it doesn't implement the `PartialEq` trait
    //let _this_is_true = (_one_second == _one_second);
    // TODO ^ Try uncommenting this line

    let foot = Inches(12);

    println!("One foot equals {:?}", foot);

    let meter = Centimeters(100.0);

    let cmp =
        if foot.to_centimeters() < meter {
            "smaller"
        } else {
            "bigger"
        };

    println!("One foot is {} than one meter.", cmp);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Only works for traits that support derive.
- Fields must also implement the trait.

_RBE source: `trait/derive.md`_

---

### 17.1.2 Returning Traits with dyn

**What it is**

The Rust compiler needs to know how much space every function's return type requires. This means all
your functions have to return a concrete type. Unlike other languages, if you have a trait like
`Animal`, you can't write a function that returns `Animal`, because its different implementations
will need different amounts of memory. However, there's an easy workaround.

**JS mental model**

Box<dyn Trait> is dynamic dispatch (vtable) like interface references in OOP. impl Trait is static.

**Rust example**

```rust
struct Sheep {}
struct Cow {}

trait Animal {
    // Instance method signature
    fn noise(&self) -> &'static str;
}

// Implement the `Animal` trait for `Sheep`.
impl Animal for Sheep {
    fn noise(&self) -> &'static str {
        "baaaaah!"
    }
}

// Implement the `Animal` trait for `Cow`.
impl Animal for Cow {
    fn noise(&self) -> &'static str {
        "moooooo!"
    }
}

// Returns some struct that implements Animal, but we don't know which one at compile time.
fn random_animal(random_number: f64) -> Box<dyn Animal> {
    if random_number < 0.5 {
        Box::new(Sheep {})
    } else {
        Box::new(Cow {})
    }
}

fn main() {
    let random_number = 0.234;
    let animal = random_animal(random_number);
    println!("You've randomly chosen an animal, and it says {}", animal.noise());
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- dyn has a small runtime cost; size must be boxed/referenced.
- Not all traits are object-safe.

_RBE source: `trait/dyn.md`_

---

### 17.1.3 Operator Overloading

**What it is**

In Rust, many of the operators can be overloaded via traits. That is, some operators can
be used to accomplish different tasks based on their input arguments. This is possible
because operators are syntactic sugar for method calls. For example, the `+` operator in
`a + b` calls the `add` method (as in `a.add(b)`).

**JS mental model**

Overload + etc. by implementing std::ops traits — explicit and typed.

**Rust example**

```rust
use std::ops;

struct Foo;
struct Bar;

#[derive(Debug)]
struct FooBar;

#[derive(Debug)]
struct BarFoo;

// The `std::ops::Add` trait is used to specify the functionality of `+`.
// Here, we make `Add<Bar>` - the trait for addition with a RHS of type `Bar`.
// The following block implements the operation: Foo + Bar = FooBar
impl ops::Add<Bar> for Foo {
    type Output = FooBar;

    fn add(self, _rhs: Bar) -> FooBar {
        println!("> Foo.add(Bar) was called");

        FooBar
    }
}

// By reversing the types, we end up implementing non-commutative addition.
// Here, we make `Add<Foo>` - the trait for addition with a RHS of type `Foo`.
// This block implements the operation: Bar + Foo = BarFoo
impl ops::Add<Foo> for Bar {
    type Output = BarFoo;

    fn add(self, _rhs: Foo) -> BarFoo {
        println!("> Bar.add(Foo) was called");

        BarFoo
    }
}

fn main() {
    println!("Foo + Bar = {:?}", Foo + Bar);
    println!("Bar + Foo = {:?}", Bar + Foo);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Keep operators intuitive.
- Prefer named methods if meaning is unclear.

_RBE source: `trait/ops.md`_

---

### 17.1.4 Drop

**What it is**

The [`Drop`][Drop] trait only has one method: `drop`, which is called automatically
when an object goes out of scope. The main use of the `Drop` trait is to free the
resources that the implementor instance owns. `Box`, `Vec`, `String`, `File`, and `Process` are some examples of types that
implement the `Drop` trait to free resources. The `Drop` trait can also be
manually implemented for any custom data type.

**JS mental model**

Custom destructor via Drop — like a finalizer that actually runs at known time.

**Rust example**

```rust
use std::fs::File;
use std::path::PathBuf;

struct TempFile {
    file: File,
    path: PathBuf,
}

impl TempFile {
    fn new(path: PathBuf) -> std::io::Result<Self> {
        // Note: File::create() will overwrite existing files
        let file = File::create(&path)?;

        Ok(Self { file, path })
    }
}

// When TempFile is dropped:
// 1. First, our custom drop implementation runs. The file is still open at this point,
//    but we can remove it from the filesystem by path.
// 2. Then, after our drop returns, Rust automatically drops each field,
//    so File's drop runs and closes the file handle.
impl Drop for TempFile {
    fn drop(&mut self) {
        // Note: the File is still open here — field destructors run after this method.
        if let Err(e) = std::fs::remove_file(&self.path) {
            eprintln!("Failed to remove temporary file: {}", e);
        }
        println!("> Dropped temporary file: {:?}", self.path);
        // After this method returns, Rust will drop each field (including `file`),
        // which closes the underlying file handle.
    }
}

fn main() -> std::io::Result<()> {
    // Create a new scope to demonstrate drop behavior
    {
        let temp = TempFile::new("test.txt".into())?;
        println!("Temporary file created");
        // File will be automatically cleaned up when temp goes out of scope
    }
    println!("End of scope - file should be cleaned up");

    // We can also manually drop if needed
    let temp2 = TempFile::new("another_test.txt".into())?;
    drop(temp2); // Explicitly drop the file
    println!("Manually dropped file");

    Ok(())
}
```

```rust
struct Droppable {
    name: &'static str,
}

// This trivial implementation of `drop` adds a print to console.
impl Drop for Droppable {
    fn drop(&mut self) {
        println!("> Dropping {}", self.name);
    }
}

fn main() {
    let _a = Droppable { name: "a" };

    // block A
    {
        let _b = Droppable { name: "b" };

        // block B
        {
            let _c = Droppable { name: "c" };
            let _d = Droppable { name: "d" };

            println!("Exiting block B");
        }
        println!("Just exited block B");

        println!("Exiting block A");
    }
    println!("Just exited block A");

    // Variable can be manually dropped using the `drop` function
    drop(_a);
    // TODO ^ Try commenting this line

    println!("end of the main function");

    // `_a` *won't* be `drop`ed again here, because it already has been
    // (manually) `drop`ed
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Do not panic in Drop casually.
- Drop order: fields after the Drop impl runs.

_RBE source: `trait/drop.md`_

---

### 17.1.5 Iterators

**What it is**

The [`Iterator`][iter] trait is used to implement iterators over collections
such as arrays. The trait requires only a method to be defined for the `next` element,
which may be manually defined in an `impl` block or automatically
defined (as in arrays and ranges). As a point of convenience for common situations, the `for` construct
turns some collections into iterators using the [`.into_iter()`][intoiter] method. [intoiter]: https://doc.rust-lang.org/std/iter/trait.IntoIterator.html
[iter]: https://doc.rust-lang.org/core/iter/trait.Iterator.html.

**JS mental model**

Implement Iterator for your type — like making an object iterable ([Symbol.iterator]).

**Rust example**

```rust
struct Fibonacci {
    curr: u32,
    next: u32,
}

// Implement `Iterator` for `Fibonacci`.
// The `Iterator` trait only requires a method to be defined for the `next` element,
// and an `associated type` to declare the return type of the iterator.
impl Iterator for Fibonacci {
    // We can refer to this type using Self::Item
    type Item = u32;

    // Here, we define the sequence using `.curr` and `.next`.
    // The return type is `Option<T>`:
    //     * When the `Iterator` is finished, `None` is returned.
    //     * Otherwise, the next value is wrapped in `Some` and returned.
    // We use Self::Item in the return type, so we can change
    // the type without having to update the function signatures.
    fn next(&mut self) -> Option<Self::Item> {
        let current = self.curr;

        self.curr = self.next;
        self.next = current + self.next;

        // Since there's no endpoint to a Fibonacci sequence, the `Iterator`
        // will never return `None`, and `Some` is always returned.
        Some(current)
    }
}

// Returns a Fibonacci sequence generator
fn fibonacci() -> Fibonacci {
    Fibonacci { curr: 0, next: 1 }
}

fn main() {
    // `0..3` is an `Iterator` that generates: 0, 1, and 2.
    let mut sequence = 0..3;

    println!("Four consecutive `next` calls on 0..3");
    println!("> {:?}", sequence.next());
    println!("> {:?}", sequence.next());
    println!("> {:?}", sequence.next());
    println!("> {:?}", sequence.next());

    // `for` works through an `Iterator` until it returns `None`.
    // Each `Some` value is unwrapped and bound to a variable (here, `i`).
    println!("Iterate through 0..3 using `for`");
    for i in 0..3 {
        println!("> {}", i);
    }

    // The `take(n)` method reduces an `Iterator` to its first `n` terms.
    println!("The first four terms of the Fibonacci sequence are: ");
    for i in fibonacci().take(4) {
        println!("> {}", i);
    }

    // The `skip(n)` method shortens an `Iterator` by dropping its first `n` terms.
    println!("The next four terms of the Fibonacci sequence are: ");
    for i in fibonacci().skip(4).take(4) {
        println!("> {}", i);
    }

    let array = [1u32, 3, 3, 7];

    // The `iter` method produces an `Iterator` over an array/slice.
    println!("Iterate the following array {:?}", &array);
    for i in array.iter() {
        println!("> {}", i);
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Just implement next returning Option<Item>.
- Adapters come for free.

_RBE source: `trait/iter.md`_

---

### 17.1.6 impl Trait

**What it is**

`impl Trait` can be used in two locations:
1. as an argument type
2. as a return type
As an argument type
If your function is generic over a trait but you don't mind the specific type, you can simplify the function declaration using `impl Trait` as the type of the argument. For example, consider the following code:
`parse_csv_document` is generic, allowing it to take any type which implements BufRead, such as `BufReader<File>` or `[u8]`,
but it's not important what type `R` is, and `R` is only used to declare the type of `src`, so the function can also be written as:
Note that using `impl Trait` as an argument type means that you cannot explicitly state what form of the function you use, i.e.

**JS mental model**

impl Trait in return position hides a concrete type — like returning some iterable without naming it.

**Rust example**

```rust
use std::iter;
use std::vec::IntoIter;

// This function combines two `Vec<i32>` and returns an iterator over it.
// Look how complicated its return type is!
fn combine_vecs_explicit_return_type(
    v: Vec<i32>,
    u: Vec<i32>,
) -> iter::Cycle<iter::Chain<IntoIter<i32>, IntoIter<i32>>> {
    v.into_iter().chain(u.into_iter()).cycle()
}

// This is the exact same function, but its return type uses `impl Trait`.
// Look how much simpler it is!
fn combine_vecs(
    v: Vec<i32>,
    u: Vec<i32>,
) -> impl Iterator<Item=i32> {
    v.into_iter().chain(u.into_iter()).cycle()
}

fn main() {
    let v1 = vec![1, 2, 3];
    let v2 = vec![4, 5];
    let mut v3 = combine_vecs(v1, v2);
    assert_eq!(Some(1), v3.next());
    assert_eq!(Some(2), v3.next());
    assert_eq!(Some(3), v3.next());
    assert_eq!(Some(4), v3.next());
    assert_eq!(Some(5), v3.next());
    println!("all done");
}
```

```rust
fn parse_csv_document<R: std::io::BufRead>(src: R) -> std::io::Result<Vec<Vec<String>>> {
    src.lines()
        .map(|line| {
            // For each line in the source
            line.map(|line| {
                // If the line was read successfully, process it, if not, return the error
                line.split(',') // Split the line separated by commas
                    .map(|entry| String::from(entry.trim())) // Remove leading and trailing whitespace
                    .collect() // Collect all strings in a row into a Vec<String>
            })
        })
        .collect() // Collect all lines into a Vec<Vec<String>>
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Also usable in argument position as anonymous generics.
- Different from dyn Trait.

_RBE source: `trait/impl_trait.md`_

---

### 17.1.7 Clone

**What it is**

When dealing with resources, the default behavior is to transfer them during
assignments or function calls. However, sometimes we need to make a
copy of the resource as well. The [`Clone`][clone] trait helps us do exactly this. Most commonly, we can
use the `.clone()` method defined by the `Clone` trait.

**JS mental model**

Clone is explicit copy via .clone() — JS copies are usually shallow by default and silent.

**Rust example**

```rust
// A unit struct without resources
// Note: Copy requires Clone, so we must derive both
#[derive(Debug, Clone, Copy)]
struct Unit;

// A tuple struct with resources that implements the `Clone` trait
// This CANNOT be Copy because Box<T> is not Copy
#[derive(Clone, Debug)]
struct Pair(Box<i32>, Box<i32>);

fn main() {
    // Instantiate `Unit`
    let unit = Unit;
    // Copy `Unit` - this is an implicit copy, not a move!
    // Because Unit implements Copy, the value is duplicated automatically
    let copied_unit = unit;

    // Both `Unit`s can be used independently
    println!("original: {:?}", unit);
    println!("copy: {:?}", copied_unit);

    // Instantiate `Pair`
    let pair = Pair(Box::new(1), Box::new(2));
    println!("original: {:?}", pair);

    // Move `pair` into `moved_pair`, moves resources
    // Pair does not implement Copy, so this is a move
    let moved_pair = pair;
    println!("moved: {:?}", moved_pair);

    // Error! `pair` has lost its resources
    //println!("original: {:?}", pair);
    // TODO ^ Try uncommenting this line

    // Clone `moved_pair` into `cloned_pair` (resources are included)
    // Unlike Copy, Clone is explicit - we must call .clone()
    let cloned_pair = moved_pair.clone();
    // Drop the moved original pair using std::mem::drop
    drop(moved_pair);

    // Error! `moved_pair` has been dropped
    //println!("moved and dropped: {:?}", moved_pair);
    // TODO ^ Try uncommenting this line

    // The result from .clone() can still be used!
    println!("clone: {:?}", cloned_pair);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Copy is implicit bitwise copy for simple types.
- Derive when all fields allow it.

_RBE source: `trait/clone.md`_

---

### 17.1.8 Supertraits

**What it is**

Rust doesn't have "inheritance", but you can define a trait as being a superset
of another trait. For example:
See also:
[The Rust Programming Language chapter on supertraits][trpl_supertraits]
[trpl_supertraits]: https://doc.rust-lang.org/book/ch19-03-advanced-traits.html#using-supertraits-to-require-one-traits-functionality-within-another-trait.

**JS mental model**

trait B: A means B requires A — like interface extends.

**Rust example**

```rust
trait Person {
    fn name(&self) -> String;
}

// Person is a supertrait of Student.
// Implementing Student requires you to also impl Person.
trait Student: Person {
    fn university(&self) -> String;
}

trait Programmer {
    fn fav_language(&self) -> String;
}

// CompSciStudent (computer science student) is a subtrait of both Programmer
// and Student. Implementing CompSciStudent requires you to impl both supertraits.
trait CompSciStudent: Programmer + Student {
    fn git_username(&self) -> String;
}

fn comp_sci_student_greeting(student: &dyn CompSciStudent) -> String {
    format!(
        "My name is {} and I attend {}. My favorite language is {}. My Git username is {}",
        student.name(),
        student.university(),
        student.fav_language(),
        student.git_username()
    )
}

struct CSStudent {
    name: String,
    university: String,
    fav_language: String,
    git_username: String
}

impl Programmer for CSStudent {
    fn fav_language(&self) -> String {
        self.fav_language.clone()
    }
}

impl Student for CSStudent {
    fn university(&self) -> String {
        self.university.clone()
    }
}

impl Person for CSStudent {
    fn name(&self) -> String {
        self.name.clone()
    }
}

impl CompSciStudent for CSStudent {
    fn git_username(&self) -> String {
        self.git_username.clone()
    }
}

fn main() {
    let student = CSStudent {
        name: String::from("Alice"),
        university: String::from("MIT"),
        fav_language: String::from("Rust"),
        git_username: String::from("alice_codes"),
    };

    let greeting = comp_sci_student_greeting(&student);
    println!("{}", greeting);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Implementing B requires A's methods too.

_RBE source: `trait/supertraits.md`_

---

### 17.1.9 Disambiguating overlapping traits

**What it is**

A type can implement many different traits. What if two traits both require
the same name for a function? For example, many traits might have a method
named `get()`. They might even have different return types!

**JS mental model**

When two traits define the same method name, write Trait::method(&x).

**Rust example**

```rust
trait UsernameWidget {
    // Get the selected username out of this widget
    fn get(&self) -> String;
}

trait AgeWidget {
    // Get the selected age out of this widget
    fn get(&self) -> u8;
}

// A form with both a UsernameWidget and an AgeWidget
struct Form {
    username: String,
    age: u8,
}

impl UsernameWidget for Form {
    fn get(&self) -> String {
        self.username.clone()
    }
}

impl AgeWidget for Form {
    fn get(&self) -> u8 {
        self.age
    }
}

fn main() {
    let form = Form {
        username: "rustacean".to_owned(),
        age: 28,
    };

    // If you uncomment this line, you'll get an error saying
    // "multiple `get` found". Because, after all, there are multiple methods
    // named `get`.
    // println!("{}", form.get());

    let username = <Form as UsernameWidget>::get(&form);
    assert_eq!("rustacean".to_owned(), username);
    let age = <Form as AgeWidget>::get(&form);
    assert_eq!(28, age);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Same idea as name clashes with mixins — Rust forces clarity.

_RBE source: `trait/disambiguating.md`_

---

## 18. Macros

_RBE chapter root maps here. Every nested leaf below has the full 5-part teaching block._

### 18.1 macro_rules!

**What it is**

Rust provides a powerful macro system that allows metaprogramming. As you've
seen in previous chapters, macros look like functions, except that their name
ends with a bang `!`, but instead of generating a function call, macros are
expanded into source code that gets compiled with the rest of the program. However, unlike macros in C and other languages, Rust macros are expanded into
abstract syntax trees, rather than string preprocessing, so you don't get
unexpected precedence bugs. Macros can be created using the `macro_rules!` macro.

**JS mental model**

Macros expand at compile time to code. macro_rules! is pattern-based — not runtime eval.

**Rust example**

```rust
// This is a simple macro named `say_hello`.
macro_rules! say_hello {
    // `()` indicates that the macro takes no argument.
    () => {
        // The macro will expand into the contents of this block.
        println!("Hello!")
    };
}

fn main() {
    // This call will expand into `println!("Hello!")`
    say_hello!()
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Macro errors can be noisy — grow them slowly.
- Prefer functions when possible.

_RBE source: `macros.md`_

---

### 18.1.1 Syntax

**What it is**

Macro syntax uses declarative rules: match the input tokens against patterns and emit output tokens. Invocation looks like a function call but with !.

**JS mental model**

Define with macro_rules! name { (pattern) => { expansion } }. Invoke with name!.

**Rust example**

```rust
macro_rules! say_hello {
    () => {
        println!("Hello!");
    };
}

fn main() {
    say_hello!();
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Each rule is a matching arm.
- Use $ for metavariables.

_RBE source: `macros/syntax.md`_

---

#### 18.1.1.1 Designators

**What it is**

Designators are the typed holes in macro patterns. For example $name:ident matches an identifier, $e:expr matches an expression. They guide the parser so macros are safer than raw text substitution.

**JS mental model**

Fragment designators tell what kind of thing $x is: ident, expr, ty, block, tt, ...

**Rust example**

```rust
macro_rules! create_function {
    // This macro takes an argument of designator `ident` and
    // creates a function named `$func_name`.
    // The `ident` designator is used for variable/function names.
    ($func_name:ident) => {
        fn $func_name() {
            // The `stringify!` macro converts an `ident` into a string.
            println!("You called {:?}()",
                     stringify!($func_name));
        }
    };
}

// Create functions named `foo` and `bar` with the above macro.
create_function!(foo);
create_function!(bar);

macro_rules! print_result {
    // This macro takes an expression of type `expr` and prints
    // it as a string along with its result.
    // The `expr` designator is used for expressions.
    ($expression:expr) => {
        // `stringify!` will convert the expression *as it is* into a string.
        println!("{:?} = {:?}",
                 stringify!($expression),
                 $expression);
    };
}

fn main() {
    foo();
    bar();

    print_result!(1u32 + 1);

    // Recall that blocks are expressions too!
    print_result!({
        let x = 1u32;

        x * x + 2 * x - 1
    });
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Wrong designator = match failure.
- tt is a flexible token tree.

_RBE source: `macros/designators.md`_

---

#### 18.1.1.2 Overload

**What it is**

A macro can have several (pattern) => { ... } arms. Depending on how you call it, a different arm expands. This is how one macro name supports multiple syntaxes.

**JS mental model**

Multiple rules overload the macro for different call shapes — like function overloads but for syntax.

**Rust example**

```rust
// `test!` will compare `$left` and `$right`
// in different ways depending on how you invoke it:
macro_rules! test {
    // Arguments don't need to be separated by a comma.
    // Any template can be used!
    ($left:expr; and $right:expr) => {
        println!("{:?} and {:?} is {:?}",
                 stringify!($left),
                 stringify!($right),
                 $left && $right)
    };
    // ^ each arm must end with a semicolon.
    ($left:expr; or $right:expr) => {
        println!("{:?} or {:?} is {:?}",
                 stringify!($left),
                 stringify!($right),
                 $left || $right)
    };
}

fn main() {
    test!(1i32 + 1 == 2i32; and 2i32 * 2 == 4i32);
    test!(true; or false);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- First match wins — order matters.
- Keep rules specific before general.

_RBE source: `macros/overload.md`_

---

#### 18.1.1.3 Repeat

**What it is**

Repetition lets a macro accept a list of items. In the pattern you write $($x:expr),* and in the body you expand $($x),* or map each piece.

**JS mental model**

$(...),* repeats patterns — like rest/variadic pieces in a macro.

**Rust example**

```rust
// `find_min!` will calculate the minimum of any number of arguments.
macro_rules! find_min {
    // Base case:
    ($x:expr) => ($x);
    // `$x` followed by at least one `$y,`
    ($x:expr, $($y:expr),+) => (
        // Call `find_min!` on the tail `$y`
        std::cmp::min($x, find_min!($($y),+))
    )
}

fn main() {
    println!("{}", find_min!(1));
    println!("{}", find_min!(1 + 2, 2));
    println!("{}", find_min!(5, 2 * 3, 4));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- * = zero or more, + = one or more, ? = optional.
- You can repeat in both pattern and expansion.

_RBE source: `macros/repeat.md`_

---

### 18.1.2 DRY (Don't Repeat Yourself)

**What it is**

DRY macros generate repeated implementations (for example operator helpers for many types) from one template. You write the pattern once and instantiate it for each case.

**JS mental model**

Macros remove boilerplate across many similar impls/tests — DRY at the syntax level.

**Rust example**

```rust
use std::ops::{Add, Mul, Sub};

macro_rules! assert_equal_len {
    // The `tt` (token tree) designator is used for
    // operators and tokens.
    ($a:expr, $b:expr, $func:ident, $op:tt) => {
        assert!($a.len() == $b.len(),
                "{:?}: dimension mismatch: {:?} {:?} {:?}",
                stringify!($func),
                ($a.len(),),
                stringify!($op),
                ($b.len(),));
    };
}

macro_rules! op {
    ($func:ident, $bound:ident, $op:tt, $method:ident) => {
        fn $func<T: $bound<T, Output=T> + Copy>(xs: &mut Vec<T>, ys: &Vec<T>) {
            assert_equal_len!(xs, ys, $func, $op);

            for (x, y) in xs.iter_mut().zip(ys.iter()) {
                *x = $bound::$method(*x, *y);
                // *x = x.$method(*y);
            }
        }
    };
}

// Implement `add_assign`, `mul_assign`, and `sub_assign` functions.
op!(add_assign, Add, +=, add);
op!(mul_assign, Mul, *=, mul);
op!(sub_assign, Sub, -=, sub);

mod test {
    use std::iter;
    macro_rules! test {
        ($func:ident, $x:expr, $y:expr, $z:expr) => {
            #[test]
            fn $func() {
                for size in 0usize..10 {
                    let mut x: Vec<_> = iter::repeat($x).take(size).collect();
                    let y: Vec<_> = iter::repeat($y).take(size).collect();
                    let z: Vec<_> = iter::repeat($z).take(size).collect();

                    super::$func(&mut x, &y);

                    assert_eq!(x, z);
                }
            }
        };
    }

    // Test `add_assign`, `mul_assign`, and `sub_assign`.
    test!(add_assign, 1u32, 2u32, 3u32);
    test!(mul_assign, 2u32, 3u32, 6u32);
    test!(sub_assign, 3u32, 2u32, 1u32);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Harder to read — document what the macro generates.
- Consider if a generic function is enough first.

_RBE source: `macros/dry.md`_

---

### 18.1.3 DSL (Domain Specific Languages)

**What it is**

A DSL macro makes special-looking syntax compile into ordinary Rust. println! itself is a small DSL for formatting. Your macros can offer eval-style sugar similarly.

**JS mental model**

Macros can create mini languages (sql!, html!) — domain-specific syntax.

**Rust example**

```rust
macro_rules! calculate {
    (eval $e:expr) => {
        {
            let val: usize = $e; // Force types to be unsigned integers
            println!("{} = {}", stringify!{$e}, val);
        }
    };
}

fn main() {
    calculate! {
        eval 1 + 2 // hehehe `eval` is _not_ a Rust keyword!
    }

    calculate! {
        eval (1 + 2) * (3 / 4)
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Great for ergonomics; cost is learning another mini-syntax.
- Keep DSL macros well documented.

_RBE source: `macros/dsl.md`_

---

### 18.1.4 Variadics

**What it is**

Variadic-looking APIs are usually macros. They accept a variable number of arguments by matching repetitions, then expand into fixed Rust code.

**JS mental model**

Rust functions are not variadic like C, but macros can accept variable arguments (println!).

**Rust example**

```rust
macro_rules! calculate {
    // The pattern for a single `eval`
    (eval $e:expr) => {
        {
            let val: usize = $e; // Force types to be integers
            println!("{} = {}", stringify!{$e}, val);
        }
    };

    // Decompose multiple `eval`s recursively
    (eval $e:expr, $(eval $es:expr),+) => {{
        calculate! { eval $e }
        calculate! { $(eval $es),+ }
    }};
}

fn main() {
    calculate! { // Look ma! Variadic `calculate!`!
        eval 1 + 2,
        eval 3 + 4,
        eval (2 * 3) + 1
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Use repetition to emulate variadic APIs.
- Type checking still happens after expansion.

_RBE source: `macros/variadics.md`_

---

## 19. Error handling

_RBE chapter root maps here. Every nested leaf below has the full 5-part teaching block._

### 19.1 Error handling

**What it is**

Error handling is the process of handling the possibility of failure. For
example, failing to read a file and then continuing to use that *bad* input
would clearly be problematic. Noticing and explicitly managing those errors
saves the rest of the program from various pitfalls. There are various ways to deal with errors in Rust, which are described in the
following subchapters.

**JS mental model**

Recoverable errors use Result; absence uses Option; crashes use panic!. No exceptions by default.

**Rust example**

```rust
fn fallible() -> Result<i32, &'static str> {
    Ok(1)
}

fn main() -> Result<(), &'static str> {
    let n = fallible()?;
    println!("{n}");
    Ok(())
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Prefer Result over panic for expected failures.
- ? propagates errors upward.

_RBE source: `error.md`_

---

### 19.1.1 panic

**What it is**

The simplest error handling mechanism we will see is `panic`. It prints an
error message, starts unwinding the stack, and usually exits the program. Here, we explicitly call `panic` on our error condition:
The first call to `drink` works. The second panics and thus the third is never called.

**JS mental model**

panic! aborts the current thread stack unwind (or aborts process) — like throwing without catch.

**Rust example**

```rust
fn drink(beverage: &str) {
    // You shouldn't drink too many sugary beverages.
    if beverage == "lemonade" { panic!("AAAaaaaa!!!!"); }

    println!("Some refreshing {} is all I need.", beverage);
}

fn main() {
    drink("water");
    drink("lemonade");
    drink("still water");
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Use for bugs / cannot continue.
- Not for normal validation failures.

_RBE source: `error/panic.md`_

---

### 19.1.2 abort & unwind

**What it is**

The previous section illustrates the error handling mechanism `panic`. Different code paths can be conditionally compiled based on the panic setting. The current values available are `unwind` and `abort`. Building on the prior lemonade example, we explicitly use the panic strategy to exercise different lines of code.

**JS mental model**

Profiles can abort-on-panic (smaller binaries) or unwind — like choosing process crash vs try/finally cleanup.

**Rust example**

```rust
fn drink(beverage: &str) {
    // You shouldn't drink too much sugary beverages.
    if beverage == "lemonade" {
        if cfg!(panic = "abort") {
            println!("This is not your party. Run!!!!");
        } else {
            println!("Spit it out!!!!");
        }
    } else {
        println!("Some refreshing {} is all I need.", beverage);
    }
}

fn main() {
    drink("water");
    drink("lemonade");
}
```

```rust
#[cfg(panic = "unwind")]
fn ah() {
    println!("Spit it out!!!!");
}

#[cfg(not(panic = "unwind"))]
fn ah() {
    println!("This is not your party. Run!!!!");
}

fn drink(beverage: &str) {
    if beverage == "lemonade" {
        ah();
    } else {
        println!("Some refreshing {} is all I need.", beverage);
    }
}

fn main() {
    drink("water");
    drink("lemonade");
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Libraries should be careful what they assume.
- Set in Cargo.toml profile.

_RBE source: `error/abort_unwind.md`_

---

### 19.1.3 Option & unwrap

**What it is**

In the last example, we showed that we can induce program failure at will. We told our program to `panic` if we drink a sugary lemonade. But what if we expect _some_ drink but don't receive one? That case would be just as bad, so it needs to be handled!

**JS mental model**

Option<T> is Some(value) or None — null safety built-in. unwrap means give value or panic.

**Rust example**

```rust
// The adult has seen it all, and can handle any drink well.
// All drinks are handled explicitly using `match`.
fn give_adult(drink: Option<&str>) {
    // Specify a course of action for each case.
    match drink {
        Some("lemonade") => println!("Yuck! Too sugary."),
        Some(inner)   => println!("{}? How nice.", inner),
        None          => println!("No drink? Oh well."),
    }
}

// Others will `panic` before drinking sugary drinks.
// All drinks are handled implicitly using `unwrap`.
fn drink(drink: Option<&str>) {
    // `unwrap` returns a `panic` when it receives a `None`.
    let inside = drink.unwrap();
    if inside == "lemonade" { panic!("AAAaaaaa!!!!"); }

    println!("I love {}s!!!!!", inside);
}

fn main() {
    let water  = Some("water");
    let lemonade = Some("lemonade");
    let void  = None;

    give_adult(water);
    give_adult(lemonade);
    give_adult(void);

    let coffee = Some("coffee");
    let nothing = None;

    drink(coffee);
    drink(nothing);
}
```

**JS equivalent**

```javascript
const x = maybe ?? defaultValue;
const y = maybe?.field;
```

**Watch out**

- Prefer ?, map, or match over unwrap in libraries.
- expect("msg") panics with a message.

_RBE source: `error/option_unwrap.md`_

---

#### 19.1.3.1 Unpacking options with ?

**What it is**

You can unpack `Option`s by using `match` statements, but it's often easier to
use the `?` operator. If `x` is an `Option`, then evaluating `x?` will return
the underlying value if `x` is `Some`, otherwise it will terminate whatever
function is being executed and return `None`. You can chain many `?`s together to make your code much more readable.

**JS mental model**

? on Option returns early with None — like optional chaining with early return.

**Rust example**

```rust
struct Person {
    job: Option<Job>,
}

#[derive(Clone, Copy)]
struct Job {
    phone_number: Option<PhoneNumber>,
}

#[derive(Clone, Copy)]
#[allow(dead_code)]
struct PhoneNumber {
    area_code: Option<u8>,
    number: u32,
}

impl Person {

    // Gets the area code of the phone number of the person's job, if it exists.
    fn work_phone_area_code(&self) -> Option<u8> {
        // This would need many nested `match` statements without the `?` operator.
        // It would take a lot more code - try writing it yourself and see which
        // is easier.
        self.job?.phone_number?.area_code
    }
}

fn main() {
    let p = Person {
        job: Some(Job {
            phone_number: Some(PhoneNumber {
                area_code: Some(61),
                number: 439222222,
            }),
        }),
    };

    assert_eq!(p.work_phone_area_code(), Some(61));
}
```

```rust
fn next_birthday(current_age: Option<u8>) -> Option<String> {
    // If `current_age` is `None`, this returns `None`.
    // If `current_age` is `Some`, the inner `u8` value + 1
    // gets assigned to `next_age`
    let next_age: u8 = current_age? + 1;
    Some(format!("Next year I will be {}", next_age))
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Function must return Option (or compatible).

_RBE source: `error/option_unwrap/question_mark.md`_

---

#### 19.1.3.2 Combinators: map

**What it is**

`match` is a valid method for handling `Option`s. However, you may
eventually find heavy usage tedious, especially with operations only valid
with an input. In these cases, [combinators][combinators] can be used to
manage control flow in a modular fashion. `Option` has a built in method called `map()`, a combinator for the simple
mapping of `Some -> Some` and `None -> None`.

**JS mental model**

opt.map(f) transforms Some — like optional map.

**Rust example**

```rust
#![allow(dead_code)]

#[derive(Debug)] enum Food { Apple, Carrot, Potato }

#[derive(Debug)] struct Peeled(Food);
#[derive(Debug)] struct Chopped(Food);
#[derive(Debug)] struct Cooked(Food);

// Peeling food. If there isn't any, then return `None`.
// Otherwise, return the peeled food.
fn peel(food: Option<Food>) -> Option<Peeled> {
    match food {
        Some(food) => Some(Peeled(food)),
        None       => None,
    }
}

// Chopping food. If there isn't any, then return `None`.
// Otherwise, return the chopped food.
fn chop(peeled: Option<Peeled>) -> Option<Chopped> {
    match peeled {
        Some(Peeled(food)) => Some(Chopped(food)),
        None               => None,
    }
}

// Cooking food. Here, we showcase `map()` instead of `match` for case handling.
fn cook(chopped: Option<Chopped>) -> Option<Cooked> {
    chopped.map(|Chopped(food)| Cooked(food))
}

// A function to peel, chop, and cook food all in sequence.
// We chain multiple uses of `map()` to simplify the code.
fn process(food: Option<Food>) -> Option<Cooked> {
    food.map(|f| Peeled(f))
        .map(|Peeled(f)| Chopped(f))
        .map(|Chopped(f)| Cooked(f))
}

// Check whether there's food or not before trying to eat it!
fn eat(food: Option<Cooked>) {
    match food {
        Some(food) => println!("Mmm. I love {:?}", food),
        None       => println!("Oh no! It wasn't edible."),
    }
}

fn main() {
    let apple = Some(Food::Apple);
    let carrot = Some(Food::Carrot);
    let potato = None;

    let cooked_apple = cook(chop(peel(apple)));
    let cooked_carrot = cook(chop(peel(carrot)));
    // Let's try the simpler looking `process()` now.
    let cooked_potato = process(potato);

    eat(cooked_apple);
    eat(cooked_carrot);
    eat(cooked_potato);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- None stays None.

_RBE source: `error/option_unwrap/map.md`_

---

#### 19.1.3.3 Combinators: and_then

**What it is**

`map()` was described as a chainable way to simplify `match` statements. However, using `map()` on a function that returns an `Option<T>` results
in the nested `Option<Option<T>>`. Chaining multiple calls together can
then become confusing. That's where another combinator called `and_then()`,
known in some languages as flatmap, comes in.

**JS mental model**

and_then is flatMap for Option — when f itself returns Option.

**Rust example**

```rust
#![allow(dead_code)]

#[derive(Debug)] enum Food { CordonBleu, Steak, Sushi }
#[derive(Debug)] enum Day { Monday, Tuesday, Wednesday }

// We don't have the ingredients to make Sushi.
fn have_ingredients(food: Food) -> Option<Food> {
    match food {
        Food::Sushi => None,
        _           => Some(food),
    }
}

// We have the recipe for everything except Cordon Bleu.
fn have_recipe(food: Food) -> Option<Food> {
    match food {
        Food::CordonBleu => None,
        _                => Some(food),
    }
}

// To make a dish, we need both the recipe and the ingredients.
// We can represent the logic with a chain of `match`es:
fn cookable_v1(food: Food) -> Option<Food> {
    match have_recipe(food) {
        None       => None,
        Some(food) => have_ingredients(food),
    }
}

// This can conveniently be rewritten more compactly with `and_then()`:
fn cookable_v3(food: Food) -> Option<Food> {
    have_recipe(food).and_then(have_ingredients)
}

// Otherwise we'd need to `flatten()` an `Option<Option<Food>>`
// to get an `Option<Food>`:
fn cookable_v2(food: Food) -> Option<Food> {
    have_recipe(food).map(have_ingredients).flatten()
}

fn eat(food: Food, day: Day) {
    match cookable_v3(food) {
        Some(food) => println!("Yay! On {:?} we get to eat {:?}.", day, food),
        None       => println!("Oh no. We don't get to eat on {:?}?", day),
    }
}

fn main() {
    let (cordon_bleu, steak, sushi) = (Food::CordonBleu, Food::Steak, Food::Sushi);

    eat(cordon_bleu, Day::Monday);
    eat(steak, Day::Tuesday);
    eat(sushi, Day::Wednesday);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Avoids Option<Option<T>>.

_RBE source: `error/option_unwrap/and_then.md`_

---

#### 19.1.3.4 Defaults: or, or_else, get_or_insert, get_or_insert_with

**What it is**

There is more than one way to unpack an `Option` and fall back on a default if it is `None`. To choose the one that meets our needs, we need to consider the following:
* do we need eager or lazy evaluation? * do we need to keep the original empty value intact, or modify it in place? `or()` is chainable, evaluates eagerly, keeps empty value intact
`or()`is chainable and eagerly evaluates its argument, as is shown in the following example.

**JS mental model**

or / or_else / unwrap_or / get_or_insert supply defaults — like ?? and lazy defaults.

**Rust example**

```rust
#[derive(Debug)]
enum Fruit { Apple, Orange, Banana, Kiwi, Lemon }

fn main() {
    let mut my_fruit: Option<Fruit> = None;
    let get_lemon_as_fallback = || {
        println!("Providing lemon as fallback");
        Fruit::Lemon
    };
    let first_available_fruit = my_fruit
        .get_or_insert_with(get_lemon_as_fallback);
    println!("first_available_fruit is: {:?}", first_available_fruit);
    println!("my_fruit is: {:?}", my_fruit);
    // Providing lemon as fallback
    // first_available_fruit is: Lemon
    // my_fruit is: Some(Lemon)

    // If the Option has a value, it is left unchanged, and the closure is not invoked
    let mut my_apple = Some(Fruit::Apple);
    let should_be_apple = my_apple.get_or_insert_with(get_lemon_as_fallback);
    println!("should_be_apple is: {:?}", should_be_apple);
    println!("my_apple is unchanged: {:?}", my_apple);
    // The output is a follows. Note that the closure `get_lemon_as_fallback` is not invoked
    // should_be_apple is: Apple
    // my_apple is unchanged: Some(Apple)
}
```

```rust
#[derive(Debug)]
enum Fruit { Apple, Orange, Banana, Kiwi, Lemon }

fn main() {
    let apple = Some(Fruit::Apple);
    let orange = Some(Fruit::Orange);
    let no_fruit: Option<Fruit> = None;

    let first_available_fruit = no_fruit.or(orange).or(apple);
    println!("first_available_fruit: {:?}", first_available_fruit);
    // first_available_fruit: Some(Orange)

    // `or` moves its argument.
    // In the example above, `or(orange)` returned a `Some`, so `or(apple)` was not invoked.
    // But the variable named `apple` has been moved regardless, and cannot be used anymore.
    // println!("Variable apple was moved, so this line won't compile: {:?}", apple);
    // TODO: uncomment the line above to see the compiler error
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- or_else is lazy (closure); or is eager.

_RBE source: `error/option_unwrap/defaults.md`_

---

### 19.1.4 Result

**What it is**

[`Result`][result] is a richer version of the [`Option`][option] type that
describes possible *error* instead of possible *absence*. That is, `Result<T, E>` could have one of two outcomes:
* `Ok(T)`: An element `T` was found
* `Err(E)`: An error was found with element `E`
By convention, the expected outcome is `Ok` while the unexpected outcome is `Err`. Like `Option`, `Result` has many methods associated with it. `unwrap()`, for
example, either yields the element `T` or `panic`s.

**JS mental model**

Result<T,E> is Ok(T) or Err(E) — success/failure without exceptions.

**Rust example**

```rust
fn multiply(first_number_str: &str, second_number_str: &str) -> i32 {
    // Let's try using `unwrap()` to get the number out. Will it bite us?
    let first_number = first_number_str.parse::<i32>().unwrap();
    let second_number = second_number_str.parse::<i32>().unwrap();
    first_number * second_number
}

fn main() {
    let twenty = multiply("10", "2");
    println!("double is {}", twenty);

    let tt = multiply("t", "2");
    println!("double is {}", tt);
}
```

```rust
use std::num::ParseIntError;

fn main() -> Result<(), ParseIntError> {
    let number_str = "10";
    let number = match number_str.parse::<i32>() {
        Ok(number)  => number,
        Err(e) => return Err(e),
    };
    println!("{}", number);
    Ok(())
}
```

**JS equivalent**

```javascript
try { doWork(); } catch (e) { handle(e); }
```

**Watch out**

- match / ? / combinators to handle.
- Errors are values.

_RBE source: `error/result.md`_

---

#### 19.1.4.1 map for Result

**What it is**

Panicking in the previous example's `multiply` does not make for robust code. Generally, we want to return the error to the caller so it can decide what is
the right way to respond to errors. We first need to know what kind of error type we are dealing with. To determine
the `Err` type, we look to [`parse()`][parse], which is implemented with the
[`FromStr`][from_str] trait for [`i32`][i32].

**JS mental model**

map / map_err transform Ok/Err sides — like Promise.then on success only.

**Rust example**

```rust
use std::num::ParseIntError;

// With the return type rewritten, we use pattern matching without `unwrap()`.
fn multiply(first_number_str: &str, second_number_str: &str) -> Result<i32, ParseIntError> {
    match first_number_str.parse::<i32>() {
        Ok(first_number)  => {
            match second_number_str.parse::<i32>() {
                Ok(second_number)  => {
                    Ok(first_number * second_number)
                },
                Err(e) => Err(e),
            }
        },
        Err(e) => Err(e),
    }
}

fn print(result: Result<i32, ParseIntError>) {
    match result {
        Ok(n)  => println!("n is {}", n),
        Err(e) => println!("Error: {}", e),
    }
}

fn main() {
    // This still presents a reasonable answer.
    let twenty = multiply("10", "2");
    print(twenty);

    // The following now provides a much more helpful error message.
    let tt = multiply("t", "2");
    print(tt);
}
```

```rust
use std::num::ParseIntError;

// As with `Option`, we can use combinators such as `map()`.
// This function is otherwise identical to the one above and reads:
// Multiply if both values can be parsed from str, otherwise pass on the error.
fn multiply(first_number_str: &str, second_number_str: &str) -> Result<i32, ParseIntError> {
    first_number_str.parse::<i32>().and_then(|first_number| {
        second_number_str.parse::<i32>().map(|second_number| first_number * second_number)
    })
}

fn print(result: Result<i32, ParseIntError>) {
    match result {
        Ok(n)  => println!("n is {}", n),
        Err(e) => println!("Error: {}", e),
    }
}

fn main() {
    // This still presents a reasonable answer.
    let twenty = multiply("10", "2");
    print(twenty);

    // The following now provides a much more helpful error message.
    let tt = multiply("t", "2");
    print(tt);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Leaves the other side unchanged.

_RBE source: `error/result/result_map.md`_

---

#### 19.1.4.2 aliases for Result

**What it is**

How about when we want to reuse a specific `Result` type many times? Recall that Rust allows us to create [aliases][typealias]. Conveniently,
we can define one for the specific `Result` in question. At a module level, creating aliases can be particularly helpful.

**JS mental model**

type AliasedResult<T> = Result<T, MyError> shortens signatures — like a shared error type alias.

**Rust example**

```rust
use std::num::ParseIntError;

// Define a generic alias for a `Result` with the error type `ParseIntError`.
type AliasedResult<T> = Result<T, ParseIntError>;

// Use the above alias to refer to our specific `Result` type.
fn multiply(first_number_str: &str, second_number_str: &str) -> AliasedResult<i32> {
    first_number_str.parse::<i32>().and_then(|first_number| {
        second_number_str.parse::<i32>().map(|second_number| first_number * second_number)
    })
}

// Here, the alias again allows us to save some space.
fn print(result: AliasedResult<i32>) {
    match result {
        Ok(n)  => println!("n is {}", n),
        Err(e) => println!("Error: {}", e),
    }
}

fn main() {
    print(multiply("10", "2"));
    print(multiply("t", "2"));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Common in modules with one error type.

_RBE source: `error/result/result_alias.md`_

---

#### 19.1.4.3 Early returns

**What it is**

In the previous example, we explicitly handled the errors using combinators. Another way to deal with this case analysis is to use a combination of
`match` statements and *early returns*. That is, we can simply stop executing the function and return the error if
one occurs. For some, this form of code can be easier to both read and
write.

**JS mental model**

Match on Err and return Err(...) manually — the pattern ? automates.

**Rust example**

```rust
use std::num::ParseIntError;

fn multiply(first_number_str: &str, second_number_str: &str) -> Result<i32, ParseIntError> {
    let first_number = match first_number_str.parse::<i32>() {
        Ok(first_number)  => first_number,
        Err(e) => return Err(e),
    };

    let second_number = match second_number_str.parse::<i32>() {
        Ok(second_number)  => second_number,
        Err(e) => return Err(e),
    };

    Ok(first_number * second_number)
}

fn print(result: Result<i32, ParseIntError>) {
    match result {
        Ok(n)  => println!("n is {}", n),
        Err(e) => println!("Error: {}", e),
    }
}

fn main() {
    print(multiply("10", "2"));
    print(multiply("t", "2"));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Good to understand before using ? everywhere.

_RBE source: `error/result/early_returns.md`_

---

#### 19.1.4.4 Introducing ?

**What it is**

Sometimes we just want the simplicity of `unwrap` without the possibility of
a `panic`. Until now, `unwrap` has forced us to nest deeper and deeper when
what we really wanted was to get the variable *out*. This is exactly the purpose of `?`. Upon finding an `Err`, there are two valid actions to take:
1.

**JS mental model**

? unwraps Ok or returns Err from the function — like error propagation without try/catch.

**Rust example**

```rust
// To compile and run this example without errors, while using Cargo, change the value
// of the `edition` field, in the `[package]` section of the `Cargo.toml` file, to "2015".

use std::num::ParseIntError;

fn multiply(first_number_str: &str, second_number_str: &str) -> Result<i32, ParseIntError> {
    let first_number = try!(first_number_str.parse::<i32>());
    let second_number = try!(second_number_str.parse::<i32>());

    Ok(first_number * second_number)
}

fn print(result: Result<i32, ParseIntError>) {
    match result {
        Ok(n)  => println!("n is {}", n),
        Err(e) => println!("Error: {}", e),
    }
}

fn main() {
    print(multiply("10", "2"));
    print(multiply("t", "2"));
}
```

```rust
use std::num::ParseIntError;

fn multiply(first_number_str: &str, second_number_str: &str) -> Result<i32, ParseIntError> {
    let first_number = first_number_str.parse::<i32>()?;
    let second_number = second_number_str.parse::<i32>()?;

    Ok(first_number * second_number)
}

fn print(result: Result<i32, ParseIntError>) {
    match result {
        Ok(n)  => println!("n is {}", n),
        Err(e) => println!("Error: {}", e),
    }
}

fn main() {
    print(multiply("10", "2"));
    print(multiply("t", "2"));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Converts compatible errors via From.
- Function return type must be Result (or Option, etc.).

_RBE source: `error/result/enter_question_mark.md`_

---

### 19.1.5 Multiple error types

**What it is**

The previous examples have always been very convenient; `Result`s interact
with other `Result`s and `Option`s interact with other `Option`s. Sometimes an `Option` needs to interact with a `Result`, or a
`Result<T, Error1>` needs to interact with a `Result<T, Error2>`. In those
cases, we want to manage our different error types in a way that makes them
composable and easy to interact with. In the following code, two instances of `unwrap` generate different error
types.

**JS mental model**

Real programs mix error types — need aliases, boxing, or custom wrappers.

**Rust example**

```rust
fn double_first(vec: Vec<&str>) -> i32 {
    let first = vec.first().unwrap(); // Generate error 1
    2 * first.parse::<i32>().unwrap() // Generate error 2
}

fn main() {
    let numbers = vec!["42", "93", "18"];
    let empty = vec![];
    let strings = vec!["tofu", "93", "18"];

    println!("The first doubled is {}", double_first(numbers));

    println!("The first doubled is {}", double_first(empty));
    // Error 1: the input vector is empty

    println!("The first doubled is {}", double_first(strings));
    // Error 2: the element doesn't parse to a number
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Pick one strategy per crate layer.

_RBE source: `error/multiple_error_types.md`_

---

#### 19.1.5.1 Pulling Results out of Options

**What it is**

The most basic way of handling mixed error types is to just embed them in each
other. There are times when we'll want to stop processing on errors (like with
[`?`][enter_question_mark]) but keep going when the `Option` is `None`. The `transpose` function comes in handy to swap the `Result` and `Option`. [enter_question_mark]: ../result/enter_question_mark.md.

**JS mental model**

Composing Option and Result needs careful conversion (ok_or, transpose).

**Rust example**

```rust
use std::num::ParseIntError;

fn double_first(vec: Vec<&str>) -> Option<Result<i32, ParseIntError>> {
    vec.first().map(|first| {
        first.parse::<i32>().map(|n| 2 * n)
    })
}

fn main() {
    let numbers = vec!["42", "93", "18"];
    let empty = vec![];
    let strings = vec!["tofu", "93", "18"];

    println!("The first doubled is {:?}", double_first(numbers));

    println!("The first doubled is {:?}", double_first(empty));
    // Error 1: the input vector is empty

    println!("The first doubled is {:?}", double_first(strings));
    // Error 2: the element doesn't parse to a number
}
```

```rust
use std::num::ParseIntError;

fn double_first(vec: Vec<&str>) -> Result<Option<i32>, ParseIntError> {
    let opt = vec.first().map(|first| {
        first.parse::<i32>().map(|n| 2 * n)
    });

    opt.transpose()
}

fn main() {
    let numbers = vec!["42", "93", "18"];
    let empty = vec![];
    let strings = vec!["tofu", "93", "18"];

    println!("The first doubled is {:?}", double_first(numbers));
    println!("The first doubled is {:?}", double_first(empty));
    println!("The first doubled is {:?}", double_first(strings));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- transpose turns Option<Result<T,E>> into Result<Option<T>,E>.

_RBE source: `error/multiple_error_types/option_result.md`_

---

#### 19.1.5.2 Defining an error type

**What it is**

Sometimes it simplifies the code to mask all of the different errors with a
single type of error. We'll show this with a custom error. Rust allows us to define our own error types. In general, a "good" error type:
* Represents different errors with the same type
* Presents nice error messages to the user
* Is easy to compare with other types
  * Good: `Err(EmptyVec)`
  * Bad: `Err("Please use a vector with at least one element".to_owned())`
* Can hold information about the error
  * Good: `Err(BadChar(c, position))`
  * Bad: `Err("+ cannot be used here".to_owned())`
* Composes well with other errors.

**JS mental model**

Define your own error enum — like a custom Error subclass hierarchy but as data.

**Rust example**

```rust
use std::fmt;

type Result<T> = std::result::Result<T, DoubleError>;

// Define our error types. These may be customized for our error handling cases.
// Now we will be able to write our own errors, defer to an underlying error
// implementation, or do something in between.
#[derive(Debug, Clone)]
struct DoubleError;

// Generation of an error is completely separate from how it is displayed.
// There's no need to be concerned about cluttering complex logic with the display style.
//
// Note that we don't store any extra info about the errors. This means we can't state
// which string failed to parse without modifying our types to carry that information.
impl fmt::Display for DoubleError {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "invalid first item to double")
    }
}

fn double_first(vec: Vec<&str>) -> Result<i32> {
    vec.first()
        // Change the error to our new type.
        .ok_or(DoubleError)
        .and_then(|s| {
            s.parse::<i32>()
                // Update to the new error type here also.
                .map_err(|_| DoubleError)
                .map(|i| 2 * i)
        })
}

fn print(result: Result<i32>) {
    match result {
        Ok(n) => println!("The first doubled is {}", n),
        Err(e) => println!("Error: {}", e),
    }
}

fn main() {
    let numbers = vec!["42", "93", "18"];
    let empty = vec![];
    let strings = vec!["tofu", "93", "18"];

    print(double_first(numbers));
    print(double_first(empty));
    print(double_first(strings));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Implement Display + Error.
- thiserror crate helps later.

_RBE source: `error/multiple_error_types/define_error_type.md`_

---

#### 19.1.5.3 Boxing errors

**What it is**

A way to write simple code while preserving the original errors is to [`Box`][box]
them. The drawback is that the underlying error type is only known at runtime and not
[statically determined][dynamic_dispatch]. The stdlib helps in boxing our errors by having `Box` implement conversion from
any type that implements the `Error` trait into the trait object `Box<Error>`,
via [`From`][from]. See also:
[Dynamic dispatch][dynamic_dispatch] and [`Error` trait][error]
[box]: https://doc.rust-lang.org/std/boxed/struct.Box.html
[dynamic_dispatch]: https://doc.rust-lang.org/book/ch17-02-trait-objects.html#trait-objects-perform-dynamic-dispatch
[error]: https://doc.rust-lang.org/std/error/trait.Error.html
[from]: https://doc.rust-lang.org/std/convert/trait.From.html.

**JS mental model**

Box<dyn Error> erases error type — like catching unknown error interface.

**Rust example**

```rust
use std::error;
use std::fmt;

// Change the alias to use `Box<dyn error::Error>`.
type Result<T> = std::result::Result<T, Box<dyn error::Error>>;

#[derive(Debug, Clone)]
struct EmptyVec;

impl fmt::Display for EmptyVec {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "invalid first item to double")
    }
}

impl error::Error for EmptyVec {}

fn double_first(vec: Vec<&str>) -> Result<i32> {
    vec.first()
        .ok_or_else(|| EmptyVec.into()) // Converts to Box using Into trait.
        .and_then(|s| {
            s.parse::<i32>()
                .map_err(From::from) // Converts to Box using From::from fn pointer.
                .map(|i| 2 * i)
        })
}

fn print(result: Result<i32>) {
    match result {
        Ok(n) => println!("The first doubled is {}", n),
        Err(e) => println!("Error: {}", e),
    }
}

fn main() {
    let numbers = vec!["42", "93", "18"];
    let empty = vec![];
    let strings = vec!["tofu", "93", "18"];

    print(double_first(numbers));
    print(double_first(empty));
    print(double_first(strings));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Simple for apps; libraries often prefer concrete errors.
- Lose static matching on variants.

_RBE source: `error/multiple_error_types/boxing_errors.md`_

---

#### 19.1.5.4 Other uses of ?

**What it is**

Notice in the previous example that our immediate reaction to calling
`parse` is to `map` the error from a library error into a boxed
error:
Since this is a simple and common operation, it would be convenient if it
could be elided. Alas, because `and_then` is not sufficiently flexible, it
cannot. However, we can instead use `?`. `?` was previously explained as either `unwrap` or `return Err(err)`.

**JS mental model**

? can convert errors via From — one ? can map underlying errors into yours.

**Rust example**

```rust
use std::error;
use std::fmt;

// Change the alias to use `Box<dyn error::Error>`.
type Result<T> = std::result::Result<T, Box<dyn error::Error>>;

#[derive(Debug)]
struct EmptyVec;

impl fmt::Display for EmptyVec {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "invalid first item to double")
    }
}

impl error::Error for EmptyVec {}

// The same structure as before but rather than chain all `Results`
// and `Options` along, we `?` to get the inner value out immediately.
fn double_first(vec: Vec<&str>) -> Result<i32> {
    let first = vec.first().ok_or(EmptyVec)?;
    let parsed = first.parse::<i32>()?;
    Ok(2 * parsed)
}

fn print(result: Result<i32>) {
    match result {
        Ok(n)  => println!("The first doubled is {}", n),
        Err(e) => println!("Error: {}", e),
    }
}

fn main() {
    let numbers = vec!["42", "93", "18"];
    let empty = vec![];
    let strings = vec!["tofu", "93", "18"];

    print(double_first(numbers));
    print(double_first(empty));
    print(double_first(strings));
}
```

```rust
.and_then(|s| s.parse::<i32>())
    .map_err(|e| e.into())
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Implement From<Other> for MyError.

_RBE source: `error/multiple_error_types/reenter_question_mark.md`_

---

#### 19.1.5.5 Wrapping errors

**What it is**

An alternative to boxing errors is to wrap them in your own error type. This adds a bit more boilerplate for handling errors and might not be needed in
all applications. There are some libraries that can take care of the boilerplate
for you. See also:
[`From::from`][from] and [`Enums`][enums]
[`Crates for handling errors`][crates-errors]
[from]: https://doc.rust-lang.org/std/convert/trait.From.html
[enums]: ../../custom_types/enum.md
[crates-errors]: https://crates.io/keywords/error-handling.

**JS mental model**

Wrap underlying errors in your enum to add context — like cause chaining.

**Rust example**

```rust
use std::error;
use std::error::Error;
use std::num::ParseIntError;
use std::fmt;

type Result<T> = std::result::Result<T, DoubleError>;

#[derive(Debug)]
enum DoubleError {
    EmptyVec,
    // We will defer to the parse error implementation for their error.
    // Supplying extra info requires adding more data to the type.
    Parse(ParseIntError),
}

impl fmt::Display for DoubleError {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        match *self {
            DoubleError::EmptyVec =>
                write!(f, "please use a vector with at least one element"),
            // The wrapped error contains additional information and is available
            // via the source() method.
            DoubleError::Parse(..) =>
                write!(f, "the provided string could not be parsed as int"),
        }
    }
}

impl error::Error for DoubleError {
    fn source(&self) -> Option<&(dyn error::Error + 'static)> {
        match *self {
            DoubleError::EmptyVec => None,
            // The cause is the underlying implementation error type. Is implicitly
            // cast to the trait object `&error::Error`. This works because the
            // underlying type already implements the `Error` trait.
            DoubleError::Parse(ref e) => Some(e),
        }
    }
}

// Implement the conversion from `ParseIntError` to `DoubleError`.
// This will be automatically called by `?` if a `ParseIntError`
// needs to be converted into a `DoubleError`.
impl From<ParseIntError> for DoubleError {
    fn from(err: ParseIntError) -> DoubleError {
        DoubleError::Parse(err)
    }
}

fn double_first(vec: Vec<&str>) -> Result<i32> {
    let first = vec.first().ok_or(DoubleError::EmptyVec)?;
    // Here we implicitly use the `ParseIntError` implementation of `From` (which
    // we defined above) in order to create a `DoubleError`.
    let parsed = first.parse::<i32>()?;

    Ok(2 * parsed)
}

fn print(result: Result<i32>) {
    match result {
        Ok(n)  => println!("The first doubled is {}", n),
        Err(e) => {
            println!("Error: {}", e);
            if let Some(source) = e.source() {
                println!("  Caused by: {}", source);
            }
        },
    }
}

fn main() {
    let numbers = vec!["42", "93", "18"];
    let empty = vec![];
    let strings = vec!["tofu", "93", "18"];

    print(double_first(numbers));
    print(double_first(empty));
    print(double_first(strings));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- source() on std::error::Error enables chains.
- thiserror/anyhow are popular helpers.

_RBE source: `error/multiple_error_types/wrap_error.md`_

---

### 19.1.6 Iterating over Results

**What it is**

An `Iter::map` operation might fail, for example:
Let's step through strategies for handling this. Ignore the failed items with `filter_map()`
`filter_map` calls a function and filters out the results that are `None`. Collect the failed items with `map_err()` and `filter_map()`
`map_err` calls a function with the error, so by adding that to the previous
`filter_map` solution we can save them off to the side while iterating. Fail the entire operation with `collect()`
`Result` implements `FromIterator` so that a vector of results (`Vec<Result<T, E>>`)
can be turned into a result with a vector (`Result<Vec<T>, E>`).

**JS mental model**

Iterators of Results: collect into Result<Vec<_>, E> or partition successes/failures.

**Rust example**

```rust
fn main() {
    let strings = vec!["tofu", "93", "18"];
    let (numbers, errors): (Vec<_>, Vec<_>) = strings
        .into_iter()
        .map(|s| s.parse::<i32>())
        .partition(Result::is_ok);
    let numbers: Vec<_> = numbers.into_iter().map(Result::unwrap).collect();
    let errors: Vec<_> = errors.into_iter().map(Result::unwrap_err).collect();
    println!("Numbers: {:?}", numbers);
    println!("Errors: {:?}", errors);
}
```

```rust
fn main() {
    let strings = vec!["42", "tofu", "93", "999", "18"];
    let mut errors = vec![];
    let numbers: Vec<_> = strings
        .into_iter()
        .map(|s| s.parse::<u8>())
        .filter_map(|r| r.map_err(|e| errors.push(e)).ok())
        .collect();
    println!("Numbers: {:?}", numbers);
    println!("Errors: {:?}", errors);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- collect::<Result<Vec<_>,_>>() stops at first Err.
- Decide fail-fast vs gather-all.

_RBE source: `error/iter_result.md`_

---

## 20. Std library types

_RBE chapter root maps here. Every nested leaf below has the full 5-part teaching block._

### 20.1 Std library types

**What it is**

The `std` library provides many custom types which expands drastically on
the `primitives`. Some of these include:
* growable `String`s like: `"hello world"`
* growable vectors: `[1, 2, 3]`
* optional types: `Option<i32>`
* error handling types: `Result<i32, i32>`
* heap allocated pointers: `Box<i32>`
See also:
[primitives] and [the std library][std]
[primitives]: primitives.md
[std]: https://doc.rust-lang.org/std/.

**JS mental model**

The standard library gives you Vec, String, HashMap, Option, Result, smart pointers — like JS built-ins but explicit about allocation and ownership.

**Rust example**

```rust
fn main() {
    let v = vec![1, 2, 3];
    let s = String::from("hi");
    println!("{v:?} {s}");
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- prelude imports common items automatically.
- std assumes an OS; core is freestanding.

_RBE source: `std.md`_

---

### 20.1.1 Box, stack and heap

**What it is**

The stack holds fixed-size local data. The heap holds dynamically sized or long-lived data. Box<T> owns a heap value of type T. When the Box drops, the heap memory is freed.

**JS mental model**

Box<T> is a heap allocation — like a single-object malloc with ownership. JS objects are heap by default; Rust puts many things on the stack unless boxed.

**Rust example**

```rust
use std::mem;

#[allow(dead_code)]
#[derive(Debug, Clone, Copy)]
struct Point {
    x: f64,
    y: f64,
}

// A Rectangle can be specified by where its top left and bottom right
// corners are in space
#[allow(dead_code)]
struct Rectangle {
    top_left: Point,
    bottom_right: Point,
}

fn origin() -> Point {
    Point { x: 0.0, y: 0.0 }
}

fn boxed_origin() -> Box<Point> {
    // Allocate this point on the heap, and return a pointer to it
    Box::new(Point { x: 0.0, y: 0.0 })
}

fn main() {
    // (all the type annotations are superfluous)
    // Stack allocated variables
    let point: Point = origin();
    let rectangle: Rectangle = Rectangle {
        top_left: origin(),
        bottom_right: Point { x: 3.0, y: -4.0 }
    };

    // Heap allocated rectangle
    let boxed_rectangle: Box<Rectangle> = Box::new(Rectangle {
        top_left: origin(),
        bottom_right: Point { x: 3.0, y: -4.0 },
    });

    // The output of functions can be boxed
    let boxed_point: Box<Point> = Box::new(origin());

    // Double indirection
    let box_in_a_box: Box<Box<Point>> = Box::new(boxed_origin());

    println!("Point occupies {} bytes on the stack",
             mem::size_of_val(&point));
    println!("Rectangle occupies {} bytes on the stack",
             mem::size_of_val(&rectangle));

    // box size == pointer size
    println!("Boxed point occupies {} bytes on the stack",
             mem::size_of_val(&boxed_point));
    println!("Boxed rectangle occupies {} bytes on the stack",
             mem::size_of_val(&boxed_rectangle));
    println!("Boxed box occupies {} bytes on the stack",
             mem::size_of_val(&box_in_a_box));

    // Copy the data contained in `boxed_point` into `unboxed_point`
    let unboxed_point: Point = *boxed_point;
    println!("Unboxed point occupies {} bytes on the stack",
             mem::size_of_val(&unboxed_point));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Use Box for recursive types or large data you want on the heap.
- Deref lets you use Box<T> like &T.

_RBE source: `std/box.md`_

---

### 20.1.2 Vectors

**What it is**

Vectors are re-sizable arrays. Like slices, their size is not known at compile
time, but they can grow or shrink at any time. A vector is represented using
3 parameters:
- pointer to the data
- length
- capacity
The capacity indicates how much memory is reserved for the vector. The vector
can grow as long as the length is smaller than the capacity.

**JS mental model**

Vec<T> is a growable array — the real JS Array analogue.

**Rust example**

```rust
fn main() {
    // Iterators can be collected into vectors
    let collected_iterator: Vec<i32> = (0..10).collect();
    println!("Collected (0..10) into: {:?}", collected_iterator);

    // The `vec!` macro can be used to initialize a vector
    let mut xs = vec![1i32, 2, 3];
    println!("Initial vector: {:?}", xs);

    // Insert new element at the end of the vector
    println!("Push 4 into the vector");
    xs.push(4);
    println!("Vector: {:?}", xs);

    // Error! Immutable vectors can't grow
    collected_iterator.push(0);
    // FIXME ^ Comment out this line

    // The `len` method yields the number of elements currently stored in a vector
    println!("Vector length: {}", xs.len());

    // Indexing is done using the square brackets (indexing starts at 0)
    println!("Second element: {}", xs[1]);

    // `pop` removes the last element from the vector and returns it
    println!("Pop last element: {:?}", xs.pop());

    // Out of bounds indexing yields a panic
    println!("Fourth element: {}", xs[3]);
    // FIXME ^ Comment out this line

    // `Vector`s can be easily iterated over
    println!("Contents of xs:");
    for x in xs.iter() {
        println!("> {}", x);
    }

    // A `Vector` can also be iterated over while the iteration
    // count is enumerated in a separate variable (`i`)
    for (i, x) in xs.iter().enumerate() {
        println!("In position {} we have value {}", i, x);
    }

    // Thanks to `iter_mut`, mutable `Vector`s can also be iterated
    // over in a way that allows modifying each value
    for x in xs.iter_mut() {
        *x *= 3;
    }
    println!("Updated vector: {:?}", xs);
}
```

**JS equivalent**

```javascript
const v = []; v.push(1); v.push(2);
```

**Watch out**

- Indexing out of bounds panics.
- Prefer iter() over indexes when possible.
- vec! macro constructs quickly.

_RBE source: `std/vec.md`_

---

### 20.1.3 Strings

**What it is**

The two most used string types in Rust are `String` and `&str`. A `String` is stored as a vector of bytes (`Vec<u8>`), but guaranteed to
always be a valid UTF-8 sequence. `String` is heap allocated, growable and not
null terminated. `&str` is a slice (`&[u8]`) that always points to a valid UTF-8 sequence, and
can be used to view into a `String`, just like `&[T]` is a view into `Vec<T>`.

**JS mental model**

&str is a view into UTF-8 (like a string slice). String is owned growable UTF-8 (like a mutable string buffer). JS strings are closer to immutable UTF-16.

**Rust example**

```rust
fn main() {
    // (all the type annotations are superfluous)
    // A reference to a string allocated in read only memory
    let pangram: &'static str = "the quick brown fox jumps over the lazy dog";
    println!("Pangram: {}", pangram);

    // Iterate over words in reverse, no new string is allocated
    println!("Words in reverse");
    for word in pangram.split_whitespace().rev() {
        println!("> {}", word);
    }

    // Copy chars into a vector, sort and remove duplicates
    let mut chars: Vec<char> = pangram.chars().collect();
    chars.sort();
    chars.dedup();

    // Create an empty and growable `String`
    let mut string = String::new();
    for c in chars {
        // Insert a char at the end of string
        string.push(c);
        // Insert a string at the end of string
        string.push_str(", ");
    }

    // The trimmed string is a slice to the original string, hence no new
    // allocation is performed
    let chars_to_trim: &[char] = &[' ', ','];
    let trimmed_str: &str = string.trim_matches(chars_to_trim);
    println!("Used characters: {}", trimmed_str);

    // Heap allocate a string
    let alice = String::from("I like dogs");
    // Allocate new memory and store the modified string there
    let bob: String = alice.replace("dog", "cat");

    println!("Alice says: {}", alice);
    println!("Bob says: {}", bob);
}
```

```rust
use std::str;

fn main() {
    // Note that this is not actually a `&str`
    let bytestring: &[u8; 21] = b"this is a byte string";

    // Byte arrays don't have the `Display` trait, so printing them is a bit limited
    println!("A byte string: {:?}", bytestring);

    // Byte strings can have byte escapes...
    let escaped = b"\x52\x75\x73\x74 as bytes";
    // ...but no unicode escapes
    // let escaped = b"\u{211D} is not allowed";
    println!("Some escaped bytes: {:?}", escaped);


    // Raw byte strings work just like raw strings
    let raw_bytestring = br"\u{211D} is not escaped here";
    println!("{:?}", raw_bytestring);

    // Converting a byte array to `str` can fail
    if let Ok(my_str) = str::from_utf8(raw_bytestring) {
        println!("And the same as text: '{}'", my_str);
    }

    let _quotes = br#"You can also use "fancier" formatting, \
                    like with normal raw strings"#;

    // Byte strings don't have to be UTF-8
    let shift_jis = b"\x82\xe6\x82\xa8\x82\xb1\x82\xbb"; // "ようこそ" in SHIFT-JIS

    // But then they can't always be converted to `str`
    match str::from_utf8(shift_jis) {
        Ok(my_str) => println!("Conversion successful: '{}'", my_str),
        Err(e) => println!("Conversion failed: {:?}", e),
    };
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Indexing by byte can panic mid-char — use chars()/get.
- String + &str works; &str + &str needs to_owned/format!.

_RBE source: `std/str.md`_

---

### 20.1.4 Option

**What it is**

Sometimes it's desirable to catch the failure of some parts of a program
instead of calling `panic!`; this can be accomplished using the `Option` enum. The `Option<T>` enum has two variants:
* `None`, to indicate failure or lack of value, and
* `Some(value)`, a tuple struct that wraps a `value` with type `T`.

**JS mental model**

Option in std is the same enum you use everywhere — first-class nullable.

**Rust example**

```rust
// An integer division that doesn't `panic!`
fn checked_division(dividend: i32, divisor: i32) -> Option<i32> {
    if divisor == 0 {
        // Failure is represented as the `None` variant
        None
    } else {
        // Result is wrapped in a `Some` variant
        Some(dividend / divisor)
    }
}

// This function handles a division that may not succeed
fn try_division(dividend: i32, divisor: i32) {
    // `Option` values can be pattern matched, just like other enums
    match checked_division(dividend, divisor) {
        None => println!("{} / {} failed!", dividend, divisor),
        Some(quotient) => {
            println!("{} / {} = {}", dividend, divisor, quotient)
        },
    }
}

fn main() {
    try_division(4, 2);
    try_division(1, 0);

    // Binding `None` to a variable needs to be type annotated
    let none: Option<i32> = None;
    let _equivalent_none = None::<i32>;

    let optional_float = Some(0f32);

    // Unwrapping a `Some` variant will extract the value wrapped.
    println!("{:?} unwraps to {:?}", optional_float, optional_float.unwrap());

    // Unwrapping a `None` variant will `panic!`
    println!("{:?} unwraps to {:?}", none, none.unwrap());
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Methods: map, and_then, unwrap_or, ok_or, ...

_RBE source: `std/option.md`_

---

### 20.1.5 Result

**What it is**

We've seen that the `Option` enum can be used as a return value from functions
that may fail, where `None` can be returned to indicate failure. However,
sometimes it is important to express *why* an operation failed. To do this we
have the `Result` enum. The `Result<T, E>` enum has two variants:
* `Ok(value)` which indicates that the operation succeeded, and wraps the
  `value` returned by the operation.

**JS mental model**

Result in std is the standard success/error sum type.

**Rust example**

```rust
mod checked {
    // Mathematical "errors" we want to catch
    #[derive(Debug)]
    pub enum MathError {
        DivisionByZero,
        NonPositiveLogarithm,
        NegativeSquareRoot,
    }

    pub type MathResult = Result<f64, MathError>;

    pub fn div(x: f64, y: f64) -> MathResult {
        if y == 0.0 {
            // This operation would `fail`, instead let's return the reason of
            // the failure wrapped in `Err`
            Err(MathError::DivisionByZero)
        } else {
            // This operation is valid, return the result wrapped in `Ok`
            Ok(x / y)
        }
    }

    pub fn sqrt(x: f64) -> MathResult {
        if x < 0.0 {
            Err(MathError::NegativeSquareRoot)
        } else {
            Ok(x.sqrt())
        }
    }

    pub fn ln(x: f64) -> MathResult {
        if x <= 0.0 {
            Err(MathError::NonPositiveLogarithm)
        } else {
            Ok(x.ln())
        }
    }
}

// `op(x, y)` === `sqrt(ln(x / y))`
fn op(x: f64, y: f64) -> f64 {
    // This is a three level match pyramid!
    match checked::div(x, y) {
        Err(why) => panic!("{:?}", why),
        Ok(ratio) => match checked::ln(ratio) {
            Err(why) => panic!("{:?}", why),
            Ok(ln) => match checked::sqrt(ln) {
                Err(why) => panic!("{:?}", why),
                Ok(sqrt) => sqrt,
            },
        },
    }
}

fn main() {
    // Will this fail?
    println!("{}", op(1.0, 10.0));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Combine with From for ? conversions.

_RBE source: `std/result.md`_

---

#### 20.1.5.1 ?

**What it is**

Chaining results using match can get pretty untidy; luckily, the `?` operator
can be used to make things pretty again. `?` is used at the end of an expression
returning a `Result`, and is equivalent to a match expression, where the
`Err(err)` branch expands to an early `return Err(From::from(err))`, and the `Ok(ok)`
branch expands to an `ok` expression. Be sure to check the [documentation][docs],
as there are many methods to map/compose `Result`. [docs]: https://doc.rust-lang.org/std/result/index.html.

**JS mental model**

? works on Result in functions that return Result — main can return Result too in modern Rust.

**Rust example**

```rust
mod checked {
    #[derive(Debug)]
    enum MathError {
        DivisionByZero,
        NonPositiveLogarithm,
        NegativeSquareRoot,
    }

    type MathResult = Result<f64, MathError>;

    fn div(x: f64, y: f64) -> MathResult {
        if y == 0.0 {
            Err(MathError::DivisionByZero)
        } else {
            Ok(x / y)
        }
    }

    fn sqrt(x: f64) -> MathResult {
        if x < 0.0 {
            Err(MathError::NegativeSquareRoot)
        } else {
            Ok(x.sqrt())
        }
    }

    fn ln(x: f64) -> MathResult {
        if x <= 0.0 {
            Err(MathError::NonPositiveLogarithm)
        } else {
            Ok(x.ln())
        }
    }

    // Intermediate function
    fn op_(x: f64, y: f64) -> MathResult {
        // if `div` "fails", then `DivisionByZero` will be `return`ed
        let ratio = div(x, y)?;

        // if `ln` "fails", then `NonPositiveLogarithm` will be `return`ed
        let ln = ln(ratio)?;

        sqrt(ln)
    }

    pub fn op(x: f64, y: f64) {
        match op_(x, y) {
            Err(why) => panic!("{}", match why {
                MathError::NonPositiveLogarithm
                    => "logarithm of non-positive number",
                MathError::DivisionByZero
                    => "division by zero",
                MathError::NegativeSquareRoot
                    => "square root of negative number",
            }),
            Ok(value) => println!("{}", value),
        }
    }
}

fn main() {
    checked::op(1.0, 10.0);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- main -> Result<(), Box<dyn Error>> is a common pattern.

_RBE source: `std/result/question_mark.md`_

---

### 20.1.6 panic!

**What it is**

The `panic!` macro can be used to generate a panic and start unwinding
its stack. While unwinding, the runtime will take care of freeing all the
resources *owned* by the thread by calling the destructor of all its objects. Since we are dealing with programs with only one thread, `panic!` will cause the
program to report the panic message and exit. Let's check that `panic!` doesn't leak memory.

**JS mental model**

panic! as a library tool for unrecoverable situations; catch_unwind exists but is not like try/catch for normal flow.

**Rust example**

```rust
// Re-implementation of integer division (/)
fn division(dividend: i32, divisor: i32) -> i32 {
    if divisor == 0 {
        // Division by zero triggers a panic
        panic!("division by zero");
    } else {
        dividend / divisor
    }
}

// The `main` task
fn main() {
    // Heap allocated integer
    let _x = Box::new(0i32);

    // This operation will trigger a task failure
    division(3, 0);

    println!("This point won't be reached!");

    // `_x` should get destroyed at this point
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Do not use panic for expected I/O errors.

_RBE source: `std/panic.md`_

---

### 20.1.7 HashMap

**What it is**

Where vectors store values by an integer index, `HashMap`s store values by key. `HashMap` keys can be booleans, integers, strings,
or any other type that implements the `Eq` and `Hash` traits. More on this in the next section. Like vectors, `HashMap`s are growable, but HashMaps can also shrink themselves
when they have excess space.

**JS mental model**

HashMap<K,V> is like a Map or object-as-dict, but keys must be hashable+eq and ownership matters.

**Rust example**

```rust
use std::collections::HashMap;

fn call(number: &str) -> &str {
    match number {
        "798-1364" => "We're sorry, the call cannot be completed as dialed.
            Please hang up and try again.",
        "645-7689" => "Hello, this is Mr. Awesome's Pizza. My name is Fred.
            What can I get for you today?",
        _ => "Hi! Who is this again?"
    }
}

fn main() {
    let mut contacts = HashMap::new();

    contacts.insert("Daniel", "798-1364");
    contacts.insert("Ashley", "645-7689");
    contacts.insert("Katie", "435-8291");
    contacts.insert("Robert", "956-1745");

    // Takes a reference and returns Option<&V>
    match contacts.get(&"Daniel") {
        Some(&number) => println!("Calling Daniel: {}", call(number)),
        _ => println!("Don't have Daniel's number."),
    }

    // `HashMap::insert()` returns `None`
    // if the inserted value is new, `Some(value)` otherwise
    contacts.insert("Daniel", "164-6743");

    match contacts.get(&"Ashley") {
        Some(&number) => println!("Calling Ashley: {}", call(number)),
        _ => println!("Don't have Ashley's number."),
    }

    contacts.remove(&"Ashley");

    // `HashMap::iter()` returns an iterator that yields
    // (&'a key, &'a value) pairs in arbitrary order.
    for (contact, &number) in contacts.iter() {
        println!("Calling {}: {}", contact, call(number));
    }
}
```

**JS equivalent**

```javascript
const m = new Map(); m.set("a", 1);
```

**Watch out**

- entry API is excellent for insert-or-update.
- Default hasher is not cryptographic.

_RBE source: `std/hash.md`_

---

#### 20.1.7.1 Alternate/custom key types

**What it is**

You can use your own types as HashMap keys if they implement Eq and Hash. Unlike JS object keys (strings) or Map (reference equality for objects), Rust keys compare by value equality you define.

**JS mental model**

Custom keys need Eq + Hash — like defining value equality for Map keys in JS (which uses reference equality for objects).

**Rust example**

```rust
use std::collections::HashMap;

// Eq requires that you derive PartialEq on the type.
#[derive(PartialEq, Eq, Hash)]
struct Account<'a>{
    username: &'a str,
    password: &'a str,
}

struct AccountInfo<'a>{
    name: &'a str,
    email: &'a str,
}

type Accounts<'a> = HashMap<Account<'a>, AccountInfo<'a>>;

fn try_logon<'a>(accounts: &Accounts<'a>,
        username: &'a str, password: &'a str){
    println!("Username: {}", username);
    println!("Password: {}", password);
    println!("Attempting logon...");

    let logon = Account {
        username,
        password,
    };

    match accounts.get(&logon) {
        Some(account_info) => {
            println!("Successful logon!");
            println!("Name: {}", account_info.name);
            println!("Email: {}", account_info.email);
        },
        _ => println!("Login failed!"),
    }
}

fn main(){
    let mut accounts: Accounts = HashMap::new();

    let account = Account {
        username: "j.everyman",
        password: "password123",
    };

    let account_info = AccountInfo {
        name: "John Everyman",
        email: "j.everyman@email.com",
    };

    accounts.insert(account, account_info);

    try_logon(&accounts, "j.everyman", "psasword123");

    try_logon(&accounts, "j.everyman", "password123");
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Derive Hash/Eq when possible.
- Be consistent: equal keys must hash equal.

_RBE source: `std/hash/alt_key_types.md`_

---

#### 20.1.7.2 HashSet

**What it is**

HashSet stores unique values. Inserting a duplicate does nothing. Great for membership tests and deduplication.

**JS mental model**

HashSet<T> is a set — like JS Set.

**Rust example**

```rust
use std::collections::HashSet;

fn main() {
    let mut a: HashSet<i32> = vec![1i32, 2, 3].into_iter().collect();
    let mut b: HashSet<i32> = vec![2i32, 3, 4].into_iter().collect();

    assert!(a.insert(4));
    assert!(a.contains(&4));

    // `HashSet::insert()` returns false if
    // there was a value already present.
    assert!(b.insert(4), "Value 4 is already in set B!");
    // FIXME ^ Comment out this line

    b.insert(5);

    // If a collection's element type implements `Debug`,
    // then the collection implements `Debug`.
    // It usually prints its elements in the format `[elem1, elem2, ...]`
    println!("A: {:?}", a);
    println!("B: {:?}", b);

    // Print [1, 2, 3, 4, 5] in arbitrary order
    println!("Union: {:?}", a.union(&b).collect::<Vec<&i32>>());

    // This should print [1]
    println!("Difference: {:?}", a.difference(&b).collect::<Vec<&i32>>());

    // Print [2, 3, 4] in arbitrary order.
    println!("Intersection: {:?}", a.intersection(&b).collect::<Vec<&i32>>());

    // Print [1, 5]
    println!("Symmetric Difference: {:?}",
             a.symmetric_difference(&b).collect::<Vec<&i32>>());
}
```

**JS equivalent**

```javascript
const s = new Set([1,2,2]); // {1,2}
```

**Watch out**

- Built on HashMap.
- Union/intersection methods available.

_RBE source: `std/hash/hashset.md`_

---

### 20.1.8 Rc

**What it is**

When multiple ownership is needed, `Rc`(Reference Counting) can be used. `Rc`
keeps track of the number of the references which means the number of owners of
the value wrapped inside an `Rc`. Reference count of an `Rc` increases by 1 whenever an `Rc` is cloned, and
decreases by 1 whenever one cloned `Rc` is dropped out of the scope. When an
`Rc`'s reference count becomes zero (which means there are no remaining owners),
both the `Rc` and the value are all dropped.

**JS mental model**

Rc<T> is single-threaded reference counting — shared ownership like multiple JS references, but only on one thread.

**Rust example**

```rust
use std::rc::Rc;

fn main() {
    let rc_examples = "Rc examples".to_string();
    {
        println!("--- rc_a is created ---");

        let rc_a: Rc<String> = Rc::new(rc_examples);
        println!("Reference Count of rc_a: {}", Rc::strong_count(&rc_a));

        {
            println!("--- rc_a is cloned to rc_b ---");

            let rc_b: Rc<String> = Rc::clone(&rc_a);
            println!("Reference Count of rc_b: {}", Rc::strong_count(&rc_b));
            println!("Reference Count of rc_a: {}", Rc::strong_count(&rc_a));

            // Two `Rc`s are equal if their inner values are equal
            println!("rc_a and rc_b are equal: {}", rc_a.eq(&rc_b));

            // We can use methods of a value directly
            println!("Length of the value inside rc_a: {}", rc_a.len());
            println!("Value of rc_b: {}", rc_b);

            println!("--- rc_b is dropped out of scope ---");
        }

        println!("Reference Count of rc_a: {}", Rc::strong_count(&rc_a));

        println!("--- rc_a is dropped out of scope ---");
    }

    // Error! `rc_examples` already moved into `rc_a`
    // And when `rc_a` is dropped, `rc_examples` is dropped together
    // println!("rc_examples: {}", rc_examples);
    // TODO ^ Try uncommenting this line
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- clone() bumps the count (cheap pointer copy).
- RefCell often paired for interior mutability.
- Cycles leak without Weak.

_RBE source: `std/rc.md`_

---

### 20.1.9 Arc

**What it is**

When shared ownership between threads is needed, `Arc`(Atomically Reference
Counted) can be used. This struct, via the `Clone` implementation can create
a reference pointer for the location of a value in the memory heap while
increasing the reference counter. As it shares ownership between threads, when
the last reference pointer to a value is out of scope, the variable is dropped.

**JS mental model**

Arc<T> is atomic Rc — shared ownership across threads. Like sharing an object across workers with explicit sync.

**Rust example**

```rust
use std::time::Duration;
use std::sync::Arc;
use std::thread;

fn main() {
    // This variable declaration is where its value is specified.
    let apple = Arc::new("the same apple");

    for _ in 0..10 {
        // Here there is no value specification as it is a pointer to a
        // reference in the memory heap.
        let apple = Arc::clone(&apple);

        thread::spawn(move || {
            // As Arc was used, threads can be spawned using the value allocated
            // in the Arc variable pointer's location.
            println!("{:?}", apple);
        });
    }

    // Make sure all Arc instances are printed from spawned threads.
    thread::sleep(Duration::from_secs(1));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Combine with Mutex/RwLock for mutation.
- clone() is cheap; data is shared.

_RBE source: `std/arc.md`_

---

## 21. Std misc

_RBE chapter root maps here. Every nested leaf below has the full 5-part teaching block._

### 21.1 Std misc

**What it is**

Many other types are provided by the std library to support
things such as:
* Threads
* Channels
* File I/O
These expand beyond what the [primitives] provide. See also:
[primitives] and [the std library][std]
[primitives]: primitives.md
[std]: https://doc.rust-lang.org/std/.

**JS mental model**

Misc standard library: threads, channels, files, processes, args, FFI — Node fs/child_process/worker-ish territory.

**Rust example**

```rust
use std::thread;
use std::time::Duration;

fn main() {
    thread::spawn(|| {
        println!("hello from a thread");
    });
    thread::sleep(Duration::from_millis(50));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- APIs return Result for I/O.
- Prefer higher-level crates for production CLI/async sometimes.

_RBE source: `std_misc.md`_

---

### 21.1.1 Threads

**What it is**

Rust threads are OS threads. spawn runs a closure concurrently. Ownership rules plus Send/Sync traits prevent many data races at compile time.

**JS mental model**

std::thread::spawn is like worker threads but shares memory via Send/Sync types — not a free-for-all like mutating shared JS objects.

**Rust example**

```rust
use std::thread;

const NTHREADS: u32 = 10;

// This is the `main` thread
fn main() {
    // Make a vector to hold the children which are spawned.
    let mut children = vec![];

    for i in 0..NTHREADS {
        // Spin up another thread
        children.push(thread::spawn(move || {
            println!("this is thread number {}", i);
        }));
    }

    for child in children {
        // Wait for the thread to finish. Returns a result.
        let _ = child.join();
    }
}
```

**JS equivalent**

```javascript
// Node worker_threads — different model
// Shared memory needs Atomics; no borrow checker
```

**Watch out**

- Join handles wait for finish.
- Panics in threads do not crash main unless joined and handled.
- Data moved into spawn often needs move closures.

_RBE source: `std_misc/threads.md`_

---

#### 21.1.1.1 Testcase: map-reduce

**What it is**

Map-reduce here means: split input into chunks (map phase in parallel threads), then combine results (reduce) on the main thread. It shows threads + ownership of partial results.

**JS mental model**

Split work across threads then join results — classic map-reduce / parallel map pattern.

**Rust example**

```rust
use std::thread;

// This is the `main` thread
fn main() {

    // This is our data to process.
    // We will calculate the sum of all digits via a threaded map-reduce algorithm.
    // Each whitespace separated chunk will be handled in a different thread.
    //
    // TODO: see what happens to the output if you insert spaces!
    let data = "86967897737416471853297327050364959
11861322575564723963297542624962850
70856234701860851907960690014725639
38397966707106094172783238747669219
52380795257888236525459303330302837
58495327135744041048897885734297812
69920216438980873548808413720956532
16278424637452589860345374828574668";

    // Make a vector to hold the child-threads which we will spawn.
    let mut children = vec![];

    /*************************************************************************
     * "Map" phase
     *
     * Divide our data into segments, and apply initial processing
     ************************************************************************/

    // split our data into segments for individual calculation
    // each chunk will be a reference (&str) into the actual data
    let chunked_data = data.split_whitespace();

    // Iterate over the data segments.
    // .enumerate() adds the current loop index to whatever is iterated
    // the resulting tuple "(index, element)" is then immediately
    // "destructured" into two variables, "i" and "data_segment" with a
    // "destructuring assignment"
    for (i, data_segment) in chunked_data.enumerate() {
        println!("data segment {} is \"{}\"", i, data_segment);

        // Process each data segment in a separate thread
        //
        // spawn() returns a handle to the new thread,
        // which we MUST keep to access the returned value
        //
        // 'move || -> u32' is syntax for a closure that:
        // * takes no arguments ('||')
        // * takes ownership of its captured variables ('move') and
        // * returns an unsigned 32-bit integer ('-> u32')
        //
        // Rust is smart enough to infer the '-> u32' from
        // the closure itself so we could have left that out.
        //
        // TODO: try removing the 'move' and see what happens
        children.push(thread::spawn(move || -> u32 {
            // Calculate the intermediate sum of this segment:
            let result = data_segment
                        // iterate over the characters of our segment..
                        .chars()
                        // .. convert text-characters to their number value..
                        .map(|c| c.to_digit(10).expect("should be a digit"))
                        // .. and sum the resulting iterator of numbers
                        .sum();

            // println! locks stdout, so no text-interleaving occurs
            println!("processed segment {}, result={}", i, result);

            // "return" not needed, because Rust is an "expression language", the
            // last evaluated expression in each block is automatically its value.
            result

        }));
    }


    /*************************************************************************
     * "Reduce" phase
     *
     * Collect our intermediate results, and combine them into a final result
     ************************************************************************/

    // combine each thread's intermediate results into a single final sum.
    //
    // we use the "turbofish" ::<> to provide sum() with a type hint.
    //
    // TODO: try without the turbofish, by instead explicitly
    // specifying the type of final_result
    let final_result = children.into_iter().map(|c| c.join().unwrap()).sum::<u32>();

    println!("Final sum result: {}", final_result);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Chunk data, spawn, collect partials, merge.
- For CPU work consider rayon crate later.

_RBE source: `std_misc/threads/testcase_mapreduce.md`_

---

### 21.1.2 Channels

**What it is**

A channel has Sender and Receiver. send moves a value to another thread. This is safer mental model than sharing mutable memory — similar to posting messages between workers.

**JS mental model**

mpsc channels are like message passing / message ports between threads — send values instead of sharing memory.

**Rust example**

```rust
use std::sync::mpsc::{Sender, Receiver};
use std::sync::mpsc;
use std::thread;

static NTHREADS: i32 = 3;

fn main() {
    // Channels have two endpoints: the `Sender<T>` and the `Receiver<T>`,
    // where `T` is the type of the message to be transferred
    // (type annotation is superfluous)
    let (tx, rx): (Sender<i32>, Receiver<i32>) = mpsc::channel();
    let mut children = Vec::new();

    for id in 0..NTHREADS {
        // The sender endpoint can be copied
        let thread_tx = tx.clone();

        // Each thread will send its id via the channel
        let child = thread::spawn(move || {
            // The thread takes ownership over `thread_tx`
            // Each thread queues a message in the channel
            thread_tx.send(id).unwrap();

            // Sending is a non-blocking operation, the thread will continue
            // immediately after sending its message
            println!("thread {} finished", id);
        });

        children.push(child);
    }

    // Here, all the messages are collected
    let mut ids = Vec::with_capacity(NTHREADS as usize);
    for _ in 0..NTHREADS {
        // The `recv` method picks a message from the channel
        // `recv` will block the current thread if there are no messages available
        ids.push(rx.recv());
    }

    // Wait for the threads to complete any remaining work
    for child in children {
        child.join().expect("oops! the child thread panicked");
    }

    // Show the order in which the messages were sent
    println!("{:?}", ids);
}
```

**JS equivalent**

```javascript
// MessageChannel / worker parentPort.postMessage
```

**Watch out**

- tx.clone() for multiple producers.
- rx iterates until all tx dropped.
- Prefer channels to fight shared mutable state.

_RBE source: `std_misc/channels.md`_

---

### 21.1.3 Path

**What it is**

Path is to PathBuf as str is to String. Use Path for viewing path segments and PathBuf when you need to build/modify paths.

**JS mental model**

Path / PathBuf are OS paths — like path module in Node (path.join), distinguishing borrowed vs owned.

**Rust example**

```rust
use std::path::Path;

fn main() {
    // Create a `Path` from an `&'static str`
    let path = Path::new(".");

    // The `display` method returns a `Display`able structure
    let _display = path.display();

    // `join` merges a path with a byte container using the OS specific
    // separator, and returns a `PathBuf`
    let mut new_path = path.join("a").join("b");

    // `push` extends the `PathBuf` with a `&Path`
    new_path.push("c");
    new_path.push("myfile.tar.gz");

    // `set_file_name` updates the file name of the `PathBuf`
    new_path.set_file_name("package.tgz");

    // Convert the `PathBuf` into a string slice
    match new_path.to_str() {
        None => panic!("new path is not a valid UTF-8 sequence"),
        Some(s) => println!("new path is {}", s),
    }
}
```

**JS equivalent**

```javascript
const path = require('path');
path.join('a', 'b');
```

**Watch out**

- PathBuf owns; Path is a slice.
- Do not assume UTF-8 on all platforms — OsString exists.

_RBE source: `std_misc/path.md`_

---

### 21.1.4 File I/O

**What it is**

The `File` struct represents a file that has been opened (it wraps a file
descriptor), and gives read and/or write access to the underlying file. Since many things can go wrong when doing file I/O, all the `File` methods
return the `io::Result<T>` type, which is an alias for `Result<T, io::Error>`. This makes the failure of all I/O operations *explicit*. Thanks to this, the
programmer can see all the failure paths, and is encouraged to handle them in
a proactive manner.

**JS mental model**

std::fs::File is like fs.open Sync APIs — explicit Result errors.

**Rust example**

```rust
use std::fs::File;
use std::io::prelude::*;

fn main() -> std::io::Result<()> {
    let mut f = File::create("/tmp/rbe_demo.txt")?;
    f.write_all(b"hi")?;
    Ok(())
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Use BufReader/BufWriter for efficiency.
- Close happens on Drop (RAII).

_RBE source: `std_misc/file.md`_

---

#### 21.1.4.1 open

**What it is**

File::open(path) opens an existing file for reading. It returns Result<File>. On success you get a handle; on failure you get an error (not found, permissions, ...).

**JS mental model**

File::open reads existing files — like fs.readFile but with a handle.

**Rust example**

```rust
use std::fs::File;
use std::io::prelude::*;
use std::path::Path;

fn main() {
    // Create a path to the desired file
    let path = Path::new("hello.txt");
    let display = path.display();

    // Open the path in read-only mode, returns `io::Result<File>`
    let mut file = match File::open(&path) {
        Err(why) => panic!("couldn't open {}: {}", display, why),
        Ok(file) => file,
    };

    // Read the file contents into a string, returns `io::Result<usize>`
    let mut s = String::new();
    match file.read_to_string(&mut s) {
        Err(why) => panic!("couldn't read {}: {}", display, why),
        Ok(_) => print!("{} contains:\n{}", display, s),
    }

    // `file` goes out of scope, and the "hello.txt" file gets closed
}
```

**JS equivalent**

```javascript
const fs = require('fs');
fs.readFileSync('file.txt', 'utf8');
```

**Watch out**

- Returns Err if missing.
- Combine with Read trait methods.

_RBE source: `std_misc/file/open.md`_

---

#### 21.1.4.2 create

**What it is**

File::create(path) creates or truncates a file for writing. Be careful: existing content is wiped. For more control use OpenOptions.

**JS mental model**

File::create truncates/creates for writing — like opening with write flag.

**Rust example**

```rust
static LOREM_IPSUM: &str =
    "Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod
tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam,
quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo
consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse
cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non
proident, sunt in culpa qui officia deserunt mollit anim id est laborum.
";

use std::fs::File;
use std::io::prelude::*;
use std::path::Path;

fn main() {
    let path = Path::new("lorem_ipsum.txt");
    let display = path.display();

    // Open a file in write-only mode, returns `io::Result<File>`
    let mut file = match File::create(&path) {
        Err(why) => panic!("couldn't create {}: {}", display, why),
        Ok(file) => file,
    };

    // Write the `LOREM_IPSUM` string to `file`, returns `io::Result<()>`
    match file.write_all(LOREM_IPSUM.as_bytes()) {
        Err(why) => panic!("couldn't write to {}: {}", display, why),
        Ok(_) => println!("successfully wrote to {}", display),
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Overwrites existing content.
- Use OpenOptions for append/create_new.

_RBE source: `std_misc/file/create.md`_

---

#### 21.1.4.3 read_lines

**What it is**

To read lines efficiently, wrap a File in BufReader and call lines(). You get an iterator of Result<String> (I/O errors possible per line).

**JS mental model**

BufReader + lines() is like reading a file line by line in Node with a readline interface.

**Rust example**

```rust
use std::fs::File;
use std::io::{self, BufRead};
use std::path::Path;

fn main() {
    // File hosts.txt must exist in the current path
    if let Ok(lines) = read_lines("./hosts.txt") {
        // Consumes the iterator, returns an (Optional) String
        for line in lines.map_while(Result::ok) {
            println!("{}", line);
        }
    }
}

// The output is wrapped in a Result to allow matching on errors.
// Returns an Iterator to the Reader of the lines of the file.
fn read_lines<P>(filename: P) -> io::Result<io::Lines<io::BufReader<File>>>
where P: AsRef<Path>, {
    let file = File::open(filename)?;
    Ok(io::BufReader::new(file).lines())
}
```

```rust
use std::fs::read_to_string;

fn read_lines(filename: &str) -> Vec<String> {
    read_to_string(filename)
        .unwrap()  // panic on possible file-reading errors
        .lines()  // split the string into an iterator of string slices
        .map(String::from)  // make each slice into a string
        .collect()  // gather them together into a vector
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Each line is Result<String>.
- Handles buffering for you.

_RBE source: `std_misc/file/read_lines.md`_

---

### 21.1.5 Child processes

**What it is**

The `process::Output` struct represents the output of a finished child process,
and the `process::Command` struct is a process builder. (You are encouraged to try the previous example with an incorrect flag passed
to `rustc`).

**JS mental model**

std::process::Command is like child_process.spawn/execFile.

**Rust example**

```rust
use std::process::Command;

fn main() {
    let output = Command::new("rustc")
        .arg("--version")
        .output().unwrap_or_else(|e| {
            panic!("failed to execute process: {}", e)
    });

    if output.status.success() {
        let s = String::from_utf8_lossy(&output.stdout);

        print!("rustc succeeded and stdout was:\n{}", s);
    } else {
        let s = String::from_utf8_lossy(&output.stderr);

        print!("rustc failed and stderr was:\n{}", s);
    }
}
```

**JS equivalent**

```javascript
const { spawn } = require('child_process');
spawn('ls', ['-l']);
```

**Watch out**

- Check status.success().
- Prefer arg() list over shell strings to avoid injection.

_RBE source: `std_misc/process.md`_

---

#### 21.1.5.1 Pipes

**What it is**

You can connect a child process stdout/stderr/stdin as pipes. Read from child.stdout as a file-like Read. This mirrors Node pipes between processes.

**JS mental model**

Piping stdout/stdin to the parent is like spawn with stdio pipes.

**Rust example**

```rust
use std::io::prelude::*;
use std::process::{Command, Stdio};

static PANGRAM: &'static str =
"the quick brown fox jumps over the lazy dog\n";

fn main() {
    // Spawn the `wc` command
    let mut cmd = if cfg!(target_family = "windows") {
        let mut cmd = Command::new("powershell");
        cmd.arg("-Command").arg("$input | Measure-Object -Line -Word -Character");
        cmd
    } else {
        Command::new("wc")
    };
    let process = match cmd
                                .stdin(Stdio::piped())
                                .stdout(Stdio::piped())
                                .spawn() {
        Err(why) => panic!("couldn't spawn wc: {}", why),
        Ok(process) => process,
    };

    // Write a string to the `stdin` of `wc`.
    //
    // `stdin` has type `Option<ChildStdin>`, but since we know this instance
    // must have one, we can directly `unwrap` it.
    match process.stdin.unwrap().write_all(PANGRAM.as_bytes()) {
        Err(why) => panic!("couldn't write to wc stdin: {}", why),
        Ok(_) => println!("sent pangram to wc"),
    }

    // Because `stdin` does not live after the above calls, it is `drop`ed,
    // and the pipe is closed.
    //
    // This is very important, otherwise `wc` wouldn't start processing the
    // input we just sent.

    // The `stdout` field also has type `Option<ChildStdout>` so must be unwrapped.
    let mut s = String::new();
    match process.stdout.unwrap().read_to_string(&mut s) {
        Err(why) => panic!("couldn't read wc stdout: {}", why),
        Ok(_) => print!("wc responded with:\n{}", s),
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Stdio::piped() then take() handles.
- Deadlocks possible if you fill pipes without reading — same as Node.

_RBE source: `std_misc/process/pipe.md`_

---

#### 21.1.5.2 Wait

**What it is**

After spawning, wait for completion to get an exit status. Command::output runs and collects stdout/stderr into memory.

**JS mental model**

wait/output waits for the child to finish — like await childPromise.

**Rust example**

```rust
use std::process::Command;

fn main() {
    let mut child = Command::new("sleep").arg("5").spawn().unwrap();
    let _result = child.wait().unwrap();

    println!("reached end of main");
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- output() captures stdout/stderr.
- status codes are OS-specific.

_RBE source: `std_misc/process/wait.md`_

---

### 21.1.6 Filesystem Operations

**What it is**

The fs module offers one-shot helpers (read_to_string, write, copy, rename, remove_file, create_dir_all). They are simpler than managing File handles for small tasks.

**JS mental model**

std::fs helpers: read, write, copy, create_dir_all — like fs/promises.

**Rust example**

```rust
use std::fs;
use std::fs::{File, OpenOptions};
use std::io;
use std::io::prelude::*;
#[cfg(target_family = "unix")]
use std::os::unix;
#[cfg(target_family = "windows")]
use std::os::windows;
use std::path::Path;

// A simple implementation of `% cat path`
fn cat(path: &Path) -> io::Result<String> {
    let mut f = File::open(path)?;
    let mut s = String::new();
    match f.read_to_string(&mut s) {
        Ok(_) => Ok(s),
        Err(e) => Err(e),
    }
}

// A simple implementation of `% echo s > path`
fn echo(s: &str, path: &Path) -> io::Result<()> {
    let mut f = File::create(path)?;

    f.write_all(s.as_bytes())
}

// A simple implementation of `% touch path` (ignores existing files)
fn touch(path: &Path) -> io::Result<()> {
    match OpenOptions::new().create(true).write(true).open(path) {
        Ok(_) => Ok(()),
        Err(e) => Err(e),
    }
}

fn main() {
    println!("`mkdir a`");
    // Create a directory, returns `io::Result<()>`
    match fs::create_dir("a") {
        Err(why) => println!("! {:?}", why.kind()),
        Ok(_) => {},
    }

    println!("`echo hello > a/b.txt`");
    // The previous match can be simplified using the `unwrap_or_else` method
    echo("hello", &Path::new("a/b.txt")).unwrap_or_else(|why| {
        println!("! {:?}", why.kind());
    });

    println!("`mkdir -p a/c/d`");
    // Recursively create a directory, returns `io::Result<()>`
    fs::create_dir_all("a/c/d").unwrap_or_else(|why| {
        println!("! {:?}", why.kind());
    });

    println!("`touch a/c/e.txt`");
    touch(&Path::new("a/c/e.txt")).unwrap_or_else(|why| {
        println!("! {:?}", why.kind());
    });

    println!("`ln -s ../b.txt a/c/b.txt`");
    // Create a symbolic link, returns `io::Result<()>`
    #[cfg(target_family = "unix")] {
        unix::fs::symlink("../b.txt", "a/c/b.txt").unwrap_or_else(|why| {
            println!("! {:?}", why.kind());
        });
    }
    #[cfg(target_family = "windows")] {
        windows::fs::symlink_file("../b.txt", "a/c/b.txt").unwrap_or_else(|why| {
            println!("! {:?}", why.to_string());
        });
    }

    println!("`cat a/c/b.txt`");
    match cat(&Path::new("a/c/b.txt")) {
        Err(why) => println!("! {:?}", why.kind()),
        Ok(s) => println!("> {}", s),
    }

    println!("`ls a`");
    // Read the contents of a directory, returns `io::Result<Vec<Path>>`
    match fs::read_dir("a") {
        Err(why) => println!("! {:?}", why.kind()),
        Ok(paths) => for path in paths {
            println!("> {:?}", path.unwrap().path());
        },
    }

    println!("`rm a/c/e.txt`");
    // Remove a file, returns `io::Result<()>`
    fs::remove_file("a/c/e.txt").unwrap_or_else(|why| {
        println!("! {:?}", why.kind());
    });

    println!("`rmdir a/c/d`");
    // Remove an empty directory, returns `io::Result<()>`
    fs::remove_dir("a/c/d").unwrap_or_else(|why| {
        println!("! {:?}", why.kind());
    });
}
```

```rust
fn cat(path: &Path) -> io::Result<String> {
    let mut f = File::open(path)?;
    let mut s = String::new();
    f.read_to_string(&mut s)?;
    Ok(s)
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Almost everything returns Result.
- create_dir_all like mkdir -p.

_RBE source: `std_misc/fs.md`_

---

### 21.1.7 Program arguments

**What it is**

Standard Library
The command line arguments can be accessed using `std::env::args`, which
returns an iterator that yields a `String` for each argument:
Crates
Alternatively, there are numerous crates that can provide extra functionality
when creating command-line applications. One of the more popular command line
argument crates being [`clap`]. [`clap`]: https://rust-cli.github.io/book/tutorial/cli-args.html#parsing-cli-arguments-with-clap.

**JS mental model**

std::env::args() is like process.argv.

**Rust example**

```rust
use std::env;

fn main() {
    let args: Vec<String> = env::args().collect();

    // The first argument is the path that was used to call the program.
    println!("My path is {}.", args[0]);

    // The rest of the arguments are the passed command line parameters.
    // Call the program like this:
    //   $ ./args arg1 arg2
    println!("I got {:?} arguments: {:?}.", args.len() - 1, &args[1..]);
}
```

**JS equivalent**

```javascript
process.argv.slice(2)
```

**Watch out**

- First arg is the program path.
- For real CLIs use clap.

_RBE source: `std_misc/arg.md`_

---

#### 21.1.7.1 Argument parsing

**What it is**

You can pattern-match on collected args to implement a tiny CLI. This teaches argv handling; larger apps should use a parsing crate.

**JS mental model**

Manual argv matching with match/if — teaching tool; production uses clap/structopt-style parsers.

**Rust example**

```rust
use std::env;

fn increase(number: i32) {
    println!("{}", number + 1);
}

fn decrease(number: i32) {
    println!("{}", number - 1);
}

fn help() {
    println!("usage:
match_args <string>
    Check whether given string is the answer.
match_args {{increase|decrease}} <integer>
    Increase or decrease given integer by one.");
}

fn main() {
    let args: Vec<String> = env::args().collect();

    match args.len() {
        // no arguments passed
        1 => {
            println!("My name is 'match_args'. Try passing some arguments!");
        },
        // one argument passed
        2 => {
            match args[1].parse() {
                Ok(42) => println!("This is the answer!"),
                _ => println!("This is not the answer."),
            }
        },
        // one command and one argument passed
        3 => {
            let cmd = &args[1];
            let num = &args[2];
            // parse the number
            let number: i32 = match num.parse() {
                Ok(n) => {
                    n
                },
                Err(_) => {
                    eprintln!("error: second argument not an integer");
                    help();
                    return;
                },
            };
            // parse the command
            match &cmd[..] {
                "increase" => increase(number),
                "decrease" => decrease(number),
                _ => {
                    eprintln!("error: invalid command");
                    help();
                },
            }
        },
        // all the other cases
        _ => {
            // show a help message
            help();
        }
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Handle --help yourself if manual.
- Invalid args should print usage and exit.

_RBE source: `std_misc/arg/matching.md`_

---

### 21.1.8 Foreign Function Interface

**What it is**

Foreign Function Interface lets Rust call C functions (and expose C ABI). You declare extern "C" blocks and call inside unsafe. Types must match the C side precisely.

**JS mental model**

FFI calls C like Node native addons / ffi-napi — unsafe boundary.

**Rust example**

```rust
use std::fmt;

// this extern block links to the libm library
#[cfg(target_family = "windows")]
#[link(name = "msvcrt")]
extern {
    // this is a foreign function
    // that computes the square root of a single precision complex number
    fn csqrtf(z: Complex) -> Complex;

    fn ccosf(z: Complex) -> Complex;
}
#[cfg(target_family = "unix")]
#[link(name = "m")]
extern {
    // this is a foreign function
    // that computes the square root of a single precision complex number
    fn csqrtf(z: Complex) -> Complex;

    fn ccosf(z: Complex) -> Complex;
}

// Since calling foreign functions is considered unsafe,
// it's common to write safe wrappers around them.
fn cos(z: Complex) -> Complex {
    unsafe { ccosf(z) }
}

fn main() {
    // z = -1 + 0i
    let z = Complex { re: -1., im: 0. };

    // calling a foreign function is an unsafe operation
    let z_sqrt = unsafe { csqrtf(z) };

    println!("the square root of {:?} is {:?}", z, z_sqrt);

    // calling safe API wrapped around unsafe operation
    println!("cos({:?}) = {:?}", z, cos(z));
}

// Minimal implementation of single precision complex numbers
#[repr(C)]
#[derive(Clone, Copy)]
struct Complex {
    re: f32,
    im: f32,
}

impl fmt::Debug for Complex {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        if self.im < 0. {
            write!(f, "{}-{}i", self.re, -self.im)
        } else {
            write!(f, "{}+{}i", self.re, self.im)
        }
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Signatures must match exactly.
- Use libc / bindgen ecosystem.
- All FFI is unsafe.

_RBE source: `std_misc/ffi.md`_

---

## 22. Testing

_RBE chapter root maps here. Every nested leaf below has the full 5-part teaching block._

### 22.1 Testing

**What it is**

Rust is a programming language that cares a lot about correctness and it
includes support for writing software tests within the language itself. Testing comes in three styles:
* [Unit][unit] testing. * [Doc][doc] testing. * [Integration][integration] testing.

**JS mental model**

Rust tests are first-class: cargo test runs unit, integration, and doc tests.

**Rust example**

```rust
pub fn add(a: i32, b: i32) -> i32 { a + b }

#[cfg(test)]
mod tests {
    use super::*;
    #[test]
    fn it_works() {
        assert_eq!(add(2, 2), 4);
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Assertions: assert!, assert_eq!, assert_ne!.
- Tests are just functions with #[test].

_RBE source: `testing.md`_

---

### 22.1.1 Unit testing

**What it is**

Unit tests are #[test] functions, commonly inside a tests submodule gated by #[cfg(test)] so they only compile for cargo test. They can access private details of the parent module.

**JS mental model**

Unit tests often live next to code in #[cfg(test)] mod tests — like colocated jest tests.

**Rust example**

```rust
pub fn divide_non_zero_result(a: u32, b: u32) -> u32 {
    if b == 0 {
        panic!("Divide-by-zero error");
    } else if a < b {
        panic!("Divide result is zero");
    }
    a / b
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_divide() {
        assert_eq!(divide_non_zero_result(10, 2), 5);
    }

    #[test]
    #[should_panic]
    fn test_any_panic() {
        divide_non_zero_result(1, 0);
    }

    #[test]
    #[should_panic(expected = "Divide result is zero")]
    fn test_specific_panic() {
        divide_non_zero_result(1, 10);
    }

    #[test]
    #[should_panic = "Divide result is zero"] // This also works
    fn test_specific_panic_shorthand() {
        divide_non_zero_result(1, 10);
    }
}
```

```rust
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}

// This is a really bad adding function, its purpose is to fail in this
// example.
#[allow(dead_code)]
fn bad_add(a: i32, b: i32) -> i32 {
    a - b
}

#[cfg(test)]
mod tests {
    // Note this useful idiom: importing names from outer (for mod tests) scope.
    use super::*;

    #[test]
    fn test_add() {
        assert_eq!(add(1, 2), 3);
    }

    #[test]
    fn test_bad_add() {
        // This assert would fire and test will fail.
        // Please note, that private functions can be tested too!
        assert_eq!(bad_add(1, 2), 3);
    }
}
```

**JS equivalent**

```javascript
// Jest: test('adds', () => expect(add(1,2)).toBe(3));
```

**Watch out**

- Can test private items in the same module tree.
- Use assert_eq! for readable failures.

_RBE source: `testing/unit_testing.md`_

---

### 22.1.2 Documentation testing

**What it is**

Documentation tests take code samples from doc comments (triple-backtick rust fences inside /// comments) and run them during `cargo test`. This keeps examples honest. Prefer short, focused doc examples.

**JS mental model**

Examples in /// docs are compiled/run as tests — documentation that cannot rot silently.

**Rust example**

```rust
/// Add one to the given value.
///
/// # Example
///
/// Let v = add_one(41); assert that v equals 42.
pub fn add_one(x: i32) -> i32 {
    x + 1
}

#[cfg(test)]
mod tests {
    use super::*;
    #[test]
    fn docs_idea() {
        assert_eq!(add_one(41), 42);
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Add # hide lines for setup in doc examples.
- cargo test runs them.

_RBE source: `testing/doc_testing.md`_

---

### 22.1.3 Integration testing

**What it is**

Integration tests live in the tests/ directory at the package root. They link your library as an external crate, so they only test the public API — closer to black-box tests.

**JS mental model**

tests/*.rs are separate crates that only see your public API — like integration tests against a package.

**Rust example**

```rust
// importing common module.
mod common;

#[test]
fn test_add() {
    // using common code.
    common::setup();
    assert_eq!(adder::add(3, 2), 5);
}
```

```rust
pub fn setup() {
    // some setup code, like creating required files/directories, starting
    // servers, etc.
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Each file is its own crate.
- Share helpers via tests/common/mod.rs pattern.

_RBE source: `testing/integration_testing.md`_

---

### 22.1.4 Dev-dependencies

**What it is**

Dev-dependencies are crates you need for testing or examples but not for normal builds of your library/binary. List them under [dev-dependencies] in Cargo.toml.

**JS mental model**

[dev-dependencies] are like npm devDependencies — available for tests/examples/benches only.

**Rust example**

```rust
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}

#[cfg(test)]
mod tests {
    use super::*;
    use pretty_assertions::assert_eq; // crate for test-only use. Cannot be used in non-test code.

    #[test]
    fn test_add() {
        assert_eq!(add(2, 3), 5);
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Not shipped as runtime deps of your lib users.
- cargo add --dev pretty_assertions.

_RBE source: `testing/dev_dependencies.md`_

---

## 23. Unsafe operations

_RBE chapter root maps here. Every nested leaf below has the full 5-part teaching block._

### 23.1 Unsafe Operations

**What it is**

Unsafe Rust lets you do five extra things: dereference raw pointers, call unsafe functions, implement unsafe traits, access mutable statics, and access fields of unions. The rest of the language stays the same. Safety obligations move to you.

**JS mental model**

unsafe unlocks a few extra powers (raw pointers, FFI, mutable statics, unions) — you promise to uphold invariants the compiler cannot check.

**Rust example**

```rust
use std::slice;

fn main() {
    let some_vector = vec![1, 2, 3, 4];

    let pointer = some_vector.as_ptr();
    let length = some_vector.len();

    unsafe {
        let my_slice: &[u32] = slice::from_raw_parts(pointer, length);

        assert_eq!(some_vector.as_slice(), my_slice);
    }
}
```

```rust
fn main() {
    let raw_p: *const u32 = &10;

    unsafe {
        assert!(*raw_p == 10);
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Keep unsafe blocks small.
- Prefer safe wrappers.
- Never use unsafe to silence borrow errors casually.

_RBE source: `unsafe.md`_

---

### 23.1.1 Inline assembly

**What it is**

The asm! macro inserts assembly instructions into the compiler output. You specify template strings plus in/out registers. It is advanced, architecture-specific, and always unsafe. Most JS-coming developers will never need it — know it exists.

**JS mental model**

Inline assembly embeds CPU instructions — like writing a tiny asm block; almost never needed in app JS-land, sometimes in kernels/drivers.

**Rust example**

```rust
use std::arch::asm;

#[cfg(target_arch = "x86_64")]
fn main() {
    // three entries of four bytes each
    let mut name_buf = [0_u8; 12];
    // String is stored as ascii in ebx, edx, ecx in order
    // Because ebx is reserved, the asm needs to preserve the value of it.
    // So we push and pop it around the main asm.
    // 64 bit mode on 64 bit processors does not allow pushing/popping of
    // 32 bit registers (like ebx), so we have to use the extended rbx register instead.

    unsafe {
        asm!(
            "push rbx",
            "cpuid",
            "mov [rdi], ebx",
            "mov [rdi + 4], edx",
            "mov [rdi + 8], ecx",
            "pop rbx",
            // We use a pointer to an array for storing the values to simplify
            // the Rust code at the cost of a couple more asm instructions
            // This is more explicit with how the asm works however, as opposed
            // to explicit register outputs such as `out("ecx") val`
            // The *pointer itself* is only an input even though it's written behind
            in("rdi") name_buf.as_mut_ptr(),
            // select cpuid 0, also specify eax as clobbered
            inout("eax") 0 => _,
            // cpuid clobbers these registers too
            out("ecx") _,
            out("edx") _,
        );
    }

    let name = core::str::from_utf8(&name_buf).unwrap();
    println!("CPU Manufacturer ID: {}", name);
}

#[cfg(not(target_arch = "x86_64"))]
fn main() {}
```

```rust
#[cfg(target_arch = "x86_64")] {
use std::arch::asm;

extern "C" fn foo(arg: i32) -> i32 {
    println!("arg = {}", arg);
    arg * 2
}

fn call_foo(arg: i32) -> i32 {
    unsafe {
        let result;
        asm!(
            "call {}",
            // Function pointer to call
            in(reg) foo,
            // 1st argument in rdi
            in("rdi") arg,
            // Return value in rax
            out("rax") result,
            // Mark all registers which are not preserved by the "C" calling
            // convention as clobbered.
            clobber_abi("C"),
        );
        result
    }
}
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Always inside unsafe.
- Architecture-specific.
- Easy to invent UB — avoid unless you know why.

_RBE source: `unsafe/asm.md`_

---

## 24. Compatibility & meta

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

## 25. Zero-to-hero practice path / checklist

Work top to bottom. Check items off as you build tiny programs.

### Phase A — Survive the compiler (1–3 days)

- [ ] Install rustup, `cargo new`, `cargo run`
- [ ] `println!` formatting / Debug / Display
- [ ] Primitives, tuples, arrays, slices
- [ ] Structs + enums + `match`
- [ ] `let` / `mut` / shadowing

### Phase B — Own your memory (3–7 days) **critical**

- [ ] Explain move vs copy vs borrow in your own words
- [ ] Write functions taking `&T` and `&mut T`
- [ ] Fix 10 borrow-checker errors without cloning first
- [ ] Struct that holds a reference (explicit lifetime)
- [ ] Use `Option` instead of null thinking

### Phase C — Real programs (1–2 weeks)

- [ ] Modules + `pub` visibility across files
- [ ] Add a crates.io dependency (`serde`, `rand`, or `clap`)
- [ ] `Result` + `?` error handling end-to-end
- [ ] Read/write files; parse CLI args
- [ ] Unit tests + `cargo test`

### Phase D — Idiomatic Rust (ongoing)

- [ ] Traits + derive + `impl Trait`
- [ ] Iterators / closures HOFs (no manual index loops)
- [ ] Generics with bounds
- [ ] `HashMap` / `Vec` / `String` fluency
- [ ] Threads or async (`tokio`) at least once

### Phase E — Advanced RBE topics

- [ ] Declarative macros for a small DSL
- [ ] `Rc`/`RefCell` or `Arc`/`Mutex` pattern
- [ ] FFI hello-world (optional)
- [ ] Read unsafe code; write almost none

### Mini project ideas (JS → Rust)

1. Port a small utility script (CSV filter, JSON transform).
2. CLI todo app (`clap` + file storage).
3. HTTP JSON client (`reqwest`) replacing `fetch`/`axios`.
4. Rewrite a hot Node path as a Wasm module (`wasm-bindgen`).

### Keep learning

- Official twin: [Rust by Example](https://doc.rust-lang.org/rust-by-example/)
- Deep dive: [The Rust Book](https://doc.rust-lang.org/book/)
- API search: [docs.rs](https://docs.rs) / `rustup doc --std`

---

## 26. Quick JS↔Rust cheatsheet appendix

### Syntax & everyday APIs

| Task | JavaScript | Rust |
|---|---|---|
| Print | `console.log(x)` | `println!("{:?}", x)` |
| Variable | `let x = 1` | `let mut x = 1;` |
| Constant binding | `const x = 1` | `let x = 1;` |
| Function | `function f(a) {}` / `=>` | `fn f(a: i32) -> i32 {}` |
| Anonymous fn | `(x) => x + 1` | `|x| x + 1` |
| Array | `[1,2,3]` | `vec![1,2,3]` or `[1,2,3]` |
| Object | `{a: 1}` | `struct` / field struct lit |
| Class method | `obj.method()` | `impl` + `obj.method()` |
| Interface | `interface` | `trait` |
| null | `null`/`undefined` | `Option::None` |
| throw/catch | `try/catch` | `Result` + `?` / `match` |
| Import | `import x from 'y'` | `use y::x;` |
| Export | `export` | `pub` |
| Package.json | `dependencies` | `Cargo.toml [dependencies]` |
| Install pkg | `npm i lodash` | `cargo add lodash` (or serde etc.) |
| Run | `node app.js` | `cargo run` |
| Test | `jest` | `cargo test` |
| String concat | `a + b` / `` `${a}` `` | `format!("{}{}", a, b)` |
| Map | `arr.map` | `iter().map()` |
| Filter | `arr.filter` | `iter().filter()` |
| Length | `arr.length` | `arr.len()` |
| Equality | `===` | `==` (via `PartialEq`) |

### Types

| JS | Rust |
|---|---|
| `number` | `i32`/`u32`/`f64`/… |
| `string` | `String` / `&str` |
| `boolean` | `bool` |
| `Array` | `Vec<T>` |
| `Map` | `HashMap<K,V>` |
| `Set` | `HashSet<T>` |
| `Promise<T>` | `Future` / `Result` (sync errors) |
| `any` | avoid; `serde_json::Value` if needed |
| union `"a"|"b"` | `enum` |

### Ownership pocket card

```text
Owned T  ──move──►  new owner (old name invalid)
Owned T  ──&T────►  shared borrow (many OK)
Owned T  ──&mut T─► exclusive borrow (one OK)
Copy types (i32, bool, …) copy instead of move
Clone types can .clone() for an explicit deep(er) copy
```

### Error pocket card

```text
Option<T>   Some(v) | None      → absence
Result<T,E> Ok(v)   | Err(e)    → failure
x?          early-return None/Err from function
unwrap()    panic on None/Err (prototyping only)
```

### Reading compiler errors

1. Read the **top** error first.
2. Follow “move occurs here” / “borrowed here”.
3. Apply the smallest fix: change ownership, add `&`, extend lifetime, or clone intentionally.
4. Re-run `cargo check` (faster than full `run`).

---

## Closing

You now have a **leaf-complete** Rust-by-Example path written for JavaScript developers.
When stuck: read the compiler error, skim the matching RBE page, then re-read the **Watch out** bullets here.

Official twin: https://doc.rust-lang.org/rust-by-example/
