# Practice path / checklist

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
