# Macros

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
