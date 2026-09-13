# Quick JS↔Rust cheatsheet appendix

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
