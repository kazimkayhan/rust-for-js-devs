# Testing

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
