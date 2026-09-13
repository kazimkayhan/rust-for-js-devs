# Rust from JavaScript: Zero to Hero

A single, comprehensive Markdown tutorial that teaches **Rust from scratch to advanced topics**, written for developers who already know **JavaScript (ES6+)**.

## Main guide

→ **[RUST_FROM_JS_ZERO_TO_HERO.md](./RUST_FROM_JS_ZERO_TO_HERO.md)** (~13.5k lines)

It follows the curriculum of the official **[Rust by Example](https://doc.rust-lang.org/rust-by-example/)** book and explains **every SUMMARY leaf** (including all nested sub-topics) with:

1. **What it is** — plain English
2. **JS mental model** — analogy for JavaScript developers
3. **Rust example** — runnable-style code (edition 2021+)
4. **JS equivalent** — when it helps
5. **Watch out** — common JS-dev pitfalls

## Full nested coverage

This is not a skim of top-level chapters only. The guide deepens nested headings so **every path** in Rust by Example’s `SUMMARY.md` is findable as its own subsection.

See **[`COVERAGE.md`](./COVERAGE.md)** for the checklist: each RBE leaf marked **DONE** with the guide heading used.

Also mirrored: [`rbe-SUMMARY.md`](./rbe-SUMMARY.md).

## What's covered

All Rust by Example chapters and nested leaves, including:

- Hello World, formatting (`Debug` / `Display`), primitives
- Structs, enums, constants, bindings, types, conversion, **expressions**
- Full flow control (`match` destructuring leaves, guards, `if let`, `let-else`, …)
- Functions, methods, closures (every closure leaf), HOFs
- Modules, **crates** (creating + using a library), **Cargo** (deps, conventions, tests, build scripts)
- Attributes (`dead_code`, crate attrs, `cfg`, custom cfg)
- Generics, ownership/borrowing/**lifetimes** (every lifetime leaf)
- Traits, **macros** (syntax, designators, overload, repeat, DRY, DSL, variadics)
- Error handling (every `Option` / `Result` / multiple-error leaf)
- Std types (`Box`, `Vec`, strings, `HashMap` alt keys, `HashSet`, `Rc`, `Arc`, …)
- Std misc (threads + map-reduce, channels, path, file I/O, processes, fs, args, FFI)
- Testing (unit, doc, integration, dev-dependencies)
- Unsafe + **inline assembly**, raw identifiers, docs, playground
- Install / `cargo new` mapped to npm/node, practice checklist, JS↔Rust cheatsheet

## Official companions

- **Rust by Example:** https://doc.rust-lang.org/rust-by-example/
- **The Rust Book:** https://doc.rust-lang.org/book/
- **Install toolchain:** https://rustup.rs

## How to use

1. Install Rust with `rustup`.
2. Open `RUST_FROM_JS_ZERO_TO_HERO.md` and work section by section (use `COVERAGE.md` to jump by RBE path).
3. Create a practice crate (`cargo new rust_playground`) and type the examples yourself.
4. Spend extra time on **ownership, borrowing, and lifetimes** (section 16).
5. Follow the checklist in section 25.

## Attribution

Tutorial text is original teaching material structured around the public Rust by Example curriculum.
Rust and Rust by Example are projects of the Rust Project / Rust Foundation.
