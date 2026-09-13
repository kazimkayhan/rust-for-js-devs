# Welcome & how to use this guide

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
