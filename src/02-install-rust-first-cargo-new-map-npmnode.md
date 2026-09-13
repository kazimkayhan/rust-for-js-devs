# Install Rust + first `cargo new` (map npm/node)

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
