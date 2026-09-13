# Hello World, comments & formatting

_RBE chapter root maps here. Every nested leaf below has the full 5-part teaching block._

### 3.1 Hello World

**What it is**

Hello World is the smallest complete Rust program. It prints text to the terminal using the `println!` macro.

**JS mental model**

`println!` is like `console.log`, but it is a **macro** (note the `!`) and formats at compile time.

**Rust example**

```rust
// This is a comment, and is ignored by the compiler.
// You can test this code by clicking the "Run" button over there ->
// or if you prefer to use your keyboard, you can use the "Ctrl + Enter"
// shortcut.

// This code is editable, feel free to hack it!
// You can always return to the original code by clicking the "Reset" button ->

// This is the main function.
fn main() {
    // Statements here are executed when the compiled binary is called.

    // Print text to the console.
    println!("Hello World!");
}
```

**JS equivalent**

```javascript
console.log("Hello World!");
```

**Watch out**

- Forgetting `!` on `println` will not compile.
- Rust programs need a `fn main()` entry point like a script’s top-level code.

_RBE source: `hello.md`_

---

### 3.1.1 Comments

**What it is**

Any program requires comments, and Rust supports
a few different varieties:
Regular Comments
These are ignored by the compiler:
* **Line comments**: Start with `//` and continue to the end of the line
* **Block comments**: Enclosed in `/* ... */` and can span multiple lines
Documentation Comments (Doc Comments) which are parsed into HTML library [documentation][docs]:
 - `///` - Generates docs for the item that follows it
- `//!` - Generates docs for the enclosing item (typically used at the top of a file or module)
See also:
[Library documentation][docs]
[docs]: ../meta/doc.md.

**JS mental model**

Same idea as JS `//` and `/* */`. Rust also has `///` and `//!` for docs that become HTML docs.

**Rust example**

```rust
fn main() {
    // Line comments start with two slashes.
    // Everything after the slashes is ignored by the compiler.

    // Example: This line won't execute
    // println!("Hello, world!");

    // Try removing the slashes above and running the code again.

    /*
      Block comments are useful for temporarily disabling code.
      They can also be nested: /* like this */ which makes it easy
      to comment out large sections quickly.
     */

    /*
     * Note: The asterisk column on the left is just for style - 
     * it's not required by the language.
     */

    // Block comments make it easy to toggle code on/off by adding
    // or removing just one slash:

    /* <- Add a '/' here to uncomment the entire block below

    println!("Now");
    println!("everything");
    println!("executes!");
    // Line comments inside remain unaffected

    // */

    // Block comments can also be used within expressions:
    let x = 5 + /* 90 + */ 5;
    println!("Is `x` 10 or 100? x = {}", x);
}
```

**JS equivalent**

```javascript
// line comment
/* block comment */
```

**Watch out**

- `///` documents the next item; `//!` documents the enclosing module/crate.
- Doc comments are markdown.

_RBE source: `hello/comment.md`_

---

### 3.1.2 Formatted print

**What it is**

Printing is handled by a series of [`macros`][macros] defined in
[`std::fmt`][fmt] some of which are:
* `format!`: write formatted text to [`String`][string]
* `print!`: same as `format!` but the text is printed to the console
  (io::stdout). * `println!`: same as `print!` but a newline is appended. * `eprint!`: same as `print!` but the text is printed to the standard error
  (io::stderr). * `eprintln!`: same as `eprint!` but a newline is appended.

**JS mental model**

`console.log(a, b)` vs Rust format strings: `println!("{} {}", a, b)`. Positional and named args exist too.

**Rust example**

```rust
fn main() {
    // In general, the `{}` will be automatically replaced with any
    // arguments. These will be stringified.
    println!("{} days", 31);

    // Positional arguments can be used. Specifying an integer inside `{}`
    // determines which additional argument will be replaced. Arguments start
    // at 0 immediately after the format string.
    println!("{0}, this is {1}. {1}, this is {0}", "Alice", "Bob");

    // As can named arguments.
    println!("{subject} {verb} {object}",
             object="the lazy dog",
             subject="the quick brown fox",
             verb="jumps over");

    // Different formatting can be invoked by specifying the format character
    // after a `:`.
    println!("Base 10:               {}",   69420); // 69420
    println!("Base 2 (binary):       {:b}", 69420); // 10000111100101100
    println!("Base 8 (octal):        {:o}", 69420); // 207454
    println!("Base 16 (hexadecimal): {:x}", 69420); // 10f2c

    // You can right-justify text with a specified width. This will
    // output "    1". (Four white spaces and a "1", for a total width of 5.)
    println!("{number:>5}", number=1);

    // You can pad numbers with extra zeroes,
    println!("{number:0>5}", number=1); // 00001
    // and left-adjust by flipping the sign. This will output "10000".
    println!("{number:0<5}", number=1); // 10000

    // You can use named arguments in the format specifier by appending a `$`.
    println!("{number:0>width$}", number=1, width=5);

    // Rust even checks to make sure the correct number of arguments are used.
    println!("My name is {0}, {1} {0}", "Bond");
    // FIXME ^ Add the missing argument: "James"

    // Only types that implement fmt::Display can be formatted with `{}`. User-
    // defined types do not implement fmt::Display by default.

    #[allow(dead_code)] // disable `dead_code` which warn against unused module
    struct Structure(i32);

    // This will not compile because `Structure` does not implement
    // fmt::Display.
    // println!("This struct `{}` won't print...", Structure(3));
    // TODO ^ Try uncommenting this line

    // For Rust 1.58 and above, you can directly capture the argument from a
    // surrounding variable. Just like the above, this will output
    // "    1", 4 white spaces and a "1".
    let number: f64 = 1.0;
    let width: usize = 5;
    println!("{number:>width$}");
}
```

**JS equivalent**

```javascript
console.log(`hello ${name}`);
```

**Watch out**

- Format arity is checked at compile time — wrong number of args = error.
- `print!` has no newline; `println!` does.

_RBE source: `hello/print.md`_

---

#### 3.1.2.1 Debug

**What it is**

All types which want to use `std::fmt` formatting `traits` require an
implementation to be printable. Automatic implementations are only provided
for types such as in the `std` library. All others *must* be manually
implemented somehow. The `fmt::Debug` `trait` makes this very straightforward.

**JS mental model**

`{:?}` is like a built-in rough `JSON.stringify` for debugging. Derive `Debug` to opt in.

**Rust example**

```rust
// Derive the `fmt::Debug` implementation for `Structure`. `Structure`
// is a structure which contains a single `i32`.
#[derive(Debug)]
struct Structure(i32);

// Put a `Structure` inside of the structure `Deep`. Make it printable
// also.
#[derive(Debug)]
struct Deep(Structure);

fn main() {
    // Printing with `{:?}` is similar to with `{}`.
    println!("{:?} months in a year.", 12);
    println!("{1:?} {0:?} is the {actor:?} name.",
             "Slater",
             "Christian",
             actor="actor's");

    // `Structure` is printable!
    println!("Now {:?} will print!", Structure(3));

    // The problem with `derive` is there is no control over how
    // the results look. What if I want this to just show a `7`?
    println!("Now {:?} will print!", Deep(Structure(7)));
}
```

```rust
// This structure cannot be printed either with `fmt::Display` or
// with `fmt::Debug`.
struct UnPrintable(i32);

// The `derive` attribute automatically creates the implementation
// required to make this `struct` printable with `fmt::Debug`.
#[derive(Debug)]
struct DebugPrintable(i32);
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- `Debug` is for programmers; `Display` is for end users.
- Many std types already implement `Debug`.

_RBE source: `hello/print/print_debug.md`_

---

#### 3.1.2.2 Display

**What it is**

`fmt::Debug` hardly looks compact and clean, so it is often advantageous to
customize the output appearance. This is done by manually implementing
[`fmt::Display`][fmt], which uses the `{}` print marker. Implementing it
looks like this:
`fmt::Display` may be cleaner than `fmt::Debug` but this presents
a problem for the `std` library. How should ambiguous types be displayed?

**JS mental model**

Implementing `Display` is like writing a custom `toString()` / `util.inspect` for users.

**Rust example**

```rust
use std::fmt; // Import `fmt`

// A structure holding two numbers. `Debug` will be derived so the results can
// be contrasted with `Display`.
#[derive(Debug)]
struct MinMax(i64, i64);

// Implement `Display` for `MinMax`.
impl fmt::Display for MinMax {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        // Use `self.number` to refer to each positional data point.
        write!(f, "({}, {})", self.0, self.1)
    }
}

// Define a structure where the fields are nameable for comparison.
#[derive(Debug)]
struct Point2D {
    x: f64,
    y: f64,
}

// Similarly, implement `Display` for `Point2D`.
impl fmt::Display for Point2D {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        // Customize so only `x` and `y` are denoted.
        write!(f, "x: {}, y: {}", self.x, self.y)
    }
}

fn main() {
    let minmax = MinMax(0, 14);

    println!("Compare structures:");
    println!("Display: {}", minmax);
    println!("Debug: {:?}", minmax);

    let big_range =   MinMax(-300, 300);
    let small_range = MinMax(-3, 3);

    println!("The big range is {big} and the small is {small}",
             small = small_range,
             big = big_range);

    let point = Point2D { x: 3.3, y: 7.2 };

    println!("Compare points:");
    println!("Display: {}", point);
    println!("Debug: {:?}", point);

    // The following line would not compile: both `Debug` and `Display`
    // were implemented, but `{:b}` requires `fmt::Binary` to be
    // implemented, which it hasn't been for `Point2D`.
    // println!("What does Point2D look like in binary: {:b}?", point);
}
```

```rust
// Import (via `use`) the `fmt` module to make it available.
use std::fmt;

// Define a structure for which `fmt::Display` will be implemented. This is
// a tuple struct named `Structure` that contains an `i32`.
struct Structure(i32);

// To use the `{}` marker, the trait `fmt::Display` must be implemented
// manually for the type.
impl fmt::Display for Structure {
    // This trait requires `fmt` with this exact signature.
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        // Write strictly the first element into the supplied output
        // stream: `f`. Returns `fmt::Result` which indicates whether the
        // operation succeeded or failed. Note that `write!` uses syntax which
        // is very similar to `println!`.
        write!(f, "{}", self.0)
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- `Display` uses `{}`; `Debug` uses `{:?}`.
- You cannot `derive(Display)` — implement it yourself.

_RBE source: `hello/print/print_display.md`_

---

##### 3.1.2.2.1 Testcase: List

**What it is**

Implementing `fmt::Display` for a structure where the elements must each be
handled sequentially is tricky. The problem is that each `write!` generates a
`fmt::Result`. Proper handling of this requires dealing with *all* the
results. Rust provides the `?` operator for exactly this purpose.

**JS mental model**

Like writing a custom stringifier for an array of values.

**Rust example**

```rust
use std::fmt; // Import the `fmt` module.

// Define a structure named `List` containing a `Vec`.
struct List(Vec<i32>);

impl fmt::Display for List {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        // Create a reference to the Vec<i32> stored in the List struct.
        let vec = &self.0;

        write!(f, "[")?;

        // Iterate over `v` in `vec` while enumerating the iteration
        // index in `index`.
        for (index, v) in vec.iter().enumerate() {
            // For every element except the first, add a comma.
            // Use the ? operator to return on errors.
            if index != 0 { write!(f, ", ")?; }
            write!(f, "{}", v)?;
        }

        // Close the opened bracket and return a fmt::Result value.
        write!(f, "]")
    }
}

fn main() {
    let v = List(vec![1, 2, 3]);
    println!("{}", v);
}
```

```rust
// Try `write!` to see if it errors. If it errors, return
// the error. Otherwise continue.
write!(f, "{}", value)?;
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Inside `fmt`, use `write!` to compose output.
- Return `fmt::Result`.

_RBE source: `hello/print/print_display/testcase_list.md`_

---

#### 3.1.2.3 Formatting

**What it is**

We've seen that formatting is specified via a *format string*:
* `format!("{}", foo)` -> `"3735928559"`
* `format!("0x{:X}", foo)` -> [`"0xDEADBEEF"`][deadbeef]
* `format!("0o{:o}", foo)` -> `"0o33653337357"`
The same variable (`foo`) can be formatted differently depending on which
*argument type* is used: `X` vs `o` vs *unspecified*. This formatting functionality is implemented via traits, and there is one trait
for each argument type. The most common formatting trait is `Display`, which
handles cases where the argument type is left unspecified: `{}` for instance. You can view a [full list of formatting traits][fmt_traits] and their argument
types in the [`std::fmt`][fmt] documentation.

**JS mental model**

Similar to `printf` / template literals with padding: `{:>5}`, `{:.2}`, hex, binary, etc.

**Rust example**

```rust
use std::fmt::{self, Formatter, Display};

struct City {
    name: &'static str,
    // Latitude
    lat: f32,
    // Longitude
    lon: f32,
}

impl Display for City {
    // `f` is a buffer, and this method must write the formatted string into it.
    fn fmt(&self, f: &mut Formatter) -> fmt::Result {
        let lat_c = if self.lat >= 0.0 { 'N' } else { 'S' };
        let lon_c = if self.lon >= 0.0 { 'E' } else { 'W' };

        // `write!` is like `format!`, but it will write the formatted string
        // into a buffer (the first argument).
        write!(f, "{}: {:.3}°{} {:.3}°{}",
               self.name, self.lat.abs(), lat_c, self.lon.abs(), lon_c)
    }
}

#[derive(Debug)]
struct Color {
    red: u8,
    green: u8,
    blue: u8,
}

fn main() {
    for city in [
        City { name: "Dublin", lat: 53.347778, lon: -6.259722 },
        City { name: "Oslo", lat: 59.95, lon: 10.75 },
        City { name: "Vancouver", lat: 49.25, lon: -123.1 },
    ] {
        println!("{}", city);
    }
    for color in [
        Color { red: 128, green: 255, blue: 90 },
        Color { red: 0, green: 3, blue: 254 },
        Color { red: 0, green: 0, blue: 0 },
    ] {
        // Switch this to use {} once you've added an implementation
        // for fmt::Display.
        println!("{:?}", color);
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Compile-time checked format strings catch typos early.
- Named parameters: `println!("{name}", name=value)`.

_RBE source: `hello/print/fmt.md`_

---
