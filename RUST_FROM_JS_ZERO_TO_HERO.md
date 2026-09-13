# Rust from JavaScript: Zero to Hero

> A complete, super-simple tutorial for JavaScript (ES6+) developers learning Rust from scratch.
> Curriculum aligned with [Rust by Example](https://doc.rust-lang.org/rust-by-example/) (RBE). Edition 2021+ idioms.

---

## Table of contents

1. [Welcome & how to use this guide](#1-welcome--how-to-use-this-guide)
2. [Install Rust + first `cargo new`](#2-install-rust--first-cargo-new-map-npmnode)
3. [Hello World, comments & formatting](#3-hello-world--comments--formatting)
4. [Primitives](#4-primitives)
5. [Custom types: struct, enum, constants](#5-custom-types-struct-enum-constants)
6. [Variable bindings, mutability, scope, shadowing](#6-variable-bindings-mutability-scope-shadowing)
7. [Types, casting, inference, aliases](#7-types-casting-inference-aliases)
8. [Conversion](#8-conversion)
9. [Expressions](#9-expressions)
10. [Flow of Control](#10-flow-of-control)
11. [Functions, methods, closures, HOFs](#11-functions-methods-closures-hofs)
12. [Modules & visibility](#12-modules--visibility)
13. [Crates & Cargo](#13-crates--cargo)
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
24. [Compatibility & meta](#24-compatibility--meta)
25. [Zero-to-hero practice path](#25-zero-to-hero-practice-path--checklist)
26. [Quick JS↔Rust cheatsheet](#26-quick-jsrust-cheatsheet-appendix)

---

## 1. Welcome & how to use this guide

### Plain English

Rust is a systems language that gives you **speed like C/C++** and **memory safety without a garbage collector**. JavaScript runs with a GC and is dynamically typed. Rust is **statically typed**, **compiled**, and enforces rules at compile time so many entire classes of bugs never ship.

This guide follows every major chapter of **Rust by Example**. For each idea you get:

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

## 3. Hello World & comments & formatting

### 3.1 Hello World

#### Plain English

Every binary starts at `fn main()`. `println!` prints a line. The `!` means it is a **macro**, not a normal function.

#### JS mental model

`println!` ≈ `console.log`, but with compile-time format checking.

```rust
fn main() {
    println!("Hello, world!");
}
```

```js
console.log("Hello, world!");
```

### 3.2 Comments

RBE covers regular comments and documentation comments.

```rust
fn main() {
    // Line comment

    /*
     * Block comment (can nest: /* inner */)
     */
}

/// Doc comment for the following item (markdown).
/// Generates HTML docs with `cargo doc`.
fn documented() {}

//! Doc comment for the enclosing item (crate/module).
```

| Rust | JS |
|---|---|
| `//` | `//` |
| `/* */` (nestable) | `/* */` (not nestable) |
| `///` / `//!` | JSDoc `/** */` (similar idea) |

### 3.3 Formatted print (`println!` family)

#### Plain English

Format strings use `{}` placeholders. Arguments are checked at **compile time** — wrong types/count = error before you run.

```rust
fn main() {
    println!("{} days", 31);
    println!("{0}, this is {1}. {1}, this is {0}", "Alice", "Bob");
    println!("{subject} {verb} {object}",
             object = "the lazy dog",
             subject = "the quick brown fox",
             verb = "jumps over");

    println!("{} of {:b} people know binary, the other half doesn't", 1, 2);

    println!("{number:>5}", number = 1);   // pad left
    println!("{number:0>5}", number = 1);  // pad with zeros
    println!("{number:0<5}", number = 1);  // pad right

    println!("Pi is roughly {pi:.3}", pi = 3.141592);
}
```

```js
console.log(`${31} days`);
console.log("Alice, this is Bob. Bob, this is Alice");
```

Common macros:

| Macro | Meaning |
|---|---|
| `println!` | print + newline |
| `print!` | print, no newline |
| `eprintln!` | stderr + newline |
| `format!` | return a `String` (like template → string) |
| `dbg!` | print expression + file/line for debugging |

### 3.4 Debug vs Display

#### Plain English

- `Display` (`{}`) = user-facing pretty text (like a careful `toString()`).
- `Debug` (`{:?}`) = developer dump (like `util.inspect`).
- `{:#?}` = pretty Debug.

Many types only implement Debug via `#[derive(Debug)]`.

```rust
#[derive(Debug)]
struct Point {
    x: i32,
    y: i32,
}

fn main() {
    let p = Point { x: 1, y: 2 };
    println!("{:?}", p);   // Point { x: 1, y: 2 }
    println!("{:#?}", p);  // pretty
}
```

Custom `Display`:

```rust
use std::fmt;

struct Point {
    x: i32,
    y: i32,
}

impl fmt::Display for Point {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        write!(f, "({}, {})", self.x, self.y)
    }
}

fn main() {
    println!("{}", Point { x: 3, y: 4 }); // (3, 4)
}
```

#### Testcase: List (Display for a list)

```rust
use std::fmt;

struct List(Vec<i32>);

impl fmt::Display for List {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        let vec = &self.0;
        write!(f, "[")?;
        for (count, v) in vec.iter().enumerate() {
            if count != 0 {
                write!(f, ", ")?;
            }
            write!(f, "{}: {}", count, v)?;
        }
        write!(f, "]")
    }
}

fn main() {
    println!("{}", List(vec![1, 2, 3]));
}
```

### 3.5 Formatting cheatsheet

| Spec | Meaning |
|---|---|
| `{}` | Display |
| `{:?}` | Debug |
| `{:#?}` | Pretty Debug |
| `{:b}` `{:o}` `{:x}` `{:X}` | binary / octal / hex |
| `{:>5}` | right-align width 5 |
| `{:.3}` | 3 decimal places |

### Pitfalls

- Forgetting `!` on `println` → “expected function” confusion.
- Trying `console.log(obj)` style without `Debug`/`Display` → compile error (good!).
- Prefer `println!` while learning; buffering/flushing can surprise you with bare `print!`.


---

## 4. Primitives

### Plain English

Rust has a fixed set of **scalar** and **compound** primitive types. Unlike JS’s single `number`, integers have explicit sizes and signedness.

### JS mental model

| JS | Rust |
|---|---|
| `number` (IEEE-754 float64) | `f32` / `f64` **or** integer types |
| `bigint` | `i128` / `u128` (fixed) or crates for arbitrary size |
| `boolean` | `bool` |
| `string` | `char` + `str` / `String` (UTF-8) |
| arrays | `[T; N]` fixed, or `Vec<T>` growable |
| no real tuple | `(T, U, …)` tuples |

### 4.1 Scalar types overview

```rust
fn main() {
    let a: i32 = -10;      // signed 32-bit
    let b: u64 = 10;       // unsigned 64-bit
    let c: f64 = 2.0;      // default float type
    let d: bool = true;
    let e: char = '🦀';    // Unicode scalar value (4 bytes)
}
```

Integer types: `i8 i16 i32 i64 i128 isize` and `u8 u16 u32 u64 u128 usize`.
`isize`/`usize` = pointer-sized — used for indexing.

### 4.2 Literals and operators

```rust
fn main() {
    println!("1 + 2 = {}", 1u32 + 2);
    println!("1 - 2 = {}", 1i32 - 2);
    println!("1e4 is {}, -2.5e-3 is {}", 1e4, -2.5e-3);

    println!("true AND false is {}", true && false);
    println!("true OR false is {}", true || false);
    println!("NOT true is {}", !true);

    println!("0011 AND 0101 is {:04b}", 0b0011u32 & 0b0101);
    println!("0011 OR 0101 is {:04b}", 0b0011u32 | 0b0101);
    println!("0011 XOR 0101 is {:04b}", 0b0011u32 ^ 0b0101);
    println!("1 << 5 is {}", 1u32 << 5);
    println!("0x80 >> 2 is 0x{:x}", 0x80u32 >> 2);

    println!("One million is {}", 1_000_000u32);
}
```

```js
1 + 2;
true && false;
0b0011 & 0b0101;
1_000_000; // also valid in modern JS
```

### 4.3 Tuples

#### Plain English

A tuple groups values of **possibly different types**. Fixed length. Access with `.0`, `.1`, or destructuring.

```rust
fn reverse(pair: (i32, bool)) -> (bool, i32) {
    let (integer, boolean) = pair;
    (boolean, integer)
}

fn main() {
    let long_tuple = (
        1u8, 2u16, 3u32, 4u64, -1i8, -2i16, -3i32, -4i64, 0.1f32, 0.2f64, 'a', true,
    );

    println!("Long tuple first value: {}", long_tuple.0);
    println!("pair reversed: {:?}", reverse((1, true)));

    let _one = (5u32,); // one-element tuple needs trailing comma
}
```

```js
const pair = [1, true]; // no type/length guarantee
const [a, b] = pair;
```

### 4.4 Arrays and slices

#### Plain English

- Array: `[T; N]` — fixed length, same type.
- Slice: `&[T]` — a view into a contiguous sequence (array or `Vec`).

```rust
fn analyze_slice(slice: &[i32]) {
    println!("first: {}, len: {}", slice[0], slice.len());
}

fn main() {
    let xs: [i32; 5] = [1, 2, 3, 4, 5];
    let ys = [0; 500]; // 500 zeros

    println!("xs len = {}", xs.len());
    println!("array occupies {} bytes", std::mem::size_of_val(&xs));

    analyze_slice(&xs);
    analyze_slice(&ys[1..4]);

    // xs[5] would panic — not silent undefined
}
```

```js
const xs = [1, 2, 3, 4, 5];
xs.slice(1, 4);
xs[5]; // undefined — Rust panics instead
```

### Pitfalls

- JS `number` math is float; Rust integer `1/2` is **0**. Use `1.0/2.0` for floats.
- Indexing past the end **panics**.
- `char` ≠ JS string of length 1. Use `String` for text.
- Arrays do not grow — use `Vec` for push/pop.

---

## 5. Custom types: struct, enum, constants

### Plain English

Build richer data with **structs**, **enums** (tagged unions), and **constants**.

### 5.1 Structures

#### JS mental model

Typed object + class fields, but **no prototype inheritance**. Behavior lives in `impl` blocks.

```rust
#[derive(Debug)]
struct Person {
    name: String,
    age: u8,
}

struct Point(i32, i32); // tuple struct
struct Unit;            // unit struct

struct Point3D {
    x: i32,
    y: i32,
    z: i32,
}

fn main() {
    let name = String::from("Peter");
    let age = 27;
    let peter = Person { name, age };

    let point = Point3D { x: 0, y: 1, z: 2 };
    let _bottom = Point3D { x: 0, z: 0, ..point };

    println!("{:?}", peter);
    let Point3D { x, y, z } = point;
    println!("x={} y={} z={}", x, y, z);
}
```

```js
const peter = { name: "Peter", age: 27 };
const point = { x: 0, y: 1, z: 2 };
const bottom = { ...point, x: 0, z: 0 };
```

### 5.2 Enums

#### Plain English

A Rust enum is a **sum type**: one value among several variants. Variants can carry data. Replaces string unions, discriminated unions, and many flag patterns.

```rust
enum WebEvent {
    PageLoad,
    PageUnload,
    KeyPress(char),
    Paste(String),
    Click { x: i64, y: i64 },
}

fn inspect(event: WebEvent) {
    match event {
        WebEvent::PageLoad => println!("page loaded"),
        WebEvent::PageUnload => println!("page unloaded"),
        WebEvent::KeyPress(c) => println!("pressed '{}'", c),
        WebEvent::Paste(s) => println!("pasted \"{}\"", s),
        WebEvent::Click { x, y } => println!("clicked at x={}, y={}", x, y),
    }
}

fn main() {
    inspect(WebEvent::PageLoad);
    inspect(WebEvent::KeyPress('q'));
    inspect(WebEvent::Click { x: 20, y: 80 });
}
```

```js
// TS discriminated union:
// type WebEvent =
//   | { type: 'PageLoad' }
//   | { type: 'KeyPress'; char: string }
//   | { type: 'Click'; x: number; y: number };
```

#### Type aliases

```rust
enum VeryVerboseEnumOfThingsToDoWithNumbers {
    Add,
    Subtract,
}
type Operations = VeryVerboseEnumOfThingsToDoWithNumbers;

fn main() {
    let _x = Operations::Add;
}
```

### 5.3 `use` for enums

```rust
enum Status {
    Rich,
    Poor,
}
enum Work {
    Civilian,
    Soldier,
}

fn main() {
    use Status::{Poor, Rich};
    use Work::*;

    let status = Poor;
    let work = Civilian;
    match status {
        Rich => println!("The rich have lots of money!"),
        Poor => println!("The poor have no money..."),
    }
    match work {
        Civilian => println!("Civilian work!"),
        Soldier => println!("Soldier work!"),
    }
}
```

### 5.4 C-like enums

```rust
enum Number {
    Zero,
    One,
    Two,
}
enum Color {
    Red = 0xff0000,
    Green = 0x00ff00,
    Blue = 0x0000ff,
}

fn main() {
    println!("zero is {}", Number::Zero as i32);
    println!("roses are #{:06x}", Color::Red as i32);
}
```

### 5.5 Testcase: linked-list with enum

```rust
use List::*;

enum List {
    Cons(u32, Box<List>),
    Nil,
}

impl List {
    fn new() -> List {
        Nil
    }

    fn prepend(self, elem: u32) -> List {
        Cons(elem, Box::new(self))
    }

    fn len(&self) -> u32 {
        match self {
            Cons(_, tail) => 1 + tail.len(),
            Nil => 0,
        }
    }

    fn stringify(&self) -> String {
        match self {
            Cons(head, tail) => format!("{}, {}", head, tail.stringify()),
            Nil => "Nil".to_string(),
        }
    }
}

fn main() {
    let list = List::new().prepend(1).prepend(2).prepend(3);
    println!("len: {}", list.len());
    println!("{}", list.stringify());
}
```

### 5.6 Constants

#### Plain English

- `const`: inlined compile-time constant.
- `static`: fixed address; `'static` lifetime. Mutable statics need `unsafe`.

```rust
const THRESHOLD: i32 = 10;
static LANGUAGE: &str = "Rust";

fn is_big(n: i32) -> bool {
    n > THRESHOLD
}

fn main() {
    let n = 16;
    println!("This is {}", LANGUAGE);
    println!("{} is {}", n, if is_big(n) { "big" } else { "small" });
}
```

```js
const THRESHOLD = 10; // runtime binding; not a true compile-time constant
```

### Pitfalls

- Struct fields are **private by default** outside their module.
- Prefer data-carrying enum variants over boolean soup.
- `const` names use `SCREAMING_SNAKE` by convention.
- Do not confuse `const` with `let` immutability.

---

## 6. Variable bindings, mutability, scope, shadowing

### Plain English

Bindings use `let`. Immutable by default. Add `mut` to allow reassignment / interior mutation of the binding. Shadowing lets you re-`let` the same name.

### JS mental model

| JS | Rust |
|---|---|
| `const x = 1` | `let x = 1` (immutable binding) |
| `let x = 1` (reassignable) | `let mut x = 1` |
| `var` (function scope) | **Avoid the mental model** — Rust is block-scoped |
| Redeclare with `let` in same scope | Not allowed — but **shadowing** with new `let` is OK |

### 6.1 Bindings

```rust
fn main() {
    let an_integer = 1u32;
    let a_boolean = true;
    let unit = ();

    let copied_integer = an_integer;
    println!("An integer: {:?}", copied_integer);
    println!("A boolean: {:?}", a_boolean);
    println!("Meet the unit value: {:?}", unit);

    let _unused = 3u32; // underscore prefix silences unused warning
}
```

### 6.2 Mutability

```rust
fn main() {
    let _immutable_binding = 1;
    let mut mutable_binding = 1;

    println!("Before mutation: {}", mutable_binding);
    mutable_binding += 1;
    println!("After mutation: {}", mutable_binding);

    // _immutable_binding += 1; // ERROR
}
```

```js
let mutable = 1;
mutable += 1;
const immutable = 1;
// immutable += 1; // TypeError
```

### 6.3 Scope and shadowing

```rust
fn main() {
    let long_lived = 1;
    {
        let short_lived = 2;
        println!("inner short: {}", short_lived);

        let long_lived = 5_f32; // shadows outer
        println!("inner long: {}", long_lived);
    }
    // short_lived gone here
    println!("outer long: {}", long_lived);

    let long_lived = 'a'; // shadow again in same function
    println!("outer long shadowed: {}", long_lived);
}
```

### 6.4 Declare first

```rust
fn main() {
    let a_binding;
    {
        let x = 2;
        a_binding = x * x;
    }
    println!("a binding: {}", a_binding);

    let another_binding;
    // println!("{}", another_binding); // ERROR: possibly uninitialized
    another_binding = 1;
    println!("another: {}", another_binding);
}
```

### 6.5 Freezing

When you borrow immutably, you cannot mutate through the original until the borrow ends:

```rust
fn main() {
    let mut _mutable = 1;
    {
        let _borrow = &_mutable; // freezes
        // _mutable = 2; // ERROR while borrow is active
        println!("{}", _borrow);
    }
    _mutable = 3; // OK again
}
```

### Pitfalls

- `let` is not JS `let`. Default is immutable.
- Shadowing ≠ mutation. `let x = x + 1;` creates a **new** binding (can even change type).
- Using a variable before assigning is a compile error (TDZ-like, but stricter).

---

## 7. Types, casting, inference, aliases

### Plain English

Rust is statically typed. The compiler often **infers** types. You cast with `as`. You can name aliases with `type`.

### 7.1 Casting

No implicit numeric coercion (unlike JS). Use `as`.

```rust
fn main() {
    let decimal = 65.4321_f32;
    // let integer: u8 = decimal; // ERROR: no implicit conversion
    let integer = decimal as u8;
    let character = integer as char;

    println!("Casting: {} -> {} -> {}", decimal, integer, character);

    println!("1000 as u16: {}", 1000u32 as u16);
    println!("1000 as u8: {}", 1000u32 as u8); // truncation: 232
    println!("-1i8 as u8: {}", (-1i8) as u8);  // 255
}
```

```js
Number("65.4"); // JS coerces freely and silently — Rust rarely does
```

### 7.2 Literals

```rust
fn main() {
    let x = 1u8;
    let y = 2u32;
    let z = 3f32;
    let i = 1;    // i32 by default for integers (usually)
    let f = 1.0;  // f64 by default

    println!("size of `x` in bytes: {}", std::mem::size_of_val(&x));
    println!("size of `y` in bytes: {}", std::mem::size_of_val(&y));
    println!("size of `z` in bytes: {}", std::mem::size_of_val(&z));
    println!("size of `i` in bytes: {}", std::mem::size_of_val(&i));
    println!("size of `f` in bytes: {}", std::mem::size_of_val(&f));
}
```

### 7.3 Inference

```rust
fn main() {
    let elem = 5u8;
    let mut vec = Vec::new(); // type unknown yet
    vec.push(elem);           // now Vec<u8>
    println!("{:?}", vec);
}
```

### 7.4 Aliasing

```rust
type NanoSecond = u64;
type Inch = u64;

fn main() {
    let nanoseconds: NanoSecond = 1;
    let inches: Inch = 2;
    // aliases are NOT new types — this adds!
    println!("{} nanoseconds + {} inches = {}", nanoseconds, inches, nanoseconds + inches);
}
```

For **true** distinct types, use the newtype pattern: `struct Inch(u64);`.

### Pitfalls

- `as` casts can truncate — prefer `TryFrom` for fallible conversions.
- Type aliases do not create type safety boundaries.
- Inference fails when ambiguous — annotate then.

---

## 8. Conversion

### Plain English

Rust prefers explicit conversions via traits: `From`/`Into`, `TryFrom`/`TryInto`, and string parsing.

### JS mental model

| JS | Rust |
|---|---|
| `String(x)` / `x.toString()` | `to_string()` / `Display` / `From<…> for String` |
| `Number(x)` / `parseInt` | `parse::<T>()` / `FromStr` |
| implicit coercion | almost never — use traits |

### 8.1 `From` and `Into`

```rust
#[derive(Debug)]
struct Number {
    value: i32,
}

impl From<i32> for Number {
    fn from(item: i32) -> Self {
        Number { value: item }
    }
}

fn main() {
    let num = Number::from(30);
    println!("My number is {:?}", num);

    let int = 5;
    let num: Number = int.into(); // Into is reciprocal when From exists
    println!("My number is {:?}", num);
}
```

### 8.2 `TryFrom` and `TryInto`

Fallible conversion — returns `Result`.

```rust
use std::convert::TryFrom;

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
    assert_eq!(EvenNumber::try_from(8), Ok(EvenNumber(8)));
    assert_eq!(EvenNumber::try_from(5), Err(()));

    let result: Result<EvenNumber, ()> = 8i32.try_into();
    assert_eq!(result, Ok(EvenNumber(8)));
}
```

### 8.3 To and from Strings

```rust
use std::fmt;

struct Circle {
    radius: i32,
}

impl fmt::Display for Circle {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        write!(f, "Circle of radius {}", self.radius)
    }
}

fn main() {
    let circle = Circle { radius: 6 };
    println!("{}", circle.to_string());

    let parsed: i32 = "5".parse().unwrap();
    let turbo = "10".parse::<i32>().unwrap();
    println!("{} {}", parsed, turbo);
}
```

```js
String(6);
parseInt("5", 10);
Number("10");
```

### Pitfalls

- `.unwrap()` on parse will panic on bad input — use `?` or proper error handling.
- Implementing `From` gives you `Into` for free; prefer implementing `From`.
- `as` is not the same as `From` — different rules and safety.


---

## 9. Expressions

### Plain English

Most things in Rust are **expressions** that return a value. Blocks return the last expression (no semicolon). Statements end with `;` and return `()`.

### JS mental model

JS has expressions and statements too, but Rust leans harder on expression-oriented style (`if`, `match`, blocks all return values).

```rust
fn main() {
    let x = 5u32;
    let y = {
        let x_squared = x * x;
        let x_cube = x_squared * x;
        x_cube + x_squared + x // NO semicolon → value of block
    };

    let z = {
        2 * x; // semicolon → block returns ()
    };

    println!("x is {:?}", x);
    println!("y is {:?}", y);
    println!("z is {:?}", z);
}
```

```js
const x = 5;
const y = (() => {
  const xSquared = x * x;
  const xCube = xSquared * x;
  return xCube + xSquared + x;
})();
```

### Pitfalls

- Accidental `;` on the last line of a block changes the type to `()`.
- `return` is for early exit; prefer expression style at the end of functions.

---

## 10. Flow of Control

### Plain English

`if`/`else`, infinite `loop`, `while`, `for`, and especially **`match`** (exhaustive pattern matching). Also `if let`, `let-else`, `while let`.

### 10.1 if/else

`if` is an expression. Branches must unify types.

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

    let big_n = if n < 10 && n > -10 {
        println!(", and is a small number, increase ten-fold");
        10 * n
    } else {
        println!(", and is a big number, halve the number");
        n / 2
    };
    println!("{} -> {}", n, big_n);
}
```

```js
const bigN = n < 10 && n > -10 ? 10 * n : n / 2;
```

### 10.2 loop

Infinite loop until `break`. Can return a value from `break`.

```rust
fn main() {
    let mut count = 0u32;
    println!("Let's count until infinity!");
    loop {
        count += 1;
        if count == 3 {
            println!("three");
            continue;
        }
        println!("{}", count);
        if count == 5 {
            println!("OK, that's enough");
            break;
        }
    }
}
```

### 10.3 Nesting and labels

```rust
fn main() {
    'outer: loop {
        println!("Entered the outer loop");
        'inner: loop {
            println!("Entered the inner loop");
            break 'outer; // break outer, not just inner
        }
        // unreachable
    }
    println!("Exited the outer loop");
}
```

```js
// JS labels exist but are rare:
// outer: for (...) { break outer; }
```

### 10.4 Returning from loops

```rust
fn main() {
    let mut counter = 0;
    let result = loop {
        counter += 1;
        if counter == 10 {
            break counter * 2;
        }
    };
    println!("result = {}", result); // 20
}
```

### 10.5 while

```rust
fn main() {
    let mut n = 1;
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
        n += 1;
    }
}
```

### 10.6 for and range / iterators

```rust
fn main() {
    for n in 1..101 {
        // 1 through 100; 1..=100 is inclusive
        if n % 15 == 0 {
            println!("fizzbuzz");
        } else if n % 3 == 0 {
            println!("fizz");
        } else if n % 5 == 0 {
            println!("buzz");
        } else {
            println!("{}", n);
        }
    }

    let names = vec!["Bob", "Frank", "Ferris"];
    for name in names.iter() {
        // borrows
        match name {
            &"Ferris" => println!("There is a rustacean among us!"),
            _ => println!("Hello {}", name),
        }
    }

    for name in names.into_iter() {
        // moves
        match name {
            "Ferris" => println!("There is a rustacean among us!"),
            _ => println!("Hello {}", name),
        }
    }
    // names no longer usable here
}
```

| Method | Consumes collection? | Item type |
|---|---|---|
| `.iter()` | No | `&T` |
| `.iter_mut()` | No | `&mut T` |
| `.into_iter()` | Yes | `T` |

```js
for (const n of Array.from({ length: 100 }, (_, i) => i + 1)) { /* ... */ }
names.forEach((name) => console.log(name));
```

### 10.7 match

#### Plain English

`match` is exhaustive pattern matching — like a switch on steroids that **must** cover every case (or use `_`).

```rust
fn main() {
    let number = 13;
    match number {
        1 => println!("One!"),
        2 | 3 | 5 | 7 | 11 => println!("This is a prime"),
        13..=19 => println!("A teen"),
        _ => println!("Ain't special"),
    }

    let boolean = true;
    let binary = match boolean {
        false => 0,
        true => 1,
    };
    println!("{} -> {}", boolean, binary);
}
```

```js
// switch + ifs; TS discriminated unions help, but exhaustiveness is weaker
```

### 10.8 Destructuring in match

#### Tuples

```rust
fn main() {
    let triple = (0, -2, 3);
    match triple {
        (0, y, z) => println!("First is 0, y = {:?}, z = {:?}", y, z),
        (1, ..) => println!("First is 1 and the rest doesn't matter"),
        (.., 2) => println!("last is 2"),
        (3, ..) => println!("First is 3"),
        _ => println!("It doesn't matter what they are"),
    }
}
```

#### Arrays / slices

```rust
fn main() {
    let array = [1, -2, 6];
    match array {
        [0, second, third] => println!("0, {} and {}", second, third),
        [1, _, third] => println!("1, _, {}", third),
        [-1, second, ..] => println!("-1, {}", second),
        [3, second, tail @ ..] => println!("3, {}, {:?}", second, tail),
        [first, middle @ .., last] => println!("{}, {:?}, {}", first, middle, last),
    }
}
```

#### Enums

```rust
enum Color {
    Red,
    Blue,
    Green,
    RGB(u32, u32, u32),
    HSV(u32, u32, u32),
}

fn main() {
    let color = Color::RGB(122, 17, 40);
    match color {
        Color::Red => println!("Red!"),
        Color::Blue => println!("Blue!"),
        Color::Green => println!("Green!"),
        Color::RGB(r, g, b) => println!("R:{}, G:{}, B:{}", r, g, b),
        Color::HSV(h, s, v) => println!("H:{}, S:{}, V:{}", h, s, v),
    }
}
```

#### Pointers / ref

```rust
fn main() {
    let reference = &4;
    match reference {
        &val => println!("Got a value via destructuring: {:?}", val),
    }
    match *reference {
        val => println!("Got a value via dereferencing: {:?}", val),
    }

    let value = 5;
    match value {
        ref r => println!("Got a reference to a value: {:?}", r),
    }
    let mut mut_value = 6;
    match mut_value {
        ref mut m => {
            *m += 10;
            println!("We added 10. `mut_value`: {:?}", m);
        }
    }
}
```

#### Structs

```rust
fn main() {
    struct Foo {
        x: (u32, u32),
        y: u32,
    }
    let foo = Foo { x: (1, 2), y: 3 };
    match foo {
        Foo { x: (1, b), y } => println!("x.0 = 1, b = {}, y = {}", b, y),
        Foo { y: 2, x: i } => println!("y = 2, x = {:?}", i),
        Foo { y, .. } => println!("y = {}, other fields ignored", y),
    }
}
```

### 10.9 Guards

```rust
fn main() {
    let pair = (2, -2);
    match pair {
        (x, y) if x == y => println!("Twins"),
        (x, y) if x + y == 0 => println!("Antimatter"),
        (x, _) if x % 2 == 1 => println!("Odd"),
        _ => println!("No correlation"),
    }
}
```

### 10.10 Binding (`@`)

```rust
fn main() {
    match 1 {
        n @ 1..=5 => println!("got {}", n),
        _ => {}
    }
}
```

### 10.11 if let

Cleaner than match when you care about **one** pattern.

```rust
fn main() {
    let number = Some(7);
    if let Some(i) = number {
        println!("Matched {:?}!", i);
    }

    enum Foo {
        Bar,
        Baz,
        Qux(u32),
    }
    let a = Foo::Bar;
    if let Foo::Bar = a {
        println!("a is foobar");
    }
}
```

```js
if (number != null) {
  const i = number;
}
```

### 10.12 let-else

`let-else` diverges in the else branch (return/break/panic) so the binding is definitely unwrapped after.

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

### 10.13 while let

```rust
fn main() {
    let mut optional = Some(0);
    while let Some(i) = optional {
        if i > 9 {
            println!("Greater than 9, quit!");
            optional = None;
        } else {
            println!("`i` is `{:?}`. Try again.", i);
            optional = Some(i + 1);
        }
    }
}
```

### Pitfalls

- `match` must be exhaustive — the compiler will nag you (gift, not annoyance).
- `for x in collection` **moves** by default for many types — use `.iter()` to borrow.
- `1..5` excludes 5; `1..=5` includes 5.
- `if` without `else` returns `()` when used as expression — both branches need compatible types when assigned.

---

## 11. Functions, methods, closures, HOFs

### 11.1 Functions

```rust
fn is_divisible_by(lhs: u32, rhs: u32) -> bool {
    if rhs == 0 {
        return false;
    }
    lhs % rhs == 0
}

fn fizzbuzz(n: u32) {
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

fn fizzbuzz_to(n: u32) {
    for n in 1..=n {
        fizzbuzz(n);
    }
}

fn main() {
    fizzbuzz_to(100);
}
```

```js
function isDivisibleBy(lhs, rhs) {
  if (rhs === 0) return false;
  return lhs % rhs === 0;
}
```

### 11.2 Associated functions & methods

```rust
struct Point {
    x: f64,
    y: f64,
}

impl Point {
    // associated function (like static method) — call with Point::origin()
    fn origin() -> Point {
        Point { x: 0.0, y: 0.0 }
    }

    fn new(x: f64, y: f64) -> Point {
        Point { x, y }
    }
}

struct Rectangle {
    p1: Point,
    p2: Point,
}

impl Rectangle {
    fn area(&self) -> f64 {
        let Point { x: x1, y: y1 } = self.p1;
        let Point { x: x2, y: y2 } = self.p2;
        ((x1 - x2) * (y1 - y2)).abs()
    }

    fn perimeter(&self) -> f64 {
        let Point { x: x1, y: y1 } = self.p1;
        let Point { x: x2, y: y2 } = self.p2;
        2.0 * ((x1 - x2).abs() + (y1 - y2).abs())
    }

    fn translate(&mut self, x: f64, y: f64) {
        self.p1.x += x;
        self.p2.x += x;
        self.p1.y += y;
        self.p2.y += y;
    }
}

fn main() {
    let rectangle = Rectangle {
        p1: Point::origin(),
        p2: Point::new(3.0, 4.0),
    };
    println!("area {}", rectangle.area());
    println!("perimeter {}", rectangle.perimeter());
}
```

| Receiver | Meaning | JS analogy |
|---|---|---|
| `&self` | borrow | method reading `this` |
| `&mut self` | mutable borrow | method mutating `this` |
| `self` | consume / move | rare in JS; take ownership |

### 11.3 Closures

#### Plain English

Closures are anonymous functions that can **capture** their environment. Syntax: `|args| expr`.

```rust
fn main() {
    let outer = 10;
    let closure_annotated = |i: i32| -> i32 { i + outer };
    let closure_inferred = |i| i + outer;

    println!("annotated: {}", closure_annotated(1));
    println!("inferred: {}", closure_inferred(1));

    let one = || 1;
    println!("closure returning one: {}", one());
}
```

```js
const outer = 10;
const closure = (i) => i + outer;
```

### 11.4 Capturing

Closures capture by reference by default, but may capture by mutable ref or by move as needed. Force move with `move`.

```rust
fn main() {
    let color = String::from("green");
    let print = || println!("color: {}", color); // borrows
    print();
    print();
    let _reborrow = &color;
    println!("{}", color);

    let mut count = 0;
    let mut inc = || {
        count += 1;
        println!("count: {}", count);
    };
    inc();
    inc();

    let movable = Box::new(3);
    let consume = move || {
        println!("movable: {:?}", movable);
        // drops movable at end of closure when called, if taken by value
    };
    consume();
}
```

### 11.5 Closures as input parameters

Closures implement one or more of:

| Trait | Can |
|---|---|
| `FnOnce` | call once (may consume captures) |
| `FnMut` | call multiple times, mutate captures |
| `Fn` | call multiple times, no mutate |

```rust
fn apply<F>(f: F)
where
    F: FnOnce(),
{
    f();
}

fn apply_to_3<F>(f: F) -> i32
where
    F: Fn(i32) -> i32,
{
    f(3)
}

fn main() {
    let greeting = "hello";
    let mut farewell = "goodbye".to_owned();

    let diary = || {
        println!("I said {}.", greeting);
        farewell.push_str("!!!");
        println!("Then I screamed {}.", farewell);
        println!("And then I left.");
        // drop farewell by taking ownership implicitly via FnOnce needs
        std::mem::drop(farewell);
    };

    apply(diary);

    let double = |x| 2 * x;
    println!("3 doubled: {}", apply_to_3(double));
}
```

### 11.6 Type anonymity

```rust
fn apply<F>(f: F)
where
    F: FnOnce(),
{
    f();
}

fn main() {
    let x = 7;
    let print = || println!("{}", x);
    apply(print);
}
```

### 11.7 Input functions

Functions can be passed where closures are expected if they satisfy the trait.

```rust
fn call_me<F: Fn()>(f: F) {
    f();
}

fn function() {
    println!("I'm a function!");
}

fn main() {
    let closure = || println!("I'm a closure!");
    call_me(closure);
    call_me(function);
}
```

### 11.8 Closures as output parameters

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

### 11.9 Examples in std: Iterator::any / find

```rust
fn main() {
    let vec1 = vec![1, 2, 3];
    println!("2 in vec1: {}", vec1.iter().any(|&x| x == 2));
    println!("2 in vec1: {}", vec1.into_iter().any(|x| x == 2));

    let vec2 = vec![1, 2, 3];
    let mut iter = vec2.iter();
    assert_eq!(iter.find(|&&x| x == 2), Some(&2));
}
```

```js
[1, 2, 3].some((x) => x === 2);
[1, 2, 3].find((x) => x === 2);
```

### 11.9b Searching through iterators (`find` / `position` / `find_map`)

RBE’s “Searching through iterators” shows how closures search iterator sequences.

```rust
fn main() {
    let vec = vec![1, 9, 3, 27];
    let mut iter = vec.iter();

    // find: returns Option<&T>
    let first_gt_10 = iter.find(|&&x| x > 10);
    println!("{:?}", first_gt_10); // Some(27) — NOTE: find advances the iterator

    let vec2 = vec![1, 9, 3, 27];
    assert_eq!(vec2.iter().position(|&x| x == 9), Some(1));
    assert_eq!(
        vec2.iter().find_map(|&x| if x > 10 { Some(x * 2) } else { None }),
        Some(54)
    );
}
```

```js
[1, 9, 3, 27].find((x) => x > 10);
[1, 9, 3, 27].findIndex((x) => x === 9);
```

**Pitfall:** After `find`, the iterator is partially consumed. Reuse a fresh `.iter()` if you need to search again.

### 11.10 Higher Order Functions

```rust
fn is_odd(n: u32) -> bool {
    n % 2 == 1
}

fn main() {
    let upper = 1000;
    let sum_of_squared_odd_numbers: u32 = (0..)
        .map(|n| n * n)
        .take_while(|&n_squared| n_squared < upper)
        .filter(|&n_squared| is_odd(n_squared))
        .sum();
    println!("{}", sum_of_squared_odd_numbers);
}
```

```js
// similar pipeline with map/filter/reduce
```

### 11.11 Diverging functions

Functions that never return use `!` (never type).

```rust
fn foo() -> ! {
    panic!("This call never returns.");
}

fn main() {
    let x: i32 = match "3".parse() {
        Ok(n) => n,
        Err(_) => panic!("failed"), // panic! has type !
    };
    println!("{}", x);
}
```

### Pitfalls

- Closures and functions are related but not identical types — use generics/`impl Fn`.
- `move` closures are common when spawning threads or returning closures.
- Forgetting `&self` vs `self` changes whether the method consumes the struct.

---

## 12. Modules & visibility

### Plain English

Modules organize code. Items are **private by default**. `pub` exports them. This is stricter than JS modules.

### JS mental model

| JS | Rust |
|---|---|
| `export` / `import` | `pub` + `use` / `mod` |
| files are modules | modules declared with `mod`; files/folders follow convention |
| everything exported is public | privacy is hierarchical |

### 12.1 Visibility

```rust
mod my_mod {
    fn private_function() {
        println!("called `my_mod::private_function()`");
    }

    pub fn function() {
        println!("called `my_mod::function()`");
    }

    pub fn indirect_access() {
        print!("called `my_mod::indirect_access()`, that\n> ");
        private_function();
    }

    pub mod nested {
        pub fn function() {
            println!("called `my_mod::nested::function()`");
        }
        #[allow(dead_code)]
        fn private_function() {
            println!("called `my_mod::nested::private_function()`");
        }
    }
}

fn main() {
    my_mod::function();
    my_mod::indirect_access();
    my_mod::nested::function();
}
```

### 12.2 Struct visibility

Fields can be private even if the struct is public.

```rust
mod my {
    pub struct OpenBox<T> {
        pub contents: T,
    }
    pub struct ClosedBox<T> {
        contents: T,
    }
    impl<T> ClosedBox<T> {
        pub fn new(contents: T) -> ClosedBox<T> {
            ClosedBox { contents }
        }
    }
}

fn main() {
    let open = my::OpenBox {
        contents: "public information",
    };
    println!("{}", open.contents);

    let _closed = my::ClosedBox::new("classified");
    // _closed.contents; // ERROR: private field
}
```

### 12.3 The `use` declaration

```rust
use crate::deeply::nested::function as crate_function;

mod deeply {
    pub mod nested {
        pub fn function() {
            println!("called `deeply::nested::function()`");
        }
    }
}

fn main() {
    crate_function();
    use deeply::nested::function;
    function();
}
```

### 12.4 `super` and `self`

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
        self::function();
        function();
        self::cool::function();
        super::function();
        crate::cool::function();
    }
}

fn main() {
    my::indirect_call();
}
```

### 12.5 File hierarchy

```text
src/
  main.rs          // or lib.rs
  my_mod.rs        // mod my_mod;
  my_mod/
    mod.rs         // older style (still works)
    nested.rs
```

Modern style often uses `my_mod.rs` + `my_mod/nested.rs` instead of `my_mod/mod.rs`.

```rust
// in main.rs / lib.rs
mod my_mod;
```

### Pitfalls

- Forgotten `pub` is the #1 “why can’t I call this?” issue for JS immigrants.
- `use` brings names into scope; it does not make them public to your callers unless you `pub use`.
- Circular module structure is restricted — design acyclic trees.

---

## 13. Crates & Cargo

### 13.1 Crates

#### Plain English

A **crate** is a compilation unit (binary or library). A **package** (Cargo package) can contain multiple crates. Crates.io ≈ npm registry.

```rust
// Creating a library (rary.rs example conceptually)
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

Using a library (extern crate is mostly obsolete in edition 2018+; just add dependency and `use`).

### 13.2 Cargo.toml vs package.json

| package.json | Cargo.toml |
|---|---|
| `"name"` | `package.name` |
| `"version"` | `package.version` |
| `"dependencies"` | `[dependencies]` |
| `"devDependencies"` | `[dev-dependencies]` |
| `"scripts"` | custom: aliases / just / Makefile; cargo has built-in commands |
| `"type": "module"` | `edition = "2021"` |

```toml
[package]
name = "my_project"
version = "0.1.0"
edition = "2021"

[dependencies]
serde = { version = "1", features = ["derive"] }
rand = "0.8"

[dev-dependencies]
pretty_assertions = "1"
```

Add deps:

```bash
cargo add serde --features derive
cargo add --dev pretty_assertions
```

### 13.3 Dependencies & conventions

- Semver like npm: `"1"` means compatible with 1.x.
- Features gate optional functionality (similar to optional peer features).
- Binary entry: `src/main.rs`; library entry: `src/lib.rs`.
- Examples in `examples/`; benchmarks in `benches/`; integration tests in `tests/`.

### 13.4 Cargo testing & build scripts

```bash
cargo test
cargo build
cargo build --release
```

Build scripts: `build.rs` at package root runs before compile (codegen, link native libs) — similar in spirit to some npm prebuild hooks.

```rust
// build.rs
fn main() {
    println!("cargo:rerun-if-changed=build.rs");
}
```

### Pitfalls

- Edition is not Node’s `"type"`. It doesn’t isolate you from dependency editions.
- Feature flags can silently change API — read crate docs.
- Prefer `cargo add` over hand-editing until you’re comfortable with TOML.


---

## 14. Attributes

### Plain English

Attributes are annotations: `#[...]` on items, `#![...]` on the enclosing crate/module. Like decorators / JSDoc / babel pragmas, but first-class.

### Common attributes

```rust
#[derive(Debug, Clone)]
struct Foo;

#[allow(dead_code)]
fn unused_function() {}

#[cfg(target_os = "linux")]
fn on_linux() {
    println!("linux");
}

#[cfg(not(target_os = "linux"))]
fn on_linux() {
    println!("not linux");
}

fn main() {
    on_linux();
}
```

### 14.1 `dead_code`

By default, unused functions warn. Silence with `#[allow(dead_code)]` or use the item.

### 14.2 Crate-level attributes

```rust
// at top of main.rs / lib.rs
#![allow(dead_code)]
#![warn(missing_docs)]
```

### 14.3 `cfg` and custom cfg

Conditional compilation — like `#ifdef` or webpack DefinePlugin, but structured.

```rust
#[cfg(some_condition)]
fn conditional_function() {
    println!("condition met!");
}

fn main() {
    conditional_function();
}
```

Enable custom cfg via `RUSTFLAGS='--cfg some_condition'` or in `.cargo/config.toml`. Prefer feature flags in `Cargo.toml` for user-facing options.

```toml
[features]
default = []
awesome = []
```

```rust
#[cfg(feature = "awesome")]
fn awesome() {}
```

### Pitfalls

- `cfg` removes code at compile time — test both configurations.
- Don’t confuse `#[attr]` (next item) with `#![attr]` (this module/crate).

---

## 15. Generics

### Plain English

Generics = parameterized types/functions. Like TypeScript generics, but monomorphized at compile time (fast, specialized code).

### JS mental model

TS: `function identity<T>(x: T): T`. Rust is similar, with trait **bounds** instead of structural typing.

### 15.1 Generic functions

```rust
fn foo<T>(arg: T) -> T {
    arg
}

fn main() {
    println!("{}", foo(1));
    println!("{}", foo("hi"));
}
```

### 15.2 Implementation

```rust
struct Val {
    val: f64,
}
struct GenVal<T> {
    gen_val: T,
}

impl Val {
    fn value(&self) -> &f64 {
        &self.val
    }
}

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

### 15.3 Generic traits

```rust
struct Empty;
struct Null;

trait DoubleDrop<T> {
    fn double_drop(self, _: T);
}

impl<T, U> DoubleDrop<T> for U {
    fn double_drop(self, _: T) {}
}

fn main() {
    let empty = Empty;
    let null = Null;
    empty.double_drop(null);
}
```

### 15.4 Bounds

Require that `T` implements certain traits.

```rust
use std::fmt::Debug;

fn printer<T: Debug>(t: T) {
    println!("{:?}", t);
}

trait HasArea {
    fn area(&self) -> f64;
}

impl HasArea for f64 {
    fn area(&self) -> f64 {
        self * self
    }
}

fn print_area<T: HasArea>(shape: T) {
    println!("area: {}", shape.area());
}

fn main() {
    printer(42);
    print_area(3.0_f64);
}
```

### 15.5 Testcase: empty bounds

Even without methods, bounds can enforce capabilities via other traits (e.g. cardinality through `Eq` + construction). See RBE “empty bounds” — the idea is: a bound can exist to constrain type relationships even when you don’t call methods from it directly.

```rust
struct Cardinal;
struct BlueJay;
struct Turkey;

trait Red {}
trait Blue {}

impl Red for Cardinal {}
impl Blue for BlueJay {}

fn red<T: Red>(_: &T) -> &'static str {
    "red"
}
fn blue<T: Blue>(_: &T) -> &'static str {
    "blue"
}

fn main() {
    let cardinal = Cardinal;
    let blue_jay = BlueJay;
    println!("{}", red(&cardinal));
    println!("{}", blue(&blue_jay));
    // red(&blue_jay); // ERROR
}
```

### 15.6 Multiple bounds

```rust
use std::fmt::{Debug, Display};

fn compare_prints<T: Debug + Display>(t: &T) {
    println!("Debug: `{:?}`", t);
    println!("Display: `{}`", t);
}

fn main() {
    compare_prints(&"hello");
}
```

### 15.7 Where clauses

Cleaner for complex bounds.

```rust
use std::fmt::Debug;

fn print_t<T>(t: T)
where
    T: Debug,
{
    println!("{:?}", t);
}

fn main() {
    print_t(vec![1, 2, 3]);
}
```

### 15.8 New Type Idiom

Wrap a type in a tuple struct for stronger typing / trait impls.

```rust
struct Years(i64);
struct Days(i64);

impl Years {
    pub fn to_days(&self) -> Days {
        Days(self.0 * 365)
    }
}

fn old_enough(age: &Years) -> bool {
    age.0 >= 18
}

fn main() {
    let age = Years(77);
    let days = age.to_days();
    println!("old enough: {}", old_enough(&age));
    // old_enough(&days); // ERROR: wrong type — good!
    let _years = Years(days.0 / 365);
}
```

### 15.9 Associated items / associated types

#### The Problem (why associated types exist)

Without associated types, a trait that “contains” two related types forces callers to write awkward generic parameters everywhere:

```rust
// Conceptual "before" — awkward extra type parameters on every use site
trait ContainsAwkward<A, B> {
    fn contains(&self, _: &A, _: &B) -> bool;
}

fn difference_awkward<A, B, C>(container: &C) -> i32
where
    C: ContainsAwkward<A, B>,
{
    let _ = container;
    0
}
```

Associated types move `A` and `B` *into* the trait so implementors pick them once, and users don’t repeat them at every call site.

```rust
trait Contains {
    type A;
    type B;
    fn contains(&self, _: &Self::A, _: &Self::B) -> bool;
    fn foo(&self) -> i32;
}

fn difference<C: Contains>(container: &C) -> i32 {
    container.foo()
}

struct Container(i32, i32);

impl Contains for Container {
    type A = i32;
    type B = i32;
    fn contains(&self, n1: &i32, n2: &i32) -> bool {
        (&self.0 == n1) && (&self.1 == n2)
    }
    fn foo(&self) -> i32 {
        self.0
    }
}

fn main() {
    let c = Container(1, 2);
    println!("{}", c.contains(&1, &2));
    println!("{}", difference(&c));
}
```

### 15.10 Phantom type parameters

Zero-sized type parameters used only at compile time for type safety.

```rust
use std::marker::PhantomData;

#[derive(PartialEq)]
struct PhantomTuple<A, B>(A, PhantomData<B>);

fn main() {
    let _t1: PhantomTuple<char, f64> = PhantomTuple('Q', PhantomData);
    let _t2: PhantomTuple<char, f64> = PhantomTuple('Q', PhantomData);
    // Different B would make types incompatible even if runtime data matches
}
```

#### Testcase: unit clarification

Phantom types can encode units (meters vs mm) so you cannot add incompatible quantities without conversion — a classic RBE example of type-level safety.

```rust
use std::marker::PhantomData;
use std::ops::Add;

#[derive(Debug, Clone, Copy)]
enum Inch {}
#[derive(Debug, Clone, Copy)]
enum Mm {}

#[derive(Debug, Clone, Copy)]
struct Length<Unit>(f64, PhantomData<Unit>);

impl<Unit> Add for Length<Unit> {
    type Output = Length<Unit>;
    fn add(self, rhs: Length<Unit>) -> Length<Unit> {
        Length(self.0 + rhs.0, PhantomData)
    }
}

fn main() {
    let one_foot = Length::<Inch>(12.0, PhantomData);
    let one_meter = Length::<Mm>(1000.0, PhantomData);
    let two_feet = one_foot + one_foot;
    let _two_meters = one_meter + one_meter;
    println!("{:?}", two_feet);
    // one_foot + one_meter; // ERROR: different Unit
}
```

### Pitfalls

- Over-constraining bounds makes APIs painful — bound only what you use.
- Monomorphization can increase binary size (codegen per type).
- Associated types are often clearer than extra generic parameters on traits.

---

## 16. Scoping rules: ownership, borrowing, lifetimes

> **THE key JS → Rust section.** Read slowly. Revisit often.

### Plain English (big picture)

In JS, values live on the heap (mostly) and a **garbage collector** frees them later. In Rust, every value has exactly one **owner**. When the owner goes out of scope, Rust drops (frees) the value immediately. You can **borrow** references (`&` / `&mut`) temporarily. **Lifetimes** are how the compiler proves references never outlive their data.

### JS mental model

| JS | Rust |
|---|---|
| Many variables can point at one object | One **owner**; others may borrow |
| GC frees eventually | Drop runs at end of scope |
| Mutate freely if you have a reference | Either many `&T` **or** one `&mut T`, never both |
| Closures capture vars freely | Captures must satisfy borrow rules |
| Use-after-free / double-free possible in native addons | Prevented at compile time in safe Rust |

---

### 16.1 RAII

#### Plain English

**Resource Acquisition Is Initialization**: when a value is created, it owns its resource; when destroyed (`drop`), it releases it. No manual `free`.

```rust
fn create_box() {
    let _box1 = Box::new(3i32);
    // _box1 destroyed here, memory freed
}

fn main() {
    let _box2 = Box::new(5i32);
    {
        let _box3 = Box::new(4i32);
    } // _box3 freed
    for _ in 0u32..1_000 {
        create_box();
    }
} // _box2 freed
```

```js
// JS: objects freed by GC sometime after no references remain
```

#### Destructor (`Drop`)

```rust
struct ToDrop;

impl Drop for ToDrop {
    fn drop(&mut self) {
        println!("ToDrop is being dropped");
    }
}

fn main() {
    let _x = ToDrop;
    println!("end of main");
}
```

---

### 16.2 Ownership and moves

#### Plain English

Assigning / passing a non-`Copy` value **moves** it. The old name becomes invalid.

```rust
fn destroy_box(c: Box<i32>) {
    println!("Destroying a box that contains {}", c);
    // c dropped at end
}

fn main() {
    let x = 5u32; // Copy types (integers) are copied, not moved
    let y = x;
    println!("x is {}, y is {}", x, y);

    let a = Box::new(5i32);
    let b = a; // MOVE
    // println!("a contains {}", a); // ERROR: a moved
    destroy_box(b);
}
```

```js
// JS always "moves" references by copying the pointer; both names still work.
// Rust invalidates the old name for owned non-Copy values.
```

#### Mutability of ownership

```rust
fn main() {
    let immutable_box = Box::new(5u32);
    println!("{}", immutable_box);
    // *immutable_box = 4; // ERROR

    let mut mutable_box = immutable_box; // move + rebind as mut
    *mutable_box = 4;
    println!("{}", mutable_box);
}
```

#### Partial moves

```rust
fn main() {
    #[derive(Debug)]
    struct Person {
        name: String,
        age: u8,
    }

    let person = Person {
        name: String::from("Alice"),
        age: 20,
    };

    let Person { name, ref age } = person;
    // `name` moved out; `age` borrowed
    println!("name {} age {}", name, age);
    // println!("{:?}", person); // ERROR: partial move
    println!("age still via borrow {}", age);
}
```

---

### 16.3 Borrowing

#### Plain English

`&T` immutable borrow (shared). `&mut T` mutable borrow (exclusive). You can have many shared borrows **or** one mutable borrow.

```rust
fn eat_box_i32(boxed: Box<i32>) {
    println!("Destroying box that contains {}", boxed);
}

fn borrow_i32(borrowed: &i32) {
    println!("This int is: {}", borrowed);
}

fn main() {
    let boxed_i32 = Box::new(5_i32);
    let stacked_i32 = 6_i32;

    borrow_i32(&boxed_i32);
    borrow_i32(&stacked_i32);

    {
        let _ref_to_i32: &i32 = &boxed_i32;
        // eat_box_i32(boxed_i32); // ERROR: cannot move while borrowed
        borrow_i32(_ref_to_i32);
    }

    eat_box_i32(boxed_i32);
}
```

#### Mutability while borrowing

```rust
fn main() {
    let mut book = 10i32;
    {
        let r1 = &book;
        let r2 = &book;
        println!("{} {}", r1, r2);
        // let r3 = &mut book; // ERROR while r1/r2 active
    }
    let r3 = &mut book;
    *r3 += 1;
    println!("{}", r3);
}
```

```js
// JS: no compiler enforcement — race conditions / confused shared mutation possible
```

#### Aliasing rules (summary)

1. Any number of `&T` **or**
2. Exactly one `&mut T`
3. References must always be valid (no dangling)

#### The `ref` pattern

```rust
fn main() {
    let optional = Some(Box::new(123));
    match optional {
        Some(ref v) => println!("Got a ref to {}", v),
        None => {}
    }
    // optional still usable if we used ref (didn't move out)
}
```

---

### 16.4 Lifetimes

#### Plain English

A lifetime `'a` is a name for “how long this reference is valid.” Usually inferred (**elision**). You annotate when the compiler cannot prove relationships.

#### Explicit annotation

```rust
fn print_one<'a>(x: &'a i32) {
    println!("`print_one`: x is {}", x);
}

fn add_one<'a>(x: &'a mut i32) {
    *x += 1;
}

fn main() {
    let mut a = 5;
    print_one(&a);
    add_one(&mut a);
    print_one(&a);
}
```

#### Functions returning references

The output lifetime must be tied to an input lifetime.

```rust
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}

fn main() {
    let a = "abcd";
    let b = "xyz";
    println!("{}", longest(a, b));
}
```

```js
// JS functions can return references to inputs freely; GC keeps them alive.
// Rust forces you to prove the returned ref doesn’t outlive its source.
```

#### Methods

```rust
struct Owner(i32);

impl Owner {
    fn add_one<'a>(&'a mut self) {
        self.0 += 1;
    }
    fn print<'a>(&'a self) {
        println!("{}", self.0);
    }
}

fn main() {
    let mut owner = Owner(10);
    owner.add_one();
    owner.print();
}
```

(In real code, lifetimes on these methods are elided.)

#### Structs with references

```rust
#[derive(Debug)]
struct Borrowed<'a>(&'a i32);

#[derive(Debug)]
struct NamedBorrowed<'a> {
    x: &'a i32,
    y: &'a i32,
}

fn main() {
    let x = 18;
    let y = 15;
    let single = Borrowed(&x);
    let double = NamedBorrowed { x: &x, y: &y };
    println!("{:?} {:?}", single, double);
}
```

#### Traits and lifetime bounds

```rust
use std::fmt::Debug;

#[derive(Debug)]
struct Ref<'a, T: 'a>(&'a T);

fn print<T>(t: T)
where
    T: Debug,
{
    println!("`print`: t is {:?}", t);
}

fn main() {
    let x = 7;
    let ref_x = Ref(&x);
    print(ref_x);
}
```

`T: 'a` means “all references in `T` live at least `'a`”.

#### Coercion (subtyping of lifetimes)

Longer lifetimes can coerce to shorter ones.

```rust
fn multiply<'a>(first: &'a i32, second: &'a i32) -> i32 {
    first * second
}

fn main() {
    let x = 3;
    {
        let y = 4;
        println!("{}", multiply(&x, &y)); // 'x coerced down to 'y
    }
}
```

#### `'static`

1. References that live for the whole program (string literals).
2. Trait bound `T: 'static` means T contains no short-lived borrows.

```rust
fn main() {
    let s: &'static str = "hello"; // literal stored in binary
    println!("{}", s);
}
```

#### Elision rules (simplified)

The compiler applies rules so you often omit lifetimes:

1. Each elided input reference gets its own lifetime.
2. If there is exactly one input lifetime, it is assigned to all elided outputs.
3. If there are multiple inputs but one is `&self` / `&mut self`, self’s lifetime is assigned to outputs.

```rust
fn first_word(s: &str) -> &str {
    s.split_whitespace().next().unwrap_or("")
}
```

### Ownership checklist for JS devs

1. Who **owns** this value?
2. Am I **moving**, **copying**, or **borrowing**?
3. If borrowing: shared (`&`) or exclusive (`&mut`)?
4. Does any reference outlive the data? (lifetimes)
5. Need shared ownership? → `Rc` / `Arc` (later section)
6. Need shared mutation? → `RefCell` / `Mutex` (interior mutability)

### Pitfalls

- Fighting the borrow checker usually means the design has unclear ownership — simplify ownership graphs.
- Don’t reach for `.clone()` everywhere as a first solution; it works but can hide design issues.
- `'static` bounds on trait objects are common in async; it does not mean “allocate forever.”
- Returning references to local variables will **always** fail — return owned values (`String`, `Vec`) instead.


---

## 17. Traits

### Plain English

Traits define shared behavior — like TypeScript interfaces + mixin methods. Types **implement** traits with `impl Trait for Type`.

### JS mental model

| JS / TS | Rust |
|---|---|
| `interface` / duck typing | `trait` |
| class implements interface | `impl Trait for Struct` |
| default methods in abstract class | default methods in trait |
| Symbol.iterator | `Iterator` trait |

### 17.1 Basics

```rust
struct Sheep {
    naked: bool,
    name: &'static str,
}

trait Animal {
    fn new(name: &'static str) -> Self;
    fn name(&self) -> &'static str;
    fn noise(&self) -> &'static str;
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
            println!("{} is already naked...", self.name());
        } else {
            println!("{} gets a haircut!", self.name);
            self.naked = true;
        }
    }
}

impl Animal for Sheep {
    fn new(name: &'static str) -> Sheep {
        Sheep { name, naked: false }
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
    fn talk(&self) {
        println!("{} pauses briefly... {}", self.name, self.noise());
    }
}

fn main() {
    let mut dolly: Sheep = Animal::new("Dolly");
    dolly.talk();
    dolly.shear();
    dolly.talk();
}
```

### 17.2 Derive

Auto-implement common traits:

```rust
#[derive(Debug, Clone, Copy, PartialEq, Eq, PartialOrd, Ord, Hash, Default)]
struct Foo {
    a: i32,
}
```

| Derive | Meaning |
|---|---|
| `Debug` | `{:?}` |
| `Clone` | explicit `.clone()` |
| `Copy` | implicit bitwise copy (only if all fields Copy) |
| `PartialEq` / `Eq` | `==` |
| `PartialOrd` / `Ord` | ordering |
| `Hash` | hash maps/sets |
| `Default` | `Default::default()` |

### 17.3 Returning traits with `dyn`

```rust
struct Sheep {}
struct Cow {}

trait Animal {
    fn noise(&self) -> &'static str;
}

impl Animal for Sheep {
    fn noise(&self) -> &'static str {
        "baaaaah!"
    }
}
impl Animal for Cow {
    fn noise(&self) -> &'static str {
        "moooooo!"
    }
}

fn random_animal(random_number: f64) -> Box<dyn Animal> {
    if random_number < 0.5 {
        Box::new(Sheep {})
    } else {
        Box::new(Cow {})
    }
}

fn main() {
    let animal = random_animal(0.234);
    println!("{}", animal.noise());
}
```

```js
// return objects implementing the same interface — runtime polymorphism
```

`dyn Trait` is a trait object (dynamic dispatch). Prefer `impl Trait` when one concrete type is enough.

### 17.4 Operator overloading

```rust
use std::ops::Add;

#[derive(Debug)]
struct Foo;

#[derive(Debug)]
struct Bar;

#[derive(Debug)]
struct FooBar;

impl Add<Bar> for Foo {
    type Output = FooBar;
    fn add(self, _rhs: Bar) -> FooBar {
        FooBar
    }
}

fn main() {
    println!("{:?}", Foo + Bar);
}
```

### 17.5 Drop

Already seen — custom destructor.

```rust
struct Droppable {
    name: &'static str,
}

impl Drop for Droppable {
    fn drop(&mut self) {
        println!("> Dropping {}", self.name);
    }
}

fn main() {
    let _a = Droppable { name: "a" };
    {
        let _b = Droppable { name: "b" };
    }
    println!("Leaving main");
}
```

### 17.6 Iterators

```rust
struct Fibonacci {
    curr: u32,
    next: u32,
}

impl Iterator for Fibonacci {
    type Item = u32;
    fn next(&mut self) -> Option<Self::Item> {
        let current = self.curr;
        self.curr = self.next;
        self.next = current + self.next;
        Some(current)
    }
}

fn fibonacci() -> Fibonacci {
    Fibonacci { curr: 0, next: 1 }
}

fn main() {
    for i in fibonacci().take(4) {
        println!("{}", i);
    }
    for i in fibonacci().skip(4).take(4) {
        println!("{}", i);
    }
}
```

```js
function* fibonacci() {
  let curr = 0, next = 1;
  for (;;) {
    yield curr;
    [curr, next] = [next, curr + next];
  }
}
```

### 17.7 `impl Trait`

```rust
use std::iter;
use std::vec::IntoIter;

fn combine_vecs_explicit_return_type(
    v: Vec<i32>,
    u: Vec<i32>,
) -> iter::Cycle<iter::Chain<IntoIter<i32>, IntoIter<i32>>> {
    v.into_iter().chain(u.into_iter()).cycle()
}

fn combine_vecs(v: Vec<i32>, u: Vec<i32>) -> impl Iterator<Item = i32> {
    v.into_iter().chain(u.into_iter()).cycle()
}

fn main() {
    let mut cycle = combine_vecs(vec![1, 2], vec![3]);
    println!("{:?}", cycle.next());
}
```

As argument:

```rust
fn parse_csv_document(src: impl std::io::BufRead) {
    let _ = src;
}
```

### 17.8 Clone and Copy

```rust
#[derive(Debug, Clone, Copy)]
struct Unit;

#[derive(Clone, Debug)]
struct Pair(Box<i32>, Box<i32>);

fn main() {
    let unit = Unit;
    let _copied = unit; // Copy

    let pair = Pair(Box::new(1), Box::new(2));
    let _cloned = pair.clone(); // Clone (deep-ish via Box clone)
}
```

### 17.9 Supertraits

```rust
trait Person {
    fn name(&self) -> String;
}

trait Student: Person {
    fn university(&self) -> String;
}

trait Programmer {
    fn fav_language(&self) -> String;
}

trait CompSciStudent: Programmer + Student {
    fn git_username(&self) -> String;
}
```

### 17.10 Disambiguating overlapping traits

```rust
trait UsernameWidget {
    fn get(&self) -> String;
}
trait AgeWidget {
    fn get(&self) -> u8;
}

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
        username: "rustacean".into(),
        age: 28,
    };
    println!("{}", <Form as UsernameWidget>::get(&form));
    println!("{}", <Form as AgeWidget>::get(&form));
}
```

### Pitfalls

- Orphan rule: you can implement your trait for any type, or foreign traits for **your** types — not foreign traits for foreign types.
- Trait objects require object safety (no generic methods, etc.).
- Prefer `impl Trait` in returns when you don’t need heterogeneous values.

---

## 18. Macros

### Plain English

Macros write code for you at compile time. `macro_rules!` is declarative (“by example”). Procedural macros (derive/attribute/function-like) are more advanced and live in separate crates.

### JS mental model

Closest to hygienic compile-time codegen / Babel macros / template metaprogramming — **not** runtime `eval`.

### 18.1 `macro_rules!` basics

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

### 18.2 Designators

Common fragment types: `ident`, `expr`, `block`, `ty`, `path`, `tt`, `item`, `pat`, `lifetime`, `literal`, …

```rust
macro_rules! create_function {
    ($func_name:ident) => {
        fn $func_name() {
            println!("You called {:?}()", stringify!($func_name));
        }
    };
}

create_function!(foo);
create_function!(bar);

macro_rules! print_result {
    ($expression:expr) => {
        println!("{:?} = {:?}", stringify!($expression), $expression);
    };
}

fn main() {
    foo();
    bar();
    print_result!(1u32 + 1);
    print_result!({
        let x = 1u32;
        x * x + 2 * x - 1
    });
}
```

### 18.3 Overload / multiple patterns

```rust
macro_rules! test {
    ($left:expr; and $right:expr) => {
        println!(
            "{:?} and {:?} is {:?}",
            stringify!($left),
            stringify!($right),
            $left && $right
        )
    };
    ($left:expr; or $right:expr) => {
        println!(
            "{:?} or {:?} is {:?}",
            stringify!($left),
            stringify!($right),
            $left || $right
        )
    };
}

fn main() {
    test!(1i32 + 1 == 2i32; and 2i32 * 2 == 4i32);
    test!(true; or false);
}
```

### 18.4 Repeat

```rust
macro_rules! find_min {
    ($x:expr) => ($x);
    ($x:expr, $($y:expr),+) => {
        std::cmp::min($x, find_min!($($y),+))
    };
}

fn main() {
    println!("{}", find_min!(1));
    println!("{}", find_min!(1 + 2, 2));
    println!("{}", find_min!(5, 2 * 3, 4));
}
```

### 18.5 DRY (Don't Repeat Yourself)

Macros reduce boilerplate across similar implementations (RBE shows implementing traits for many types via macros).

```rust
// DRY: generate similar test helpers / impl blocks from one pattern
macro_rules! assert_equal_len {
    ($a:expr, $b:expr) => {
        assert_eq!($a.len(), $b.len());
    };
}

macro_rules! test_eq {
    ($left:expr, $right:expr) => {{
        assert_equal_len!($left, $right);
        for (a, b) in $left.iter().zip($right.iter()) {
            assert_eq!(a, b);
        }
    }};
}

fn main() {
    let a = [1, 2, 3];
    let b = [1, 2, 3];
    test_eq!(a, b);
}
```

This is the RBE idea: one macro eliminates repeated validation/impl boilerplate across types.

### 18.6 DSL (Domain Specific Languages)

`println!`, `vec!`, `html!` (in some crates) look like built-in syntax but are macros.

```rust
macro_rules! calculate {
    (eval $e:expr) => {{
        let val: usize = $e;
        println!("{} = {}", stringify!{$e}, val);
    }};
}

fn main() {
    calculate! { eval 1 + 2 }
    calculate! { eval (1 + 2) * (3 / 4) }
}
```

### 18.7 Variadics

```rust
macro_rules! print_all {
    ($($arg:expr),*) => {{
        $(
            println!("{}", $arg);
        )*
    }};
}

fn main() {
    print_all!(1, "two", 3.0);
}
```

### 18.8 Procedural macros (intro)

Three kinds (separate `proc-macro` crate):

1. **Derive** — `#[derive(MyTrait)]`
2. **Attribute** — `#[my_attr]`
3. **Function-like** — `my_macro!(...)`

You use them constantly (`serde::Serialize`); writing them is intermediate/advanced.

### Pitfalls

- Macro errors are harder to read — start small.
- Macros can introduce names — mind hygiene and `paste`/`stringify`.
- Prefer functions/generics when they suffice; macros are for syntax/variadics/boilerplate.

---

## 19. Error handling

### Plain English

Rust separates:

- **Unrecoverable** errors → `panic!`
- **Recoverable** absence → `Option<T>`
- **Recoverable** failure → `Result<T, E>`

The `?` operator propagates errors (similar spirit to rejecting Promises / early return).

### JS mental model

| JS | Rust |
|---|---|
| `null` / `undefined` | `Option::None` |
| `try/catch` throw | `Result::Err` + `?` / `match` |
| `Promise.reject` | `Err(...)` |
| Uncaught exception crash | `panic!` (abort or unwind) |
| Optional chaining `?.` | `?` on `Option` / combinators |

---

### 19.1 panic

```rust
fn drink(beverage: &str) {
    if beverage == "lemonade" {
        panic!("AAAaaaaa!!!!");
    }
    println!("Some refreshing {} is all I need.", beverage);
}

fn main() {
    drink("water");
    // drink("lemonade"); // panics
}
```

### 19.2 abort & unwind

Panic can unwind the stack (run destructors) or abort. Controlled via `Cargo.toml`:

```toml
[profile.release]
panic = "abort"
```

---

### 19.3 Option & unwrap

```rust
fn give_adult(drink: Option<&str>) {
    match drink {
        Some("lemonade") => println!("Yuck! Too sugary."),
        Some(inner) => println!("{}? How nice.", inner),
        None => println!("No drink? Oh well."),
    }
}

fn drink(drink: Option<&str>) {
    let inside = drink.unwrap(); // panics on None
    if inside == "lemonade" {
        panic!("AAAaaaaa!!!!");
    }
    println!("I love {}s!!!!!", inside);
}

fn main() {
    let water = Some("water");
    let lemonade = Some("lemonade");
    let void = None;
    give_adult(water);
    give_adult(lemonade);
    give_adult(void);

    let coffee = Some("coffee");
    drink(coffee);
}
```

```js
function giveAdult(drink) {
  if (drink == null) console.log("No drink?");
  else console.log(drink);
}
```

#### Unpacking options with `?`

```rust
fn next_birthday(current_age: Option<u8>) -> Option<String> {
    let next = current_age?; // return None if None
    Some(format!("Next year I will be {}", next + 1))
}
```

#### Combinators: `map`

```rust
fn main() {
    let maybe = Some(1);
    let doubled = maybe.map(|x| x * 2);
    println!("{:?}", doubled); // Some(2)
}
```

```js
const doubled = maybe == null ? null : maybe * 2;
// or optional chaining patterns
```

#### Combinators: `and_then` (flatMap)

```rust
fn sq_then_to_string(x: u32) -> Option<String> {
    x.checked_mul(x).map(|sq| sq.to_string())
}

fn main() {
    println!("{:?}", Some(2).and_then(sq_then_to_string));
    println!("{:?}", None::<u32>.and_then(sq_then_to_string));
}
```

#### Defaults: `or`, `or_else`, `get_or_insert`, `get_or_insert_with`

```rust
fn main() {
    let a: Option<i32> = None;
    assert_eq!(a.or(Some(2)), Some(2));

    let b = None::<i32>;
    assert_eq!(b.or_else(|| Some(3)), Some(3));

    let mut v = None;
    *v.get_or_insert(5) += 1;
    assert_eq!(v, Some(6));

    let mut w = None;
    *w.get_or_insert_with(|| 7) += 1;
    assert_eq!(w, Some(8));
}
```

---

### 19.4 Result

```rust
fn multiply(first: &str, second: &str) -> i32 {
    let a = first.parse::<i32>().unwrap();
    let b = second.parse::<i32>().unwrap();
    a * b
}

fn main() {
    println!("{}", multiply("10", "2"));
    // multiply("t", "2"); // panic
}
```

Better:

```rust
fn multiply(first: &str, second: &str) -> Result<i32, std::num::ParseIntError> {
    let a = first.parse::<i32>()?;
    let b = second.parse::<i32>()?;
    Ok(a * b)
}

fn main() {
    println!("{:?}", multiply("10", "2"));
    println!("{:?}", multiply("t", "2"));
}
```

```js
async function multiply(a, b) {
  const x = Number(a);
  const y = Number(b);
  if (Number.isNaN(x) || Number.isNaN(y)) throw new Error("parse");
  return x * y;
}
```

#### `map` for Result

```rust
fn main() {
    let n: Result<i32, _> = "5".parse();
    println!("{:?}", n.map(|x| x * 2));
}
```

#### Aliases for Result

```rust
type ParseResult<T> = Result<T, std::num::ParseIntError>;
```

#### Early returns

```rust
fn multiply(first: &str, second: &str) -> Result<i32, std::num::ParseIntError> {
    let a = match first.parse::<i32>() {
        Ok(n) => n,
        Err(e) => return Err(e),
    };
    let b = match second.parse::<i32>() {
        Ok(n) => n,
        Err(e) => return Err(e),
    };
    Ok(a * b)
}
```

#### Introducing `?`

`?` replaces the match-early-return pattern. (Old `try!` macro is obsolete.)

---

### 19.5 Multiple error types

#### Pulling Results out of Options

```rust
use std::num::ParseIntError;

fn double_first(vec: Vec<&str>) -> Option<Result<i32, ParseIntError>> {
    vec.first().map(|first| first.parse::<i32>().map(|n| 2 * n))
}

fn main() {
    let numbers = vec!["42", "93", "18"];
    println!("{:?}", double_first(numbers));
}
```

#### Defining an error type

```rust
use std::fmt;

#[derive(Debug)]
struct DoubleError;

impl fmt::Display for DoubleError {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        write!(f, "invalid first item to double")
    }
}

impl std::error::Error for DoubleError {}
```

#### Boxing errors

```rust
type Result<T> = std::result::Result<T, Box<dyn std::error::Error>>;

fn main() -> Result<()> {
    let s = "not a number";
    let _n: i32 = s.parse()?; // ParseIntError coerced into Box<dyn Error>
    Ok(())
}
```

#### Other uses of `?` / wrapping errors

Convert between error types with `From` / `map_err` / crates like `thiserror` / `anyhow`.

```rust
use std::num::ParseIntError;

#[derive(Debug)]
enum MyError {
    Parse(ParseIntError),
    Empty,
}

impl From<ParseIntError> for MyError {
    fn from(e: ParseIntError) -> Self {
        MyError::Parse(e)
    }
}

fn parse_first(v: &[&str]) -> Result<i32, MyError> {
    let s = v.first().ok_or(MyError::Empty)?;
    Ok(s.parse()?) // ParseIntError → MyError via From
}
```

---

### 19.6 Iterating over Results

```rust
fn main() {
    let strings = vec!["tofu", "93", "18"];
    let numbers: Vec<_> = strings
        .into_iter()
        .map(|s| s.parse::<i32>())
        .collect();
    println!("{:?}", numbers);

    let strings = vec!["tofu", "93", "18"];
    let numbers: Vec<i32> = strings
        .into_iter()
        .filter_map(|s| s.parse().ok())
        .collect();
    println!("{:?}", numbers);

    let strings = vec!["42", "93", "18"];
    let result: Result<Vec<i32>, _> = strings.into_iter().map(|s| s.parse()).collect();
    println!("{:?}", result);
}
```

### Pitfalls

- `.unwrap()` / `.expect()` are fine in examples/tests; avoid in library code.
- Don’t use exceptions-for-flow — return `Result`.
- Mixing `Option` and `Result` needs explicit conversion (`ok_or`, `transpose`).
- `main` can return `Result<(), E>` for free error printing.


---

## 20. Std library types

### Plain English

The standard library gives you heap boxes, growable arrays, strings, hash maps, and smart pointers for shared ownership.

### 20.1 Box, stack and heap

`Box<T>` is an owned heap allocation (like a unique_ptr / single owned object on the heap).

```rust
fn main() {
    let x = 5u32;            // stack
    let y = Box::new(x);     // heap
    println!("{}", y);

    #[derive(Debug)]
    enum List {
        Cons(u32, Box<List>),
        Nil,
    }
    let list = List::Cons(1, Box::new(List::Cons(2, Box::new(List::Nil))));
    println!("{:?}", list);
}
```

```js
// Objects are heap-allocated by default in JS — Box is explicit ownership of heap data.
```

### 20.2 Vectors

`Vec<T>` ≈ JS Array (growable, contiguous).

```rust
fn main() {
    let mut xs = vec![1i32, 2, 3];
    println!("len {}", xs.len());
    xs.push(4);
    println!("{:?}", xs);
    println!("pop {:?}", xs.pop());

    for x in xs.iter() {
        println!("> {}", x);
    }
    for x in xs.iter_mut() {
        *x *= 2;
    }
    println!("{:?}", xs);
}
```

```js
const xs = [1, 2, 3];
xs.push(4);
xs.pop();
```

### 20.3 Strings

Rust has:

- `&str` — string slice (view), often UTF-8 view into something else
- `String` — owned, growable UTF-8 buffer

```rust
fn main() {
    let pangram = "the quick brown fox jumps over the lazy dog";
    println!("pangram: {:?}", pangram);

    let mut s = String::from("Hello");
    s.push(' ');
    s.push_str("World");
    println!("{}", s);

    let bytes = s.into_bytes();
    println!("{:?}", bytes);

    let s = String::from_utf8(bytes).unwrap();
    println!("{}", s);

    // Escapes
    let raw = r##"Raw string with "quotes" and #hashes"##;
    println!("{}", raw);
}
```

```js
let s = "Hello";
s += " World"; // JS strings are immutable; this rebinds
```

**Pitfall:** Indexing `s[0]` does not work like JS — UTF-8 is variable width. Iterate `.chars()` or `.bytes()`, or slice carefully at char boundaries.

### 20.4 Option (std view)

Already covered — `Some` / `None`. Prefer it over sentinel values.

### 20.5 Result and `?` (std view)

```rust
fn main() -> Result<(), Box<dyn std::error::Error>> {
    let n: i32 = "5".parse()?;
    println!("{}", n);
    Ok(())
}
```

### 20.6 panic! (as a library tool)

```rust
fn main() {
    // panic!("crash");
}
```

### 20.7 HashMap

≈ JS `Map` / plain objects for string keys.

```rust
use std::collections::HashMap;

fn main() {
    let mut scores = HashMap::new();
    scores.insert("blue", 10);
    scores.insert("yellow", 50);

    println!("{:?}", scores.get("blue"));
    for (k, v) in &scores {
        println!("{}: {}", k, v);
    }

    scores.entry("red").or_insert(0);
    *scores.entry("blue").or_insert(0) += 5;
}
```

```js
const scores = new Map([
  ["blue", 10],
  ["yellow", 50],
]);
scores.get("blue");
```

#### Alternate / custom key types

Keys must implement `Eq + Hash`. Custom types need `#[derive(Eq, PartialEq, Hash)]`.

```rust
use std::collections::HashMap;

#[derive(Hash, Eq, PartialEq, Debug)]
struct Account<'a> {
    username: &'a str,
    password: &'a str,
}

fn main() {
    let mut accounts = HashMap::new();
    let account = Account {
        username: "j.everyman",
        password: "password123",
    };
    accounts.insert(account, "Role Account");
}
```

#### HashSet

≈ JS `Set`.

```rust
use std::collections::HashSet;

fn main() {
    let mut a: HashSet<i32> = vec![1i32, 2, 3].into_iter().collect();
    let b: HashSet<i32> = vec![2i32, 3, 4].into_iter().collect();
    a.insert(5);
    println!("union {:?}", a.union(&b).collect::<Vec<_>>());
    println!("intersection {:?}", a.intersection(&b).collect::<Vec<_>>());
}
```

### 20.8 Rc

`Rc<T>` = reference-counted shared ownership (**single-threaded**). ≈ sharing an object without a GC, with explicit counts.

```rust
use std::rc::Rc;

fn main() {
    let apple = Rc::new("the same apple");
    let a = Rc::clone(&apple);
    let b = Rc::clone(&apple);
    println!("count after clones: {}", Rc::strong_count(&apple));
    println!("{} {}", a, b);
}
```

```js
// JS: simply assign another variable — GC manages lifetime
const apple = { name: "apple" };
const a = apple;
const b = apple;
```

### 20.9 Arc

`Arc<T>` = atomic reference count — **thread-safe** shared ownership.

```rust
use std::sync::Arc;
use std::thread;

fn main() {
    let apple = Arc::new("the same apple");
    for _ in 0..10 {
        let apple = Arc::clone(&apple);
        thread::spawn(move || {
            println!("{:?}", apple);
        });
    }
    thread::sleep(std::time::Duration::from_secs(1));
}
```

### Smart pointer cheat sheet

| Type | Ownership | Threads | Mutation |
|---|---|---|---|
| `Box<T>` | Unique | Send if T is | via `&mut` if owned mutably |
| `Rc<T>` | Shared | No | needs `RefCell` for mut |
| `Arc<T>` | Shared | Yes | needs `Mutex`/`RwLock` for mut |
| `RefCell<T>` | Interior mutability | No | runtime borrow check |
| `Mutex<T>` | Interior mutability | Yes | lock to mutate |

---

## 21. Std misc

### 21.1 Threads

```rust
use std::thread;

fn main() {
    let handles: Vec<_> = (0..10)
        .map(|i| {
            thread::spawn(move || {
                println!("thread {} starting", i);
                i * i
            })
        })
        .collect();

    for h in handles {
        println!("got {}", h.join().unwrap());
    }
}
```

```js
// Worker threads / child_process — heavier; JS is commonly single-threaded + event loop
```

#### Testcase: map-reduce

```rust
use std::thread;

fn main() {
    let data = "86967897737416471853297327050364959
11861322575564723963297542624962850
70856234701860851907960690014725639
38397966707106094172783238747669219
52380795257888236525459303330302837
58495327135744041048891885726403216
69920238473349127451802398852956338
82985108286223637526978544821396445
73298647274070505940525241641706166
96084772702761292614762031874205602";

    let mut children = vec![];
    let chunked = data.split_whitespace().collect::<Vec<_>>();

    for (i, chunk) in chunked.into_iter().enumerate() {
        children.push(thread::spawn(move || {
            let sum: u32 = chunk
                .chars()
                .map(|c| c.to_digit(10).expect("digit"))
                .sum();
            println!("chunk {} sum {}", i, sum);
            sum
        }));
    }

    let total: u32 = children.into_iter().map(|c| c.join().unwrap()).sum();
    println!("total {}", total);
}
```

### 21.2 Channels

Message passing between threads (like Promise + queue / Node worker messages).

```rust
use std::sync::mpsc;
use std::thread;
use std::time::Duration;

fn main() {
    let (tx, rx) = mpsc::channel();
    thread::spawn(move || {
        let vals = vec!["hi", "from", "the", "thread"];
        for v in vals {
            tx.send(v).unwrap();
            thread::sleep(Duration::from_millis(100));
        }
    });
    for received in rx {
        println!("Got: {}", received);
    }
}
```

### 21.3 Path

```rust
use std::path::Path;

fn main() {
    let path = Path::new(".");
    let display = path.display();
    let mut new_path = path.join("a").join("b");
    new_path.push("c");
    new_path = new_path.with_extension("rs");
    println!("{} -> {}", display, new_path.display());
}
```

```js
import path from "path";
path.join(".", "a", "b", "c.rs");
```

### 21.4 File I/O

#### open

```rust
use std::fs::File;
use std::io::Read;

fn main() -> std::io::Result<()> {
    let mut f = File::open("/etc/hosts")?; // may fail on some OS
    let mut contents = String::new();
    match f.read_to_string(&mut contents) {
        Ok(n) => println!("read {} bytes", n),
        Err(e) => println!("error {}", e),
    }
    Ok(())
}
```

#### create

```rust
use std::fs::File;
use std::io::Write;

fn main() -> std::io::Result<()> {
    let mut f = File::create("/tmp/rust_from_js_demo.txt")?;
    f.write_all(b"Hello from Rust")?;
    Ok(())
}
```

#### read_lines

```rust
use std::fs::File;
use std::io::{BufRead, BufReader};

fn main() -> std::io::Result<()> {
    let f = File::open("/etc/hosts")?;
    for line in BufReader::new(f).lines() {
        println!("{}", line?);
    }
    Ok(())
}
```

Naive vs efficient: prefer buffered readers for large files (don’t load everything if you only need lines).

```js
import fs from "fs";
fs.readFileSync("/etc/hosts", "utf8").split("\n");
```

### 21.5 Child processes

```rust
use std::process::Command;

fn main() {
    let output = Command::new("rustc")
        .arg("--version")
        .output()
        .expect("failed to execute");
    if output.status.success() {
        let s = String::from_utf8_lossy(&output.stdout);
        println!("rustc says: {}", s);
    }
}
```

```js
import { execSync } from "child_process";
execSync("rustc --version", { encoding: "utf8" });
```

#### Pipes

```rust
use std::process::{Command, Stdio};

fn main() {
    let process = match Command::new("wc")
        .stdin(Stdio::piped())
        .stdout(Stdio::piped())
        .spawn()
    {
        Err(why) => panic!("couldn't spawn wc: {}", why),
        Ok(process) => process,
    };
    // write to process.stdin, read process.stdout...
    let _ = process;
}
```

#### Wait

```rust
use std::process::Command;

fn main() {
    let mut child = Command::new("sleep").arg("1").spawn().unwrap();
    let _result = child.wait().unwrap();
    println!("waited");
}
```

### 21.6 Filesystem operations

```rust
use std::fs;
use std::path::Path;

fn main() -> std::io::Result<()> {
    let path = Path::new("/tmp/rust_from_js_dir");
    fs::create_dir_all(path)?;
    fs::write(path.join("a.txt"), b"data")?;
    for entry in fs::read_dir(path)? {
        let entry = entry?;
        println!("{:?}", entry.path());
    }
    fs::remove_file(path.join("a.txt"))?;
    fs::remove_dir(path)?;
    Ok(())
}
```

### 21.7 Program arguments

```rust
use std::env;

fn main() {
    let args: Vec<String> = env::args().collect();
    println!("args: {:?}", args);
    // args[0] is program name
}
```

```js
process.argv
```

#### Argument parsing

For real CLIs use `clap`. Pattern matching on args manually:

```rust
use std::env;

fn main() {
    let args: Vec<String> = env::args().skip(1).collect();
    match args.iter().map(|s| s.as_str()).collect::<Vec<_>>().as_slice() {
        ["help"] => println!("help"),
        ["run", file] => println!("run {}", file),
        other => println!("unknown: {:?}", other),
    }
}
```

### 21.8 Foreign Function Interface (FFI)

Call C from Rust (and vice versa). Like Node N-API / ffi-napi, but with explicit unsafe.

```rust
use std::fmt;

#[link(name = "m")]
extern "C" {
    fn csqrtf(z: Complex) -> Complex;
}

#[repr(C)]
#[derive(Clone, Copy)]
struct Complex {
    re: f32,
    im: f32,
}

impl fmt::Debug for Complex {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        if self.im < 0. {
            write!(f, "{}-{}i", self.re, -self.im)
        } else {
            write!(f, "{}+{}i", self.re, self.im)
        }
    }
}

fn main() {
    let z = Complex { re: -1., im: 0. };
    let z_sqrt = unsafe { csqrtf(z) };
    println!("{:?}", z_sqrt);
}
```

> Note: linking `libm` / platform details vary. Conceptually: `extern "C"` + `unsafe` call.

### Pitfalls

- Always handle `std::io::Error` — disks and paths fail.
- Prefer `BufReader`/`BufWriter` for performance.
- FFI is `unsafe` — validate all boundary data.

---

## 22. Testing

### Plain English

Rust has built-in test support via `#[test]` and `cargo test`. No Jest required for basics.

### JS mental model

| Jest / Node | Rust |
|---|---|
| `test()` / `it()` | `#[test] fn ...` |
| `expect(x).toBe(y)` | `assert_eq!(x, y)` |
| `describe` | modules / files |
| `__tests__` / `.test.js` | `#[cfg(test)]` modules + `tests/` |

### 22.1 Unit testing

```rust
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_add() {
        assert_eq!(add(1, 2), 3);
    }

    #[test]
    fn test_add_negative() {
        assert_eq!(add(-2, 3), 1);
    }

    #[test]
    fn result_based() -> Result<(), String> {
        if add(2, 2) == 4 {
            Ok(())
        } else {
            Err(String::from("2 + 2 != 4"))
        }
    }

    #[test]
    #[should_panic(expected = "divide by zero")]
    fn test_panic() {
        panic!("divide by zero");
    }

    #[test]
    #[ignore]
    fn expensive() {
        // run with cargo test -- --ignored
    }
}
```

```bash
cargo test
cargo test test_add
cargo test -- --ignored
cargo test -- --nocapture
```

### 22.2 Documentation testing

Examples in doc comments are tested:

```rust
/// Adds two numbers.
///
/// ```
/// assert_eq!(my_crate::add(1, 2), 3);
/// ```
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}
```

### 22.3 Integration testing

Files in `tests/*.rs` compile as separate crates linked to your library.

```text
tests/
  integration_test.rs
```

```rust
use my_crate::add;

#[test]
fn it_adds() {
    assert_eq!(add(2, 2), 4);
}
```

### 22.4 Dev-dependencies

```toml
[dev-dependencies]
pretty_assertions = "1"
```

Only used for tests/examples/benchmarks — like `devDependencies`.

### Pitfalls

- Unit tests in the same file can access private items; integration tests cannot.
- Doc tests need a library target (`src/lib.rs`) for best ergonomics.
- Floating point: use approximate compares, not naive `assert_eq!`.

---

## 23. Unsafe operations

### Plain English

`unsafe` lets you do five extra things the compiler cannot verify. It does **not** turn off the borrow checker entirely; it is a deliberate, minimal escape hatch.

### When / why

- Calling FFI
- Building safe abstractions (e.g. implementing `Vec`)
- Performance-critical low-level work
- Raw pointer manipulation

### Raw pointers & unsafe functions

```rust
fn main() {
    let raw_p: *const u32 = &10;
    unsafe {
        assert!(*raw_p == 10);
    }
}

unsafe fn dangerous() {}

fn main2() {
    unsafe {
        dangerous();
    }
}
```

```js
// Closest mental model: writing a native addon where you can segfault
```

### Inline assembly (overview)

`core::arch::asm!` allows inline asm for advanced work (atomics, syscalls, SIMD glue). Rare in application code. See RBE “Inline assembly” for operand/options details when you need it.

```rust
use std::arch::asm;

fn main() {
    let x: u64;
    unsafe {
        asm!("mov {}, 5", out(reg) x);
    }
    assert_eq!(x, 5);
}
```

> Platform-specific; example is illustrative for x86_64.

### Minimal rules for JS devs

1. Don’t start here — master safe Rust first.
2. Keep `unsafe` blocks **small** and document invariants.
3. Prefer existing safe crates over rolling your own unsafe.
4. Never expose unsound APIs as “safe.”

---

## 24. Compatibility & meta

### 24.1 Compatibility — raw identifiers

Keywords can be used as identifiers with `r#`:

```rust
fn r#match(n: i32) -> bool {
    n == 0
}

fn main() {
    println!("{}", r#match(0));
}
```

Useful when binding to foreign APIs that use Rust keywords as names.

### 24.2 Documentation

```rust
/// Documented function. Supports **markdown**.
///
/// # Examples
///
/// ```
/// assert_eq!(2 + 2, 4);
/// ```
pub fn foo() {}

#[doc(hidden)]
pub fn secret() {}
```

```bash
cargo doc --open
```

Doc attributes: `inline`, `no_inline`, `hidden`, etc.

### 24.3 Playground

The [Rust Playground](https://play.rust-lang.org/) runs snippets in the browser — like JSFiddle / CodeSandbox for small examples. RBE embeds playground-powered samples. Great for sharing questions without a local project.

### 24.4 Benchmarks (meta note)

For benches, use `#[bench]` with nightly / or the `criterion` crate on stable — analogous to JS benchmark suites.

```bash
cargo bench
```

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

You now have a full path aligned with Rust by Example — from `cargo new` through ownership, traits, macros, errors, std types, concurrency, tests, and unsafe.

When stuck: shrink the example, read the error, check RBE’s chapter for that topic, then compare with the JS mental model tables here.

Happy hacking, and welcome to Rust. 🦀

---

*Curriculum coverage based on Rust by Example summary/print sources. Prefer the live docs for API details that evolve with the language.*

