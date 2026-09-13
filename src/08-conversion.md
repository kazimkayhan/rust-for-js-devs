# Conversion

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
