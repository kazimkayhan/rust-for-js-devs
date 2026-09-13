# Crates & Cargo

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
