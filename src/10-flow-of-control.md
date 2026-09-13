# Flow of Control

_RBE chapter root maps here. Every nested leaf below has the full 5-part teaching block._

### 10.1 Flow of Control

**What it is**

An integral part of any programming language are ways to modify control flow:
`if`/`else`, `for`, and others. Let's talk about them in Rust.

**JS mental model**

Same control flow vocabulary as JS, plus powerful `match` and loop labels.

**Rust example**

```rust
fn main() {
    let n = 5;
    if n > 0 { println!("positive"); }
    for i in 0..3 { println!("{i}"); }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- `if` is an expression — both branches must match types when used as a value.

_RBE source: `flow_control.md`_

---

### 10.1.1 if/else

**What it is**

Branching with `if`-`else` is similar to other languages. Unlike many of them,
the boolean condition doesn't need to be surrounded by parentheses, and each
condition is followed by a block. `if`-`else` conditionals are expressions,
and, all branches must return the same type.

**JS mental model**

Like JS ternary / if, but can return values: `let x = if c {1} else {2};`

**Rust example**

```rust
fn main() {
    let n = 5;

    if n < 0 {
        print!("{} is negative", n);
    } else if n > 0 {
        print!("{} is positive", n);
    } else {
        print!("{} is zero", n);
    }

    let big_n =
        if n < 10 && n > -10 {
            println!(", and is a small number, increase ten-fold");

            // This expression returns an `i32`.
            10 * n
        } else {
            println!(", and is a big number, halve the number");

            // This expression must return an `i32` as well.
            n / 2
            // TODO ^ Try suppressing this expression with a semicolon.
        };
    //   ^ Don't forget to put a semicolon here! All `let` bindings need it.

    println!("{} -> {}", n, big_n);
}
```

**JS equivalent**

```javascript
const n = x > 0 ? "pos" : "non-pos";
```

**Watch out**

- No truthy/falsy — condition must be `bool`.
- Parentheses around conditions are optional (and uncommon).

_RBE source: `flow_control/if_else.md`_

---

### 10.1.2 loop

**What it is**

Rust provides a `loop` keyword to indicate an infinite loop. The `break` statement can be used to exit a loop at anytime, whereas the
`continue` statement can be used to skip the rest of the iteration and start a
new one.

**JS mental model**

Infinite loop with `loop { }` — clearer than `while(true)`.

**Rust example**

```rust
fn main() {
    let mut count = 0u32;

    println!("Let's count until infinity!");

    // Infinite loop
    loop {
        count += 1;

        if count == 3 {
            println!("three");

            // Skip the rest of this iteration
            continue;
        }

        println!("{}", count);

        if count == 5 {
            println!("OK, that's enough");

            // Exit this loop
            break;
        }
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Use `break` / `continue`.
- Can `break value` to return a value from the loop.

_RBE source: `flow_control/loop.md`_

---

#### 10.1.2.1 Nesting and labels

**What it is**

It's possible to `break` or `continue` outer loops when dealing with nested
loops. In these cases, the loops must be annotated with some `'label`, and the
label must be passed to the `break`/`continue` statement.

**JS mental model**

Labeled loops: `'outer: loop` like labeled statements in JS (`outer: while…`).

**Rust example**

```rust
#![allow(unreachable_code, unused_labels)]

fn main() {
    'outer: loop {
        println!("Entered the outer loop");

        'inner: loop {
            println!("Entered the inner loop");

            // This would break only the inner loop
            //break;

            // This breaks the outer loop
            break 'outer;
        }

        println!("This point will never be reached");
    }

    println!("Exited the outer loop");
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Labels start with a quote: `'label`.
- `break 'outer` exits the labeled loop.

_RBE source: `flow_control/loop/nested.md`_

---

#### 10.1.2.2 Returning from loops

**What it is**

One of the uses of a `loop` is to retry an operation until it succeeds. If the
operation returns a value though, you might need to pass it to the rest of the
code: put it after the `break`, and it will be returned by the `loop`
expression.

**JS mental model**

`break expr` makes the `loop` expression equal `expr` — no JS equivalent built-in.

**Rust example**

```rust
fn main() {
    let mut counter = 0;

    let result = loop {
        counter += 1;

        if counter == 10 {
            break counter * 2;
        }
    };

    assert_eq!(result, 20);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Only `loop` (not `while`/`for`) can break with a value in stable idioms shown here.

_RBE source: `flow_control/loop/return.md`_

---

### 10.1.3 while

**What it is**

The `while` keyword can be used to run a loop while a condition is true. Let's write the infamous [FizzBuzz][fizzbuzz] using a `while` loop. [fizzbuzz]: https://en.wikipedia.org/wiki/Fizz_buzz.

**JS mental model**

Same idea as JS `while`.

**Rust example**

```rust
fn main() {
    // A counter variable
    let mut n = 1;

    // Loop while `n` is less than 101
    while n < 101 {
        if n % 15 == 0 {
            println!("fizzbuzz");
        } else if n % 3 == 0 {
            println!("fizz");
        } else if n % 5 == 0 {
            println!("buzz");
        } else {
            println!("{}", n);
        }

        // Increment counter
        n += 1;
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Prefer iterators/`for` when walking collections.

_RBE source: `flow_control/while.md`_

---

### 10.1.4 for and range

**What it is**

for and range
The `for in` construct can be used to iterate through an `Iterator`. One of the easiest ways to create an iterator is to use the range
notation `a..b`. This yields values from `a` (inclusive) to `b`
(exclusive) in steps of one. Let's write FizzBuzz using `for` instead of `while`.

**JS mental model**

`for x in xs` iterates by value/into_iter; use `.iter()` / `.iter_mut()` like choosing for-of vs indices.

**Rust example**

```rust
fn main() {
    let names = vec!["Bob", "Frank", "Ferris"];

    for name in names.into_iter() {
        match name {
            "Ferris" => println!("There is a rustacean among us!"),
            _ => println!("Hello {}", name),
        }
    }

    // `names` has been 'moved' and can no longer be used.
    // Try uncommenting the line below to see the compiler error:
    // println!("names: {:?}", names);
}
```

```rust
fn main() {
    let names = vec!["Bob", "Frank", "Ferris"];

    for name in names.iter() {
        match name {
            &"Ferris" => println!("There is a rustacean among us!"),
            // TODO ^ Try deleting the & and matching just "Ferris"
            _ => println!("Hello {}", name),
        }
    }

    println!("names: {:?}", names);
}
```

**JS equivalent**

```javascript
for (const n of [1,2,3]) console.log(n);
for (let i = 0; i < 5; i++) {}
```

**Watch out**

- `for i in 0..n` is a range (end exclusive).
- `0..=n` is inclusive end.

_RBE source: `flow_control/for.md`_

---

### 10.1.5 match

**What it is**

Rust provides pattern matching via the `match` keyword, which can be used like
a C `switch`. The first matching arm is evaluated and all possible values must be
covered.

**JS mental model**

Superpowered `switch` + destructuring. Exhaustiveness checked — missing cases are compile errors.

**Rust example**

```rust
fn main() {
    let number = 13;
    // TODO ^ Try different values for `number`

    println!("Tell me about {}", number);
    match number {
        // Match a single value
        1 => println!("One!"),
        // Match several values
        2 | 3 | 5 | 7 | 11 => println!("This is a prime"),
        // TODO ^ Try adding 13 to the list of prime values
        // Match an inclusive range
        13..=19 => println!("A teen"),
        // Handle the rest of cases
        _ => println!("Ain't special"),
        // TODO ^ Try commenting out this catch-all arm
    }

    let boolean = true;
    // Match is an expression too
    let binary = match boolean {
        // The arms of a match must cover all the possible values
        false => 0,
        true => 1,
        // TODO ^ Try commenting out one of these arms
    };

    println!("{} -> {}", boolean, binary);
}
```

**JS equivalent**

```javascript
switch (n) {
  case 1: break;
  default: break;
} // no exhaustiveness check
```

**Watch out**

- Patterns are not fall-through.
- Use `|` for or-patterns; `guards` for extra bool checks.

_RBE source: `flow_control/match.md`_

---

#### 10.1.5.1 Destructuring

**What it is**

A `match` block can destructure items in a variety of ways. * [Destructuring Tuples][tuple]
* [Destructuring Arrays and Slices][slice]
* [Destructuring Enums][enum]
* [Destructuring Pointers][refs]
* [Destructuring Structures][struct]
[enum]: destructuring/destructure_enum.md
[refs]: destructuring/destructure_pointers.md
[struct]: destructuring/destructure_structures.md
[tuple]: destructuring/destructure_tuple.md
[slice]: destructuring/destructure_slice.md
See also:
The Rust Reference for Destructuring.

**JS mental model**

Destructure inside match like nested object/array destructuring in JS `switch` — but safer and deeper.

**Rust example**

```rust
fn main() {
    let pair = (0, -2);
    match pair {
        (0, y) => println!("y={y}"),
        (x, 0) => println!("x={x}"),
        _ => println!("other"),
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Every variant/shape you care about gets its own arm.

_RBE source: `flow_control/match/destructuring.md`_

---

##### 10.1.5.1.1 tuples

**What it is**

Tuples can be destructured in a `match` as follows:
See also:
Tuples.

**JS mental model**

Like `const [a,b] = tuple` inside a match arm.

**Rust example**

```rust
fn main() {
    let triple = (0, -2, 3);
    // TODO ^ Try different values for `triple`

    println!("Tell me about {:?}", triple);
    // Match can be used to destructure a tuple
    match triple {
        // Destructure the second and third elements
        (0, y, z) => println!("First is `0`, `y` is {:?}, and `z` is {:?}", y, z),
        (1, ..)  => println!("First is `1` and the rest doesn't matter"),
        (.., 2)  => println!("last is `2` and the rest doesn't matter"),
        (3, .., 4)  => println!("First is `3`, last is `4`, and the rest doesn't matter"),
        // `..` can be used to ignore the rest of the tuple
        _      => println!("It doesn't matter what they are"),
        // `_` means don't bind the value to a variable
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- `_` ignores a part; `..` ignores the rest in longer patterns.

_RBE source: `flow_control/match/destructuring/destructure_tuple.md`_

---

##### 10.1.5.1.2 arrays/slices

**What it is**

Like tuples, arrays and slices can be destructured this way:
See also:
Arrays and Slices and Binding for `@` sigil.

**JS mental model**

Match on array/slice shapes: `[a, b, ..]` similar to rest syntax.

**Rust example**

```rust
fn main() {
    // Try changing the values in the array, or make it a slice!
    let array = [1, -2, 6];

    match array {
        // Binds the second and the third elements to the respective variables
        [0, second, third] =>
            println!("array[0] = 0, array[1] = {}, array[2] = {}", second, third),

        // Single values can be ignored with _
        [1, _, third] => println!(
            "array[0] = 1, array[2] = {} and array[1] was ignored",
            third
        ),

        // You can also bind some and ignore the rest
        [-1, second, ..] => println!(
            "array[0] = -1, array[1] = {} and all the other ones were ignored",
            second
        ),
        // The code below would not compile
        // [-1, second] => ...

        // Or store them in another array/slice (the type depends on
        // that of the value that is being matched against)
        [3, second, tail @ ..] => println!(
            "array[0] = 3, array[1] = {} and the other elements were {:?}",
            second, tail
        ),

        // Combining these patterns, we can, for example, bind the first and
        // last values, and store the rest of them in a single array
        [first, middle @ .., last] => println!(
            "array[0] = {}, middle = {:?}, array[2] = {}",
            first, middle, last
        ),
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Length-sensitive patterns are checked.

_RBE source: `flow_control/match/destructuring/destructure_slice.md`_

---

##### 10.1.5.1.3 enums

**What it is**

An `enum` is destructured similarly:
See also:
[`#[allow(...)]`][allow], [color models][color_models] and [`enum`][enum]
[allow]: ../../../attribute/unused.md
[color_models]: https://en.wikipedia.org/wiki/Color_model
[enum]: ../../../custom_types/enum.md.

**JS mental model**

Like matching a TS discriminated union: `Msg::Move { x, y } => …`.

**Rust example**

```rust
// `allow` required to silence warnings because only
// one variant is used.
#[allow(dead_code)]
enum Color {
    // These 3 are specified solely by their name.
    Red,
    Blue,
    Green,
    // These likewise tie `u32` tuples to different names: color models.
    RGB(u32, u32, u32),
    HSV(u32, u32, u32),
    HSL(u32, u32, u32),
    CMY(u32, u32, u32),
    CMYK(u32, u32, u32, u32),
}

fn main() {
    let color = Color::RGB(122, 17, 40);
    // TODO ^ Try different variants for `color`

    println!("What color is it?");
    // An `enum` can be destructured using a `match`.
    match color {
        Color::Red   => println!("The color is Red!"),
        Color::Blue  => println!("The color is Blue!"),
        Color::Green => println!("The color is Green!"),
        Color::RGB(r, g, b) =>
            println!("Red: {}, green: {}, and blue: {}!", r, g, b),
        Color::HSV(h, s, v) =>
            println!("Hue: {}, saturation: {}, value: {}!", h, s, v),
        Color::HSL(h, s, l) =>
            println!("Hue: {}, saturation: {}, lightness: {}!", h, s, l),
        Color::CMY(c, m, y) =>
            println!("Cyan: {}, magenta: {}, yellow: {}!", c, m, y),
        Color::CMYK(c, m, y, k) =>
            println!("Cyan: {}, magenta: {}, yellow: {}, key (black): {}!",
                c, m, y, k),
        // Don't need another arm because all variants have been examined
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- After move-out of data from an enum, ownership rules apply.

_RBE source: `flow_control/match/destructuring/destructure_enum.md`_

---

##### 10.1.5.1.4 pointers/ref

**What it is**

For pointers, a distinction needs to be made between destructuring
and dereferencing as they are different concepts which are used
differently from languages like C/C++. * Dereferencing uses `*`
* Destructuring uses `&`, `ref`, and `ref mut`
See also:
The ref pattern.

**JS mental model**

Matching references uses `ref` / `ref mut` or `&` patterns — no JS pointer concept.

**Rust example**

```rust
fn main() {
    // Assign a reference of type `i32`. The `&` signifies there
    // is a reference being assigned.
    let reference = &4;

    match reference {
        // If `reference` is pattern matched against `&val`, it results
        // in a comparison like:
        // `&i32`
        // `&val`
        // ^ We see that if the matching `&`s are dropped, then the `i32`
        // should be assigned to `val`.
        &val => println!("Got a value via destructuring: {:?}", val),
    }

    // To avoid the `&`, you dereference before matching.
    match *reference {
        val => println!("Got a value via dereferencing: {:?}", val),
    }

    // What if you don't start with a reference? `reference` was a `&`
    // because the right side was already a reference. This is not
    // a reference because the right side is not one.
    let _not_a_reference = 3;

    // Rust provides `ref` for exactly this purpose. It modifies the
    // assignment so that a reference is created for the element; this
    // reference is assigned.
    let ref _is_a_reference = 3;

    // Accordingly, by defining 2 values without references, references
    // can be retrieved via `ref` and `ref mut`.
    let value = 5;
    let mut mut_value = 6;

    // Use `ref` keyword to create a reference.
    match value {
        ref r => println!("Got a reference to a value: {:?}", r),
    }

    // Use `ref mut` similarly.
    match mut_value {
        ref mut m => {
            // Got a reference. Gotta dereference it before we can
            // add anything to it.
            *m += 10;
            println!("We added 10. `mut_value`: {:?}", m);
        },
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Prefer modern `&` patterns; know `ref` when reading older code.
- Deref matching can be subtle — read compiler suggestions.

_RBE source: `flow_control/match/destructuring/destructure_pointers.md`_

---

##### 10.1.5.1.5 structs

**What it is**

Similarly, a `struct` can be destructured as shown:
See also:
Structs.

**JS mental model**

Object destructuring: `Person { name, age } => …`.

**Rust example**

```rust
fn main() {
    struct Foo {
        x: (u32, u32),
        y: u32,
    }

    // Try changing the values in the struct to see what happens
    let foo = Foo { x: (1, 2), y: 3 };

    match foo {
        Foo { x: (1, b), y } => println!("First of x is 1, b = {},  y = {} ", b, y),

        // you can destructure structs and rename the variables,
        // the order is not important
        Foo { y: 2, x: i } => println!("y is 2, i = {:?}", i),

        // and you can also ignore some variables:
        Foo { y, .. } => println!("y = {}, we don't care about x", y),
        // this will give an error: pattern does not mention field `x`
        //Foo { y } => println!("y = {}", y),
    }

    let faa = Foo { x: (1, 2), y: 3 };

    // You do not need a match block to destructure structs:
    let Foo { x : x0, y: y0 } = faa;
    println!("Outside: x0 = {x0:?}, y0 = {y0}");

    // Destructuring works with nested structs as well:
    struct Bar {
        foo: Foo,
    }

    let bar = Bar { foo: faa };
    let Bar { foo: Foo { x: nested_x, y: nested_y } } = bar;
    println!("Nested: nested_x = {nested_x:?}, nested_y = {nested_y:?}");
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- `..` ignores remaining fields.
- Rename: `name: n`.

_RBE source: `flow_control/match/destructuring/destructure_structures.md`_

---

#### 10.1.5.2 Guards

**What it is**

A `match` *guard* can be added to filter the arm. Note that the compiler won't take guard conditions into account when checking
if all patterns are covered by the match expression. See also:
Tuples
Enums.

**JS mental model**

Match arm + `if condition` — like `case` with an extra if.

**Rust example**

```rust
#[allow(dead_code)]
enum Temperature {
    Celsius(i32),
    Fahrenheit(i32),
}

fn main() {
    let temperature = Temperature::Celsius(35);
    // ^ TODO try different values for `temperature`

    match temperature {
        Temperature::Celsius(t) if t > 30 => println!("{}C is above 30 Celsius", t),
        // The `if condition` part ^ is a guard
        Temperature::Celsius(t) => println!("{}C is equal to or below 30 Celsius", t),

        Temperature::Fahrenheit(t) if t > 86 => println!("{}F is above 86 Fahrenheit", t),
        Temperature::Fahrenheit(t) => println!("{}F is equal to or below 86 Fahrenheit", t),
    }
}
```

```rust
fn main() {
    let number: u8 = 4;

    match number {
        i if i == 0 => println!("Zero"),
        i if i > 0 => println!("Greater than zero"),
        // _ => unreachable!("Should never happen."),
        // TODO ^ uncomment to fix compilation
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Guards cannot introduce new bindings the same way patterns do — bind first, then guard.

_RBE source: `flow_control/match/guard.md`_

---

#### 10.1.5.3 Binding

**What it is**

Indirectly accessing a variable makes it impossible to branch and use that
variable without re-binding. `match` provides the `@` sigil for binding values to
names:
You can also use binding to "destructure" `enum` variants, such as `Option`:
See also:
[`functions`][functions], [`enums`][enums] and [`Option`][option]
[functions]: ../../fn.md
[enums]: ../../custom_types/enum.md
[option]: ../../std/option.md.

**JS mental model**

`@` binds while also matching a pattern: `n @ 1..=5` — bind the value and check range.

**Rust example**

```rust
// A function `age` which returns a `u32`.
fn age() -> u32 {
    15
}

fn main() {
    println!("Tell me what type of person you are");

    match age() {
        0             => println!("I haven't celebrated my first birthday yet"),
        // Could `match` 1 ..= 12 directly but then what age
        // would the child be?
        // Could `match` n and use an `if` guard, but would
        // not contribute to exhaustiveness checks.
        // (Although in this case that would not matter since
        // a "catch-all" pattern is present at the bottom)
        // Instead, bind to `n` for the sequence of 1 ..= 12.
        // Now the age can be reported.
        n @ 1  ..= 12 => println!("I'm a child of age {:?}", n),
        n @ 13 ..= 19 => println!("I'm a teen of age {:?}", n),
        // A similar binding can be done when matching several values.
        n @ (1 | 7 | 15 | 13) => println!("I'm a teen of age {:?}", n),
        // Nothing bound. Return the result.
        n             => println!("I'm an old person of age {:?}", n),
    }
}
```

```rust
fn some_number() -> Option<u32> {
    Some(42)
}

fn main() {
    match some_number() {
        // Got `Some` variant, match if its value, bound to `n`,
        // is equal to 42.
        // Could also use `Some(42)` and print `"The Answer: 42!"`
        // but that would require changing `42` in 2 spots should
        // you ever wish to change it.
        // Could also use `Some(n) if n == 42` and print `"The Answer: {n}!"`
        // but that would not contribute to exhaustiveness checks.
        // (Although in this case that would not matter since
        // the next arm is a "catch-all" pattern)
        Some(n @ 42) => println!("The Answer: {}!", n),
        // Match any other number.
        Some(n)      => println!("Not interesting... {}", n),
        // Match anything else (`None` variant).
        _            => (),
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Useful when you need the whole value and a sub-pattern.

_RBE source: `flow_control/match/binding.md`_

---

### 10.1.6 if let

**What it is**

For some use cases, when matching enums, `match` is awkward. For example:
`if let` is cleaner for this use case and in addition allows various
failure options to be specified:
In the same way, `if let` can be used to match any enum value:
Another benefit is that `if let` allows us to match non-parameterized enum variants. This is true even in cases where the enum doesn't implement or derive `PartialEq`. In such cases `if Foo::Bar == a` would fail to compile, because instances of the enum cannot be equated, however `if let` will continue to work.

**JS mental model**

Sugar for matching one pattern: `if let Some(x) = opt { }` ≈ `if (opt != null)`.

**Rust example**

```rust
fn main() {
    // All have type `Option<i32>`
    let number = Some(7);
    let letter: Option<i32> = None;
    let emoticon: Option<i32> = None;

    // The `if let` construct reads: "if `let` destructures `number` into
    // `Some(i)`, evaluate the block (`{}`).
    if let Some(i) = number {
        println!("Matched {:?}!", i);
    }

    // If you need to specify a failure, use an else:
    if let Some(i) = letter {
        println!("Matched {:?}!", i);
    } else {
        // Destructure failed. Change to the failure case.
        println!("Didn't match a number. Let's go with a letter!");
    }

    // Provide an altered failing condition.
    let i_like_letters = false;

    if let Some(i) = emoticon {
        println!("Matched {:?}!", i);
    // Destructure failed. Evaluate an `else if` condition to see if the
    // alternate failure branch should be taken:
    } else if i_like_letters {
        println!("Didn't match a number. Let's go with a letter!");
    } else {
        // The condition evaluated false. This branch is the default:
        println!("I don't like letters. Let's go with an emoticon :)!");
    }
}
```

```rust
// Our example enum
enum Foo {
    Bar,
    Baz,
    Qux(u32)
}

fn main() {
    // Create example variables
    let a = Foo::Bar;
    let b = Foo::Baz;
    let c = Foo::Qux(100);

    // Variable a matches Foo::Bar
    if let Foo::Bar = a {
        println!("a is foobar");
    }

    // Variable b does not match Foo::Bar
    // So this will print nothing
    if let Foo::Bar = b {
        println!("b is foobar");
    }

    // Variable c matches Foo::Qux which has a value
    // Similar to Some() in the previous example
    if let Foo::Qux(value) = c {
        println!("c is {}", value);
    }

    // Binding also works with `if let`
    if let Foo::Qux(value @ 100) = c {
        println!("c is one hundred");
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Use when `match` would have one useful arm + `_ => {}`.

_RBE source: `flow_control/if_let.md`_

---

### 10.1.7 let-else

**What it is**

> 🛈 stable since: rust 1.65
>
> 🛈 you can target specific edition by compiling like this
> `rustc --edition=2021 main.rs`
With `let`-`else`, a refutable pattern can match and bind variables
in the surrounding scope like a normal `let`, or else diverge (e.g. `break`,
`return`, `panic!`) when the pattern doesn't match. The scope of name bindings is the main thing that makes this different from
`match` or `if let`-`else` expressions. You could previously approximate these
patterns with an unfortunate bit of repetition and an outer `let`:
See also:
[option][option], [match][match], [if let][if_let] and the [let-else RFC][let_else_rfc].

**JS mental model**

`let Some(x) = opt else { return };` — early-exit sugar like a guard clause.

**Rust example**

```rust
use std::str::FromStr;
#
fn get_count_item(s: &str) -> (u64, &str) {
    let mut it = s.split(' ');
    let (count_str, item) = match (it.next(), it.next()) {
        (Some(count_str), Some(item)) => (count_str, item),
        _ => panic!("Can't segment count item pair: '{s}'"),
    };
    let count = if let Ok(count) = u64::from_str(count_str) {
        count
    } else {
        panic!("Can't parse integer: '{count_str}'");
    };
    (count, item)
}
#
assert_eq!(get_count_item("3 chairs"), (3, "chairs"));
```

```rust
use std::str::FromStr;

fn get_count_item(s: &str) -> (u64, &str) {
    let mut it = s.split(' ');
    let (Some(count_str), Some(item)) = (it.next(), it.next()) else {
        panic!("Can't segment count item pair: '{s}'");
    };
    let Ok(count) = u64::from_str(count_str) else {
        panic!("Can't parse integer: '{count_str}'");
    };
    (count, item)
}

fn main() {
    assert_eq!(get_count_item("3 chairs"), (3, "chairs"));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- `else` branch must diverge (`return`/`break`/`panic`).

_RBE source: `flow_control/let_else.md`_

---

### 10.1.8 while let

**What it is**

Similar to `if let`, `while let` can make awkward `match` sequences
more tolerable. Consider the following sequence that increments `i`:
Using `while let` makes this sequence much nicer:
See also:
[`enum`][enum], [`Option`][option], and the [RFC][while_let_rfc]
[enum]: ../custom_types/enum.md
[option]: ../std/option.md
[while_let_rfc]: https://github.com/rust-lang/rfcs/pull/214.

**JS mental model**

Loop while a pattern matches — great for popping stacks/iterators.

**Rust example**

```rust
fn main() {
    // Make `optional` of type `Option<i32>`
    let mut optional = Some(0);

    // This reads: "while `let` destructures `optional` into
    // `Some(i)`, evaluate the block (`{}`). Else `break`.
    while let Some(i) = optional {
        if i > 9 {
            println!("Greater than 9, quit!");
            optional = None;
        } else {
            println!("`i` is `{:?}`. Try again.", i);
            optional = Some(i + 1);
        }
        // ^ Less rightward drift and doesn't require
        // explicitly handling the failing case.
    }
    // ^ `if let` had additional optional `else`/`else if`
    // clauses. `while let` does not have these.
}
```

```rust
// Make `optional` of type `Option<i32>`
let mut optional = Some(0);

// Repeatedly try this test.
loop {
    match optional {
        // If `optional` destructures, evaluate the block.
        Some(i) => {
            if i > 9 {
                println!("Greater than 9, quit!");
                optional = None;
            } else {
                println!("`i` is `{:?}`. Try again.", i);
                optional = Some(i + 1);
            }
            // ^ Requires 3 indentations!
        },
        // Quit the loop when the destructure fails:
        _ => { break; }
        // ^ Why should this be required? There must be a better way!
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Common with `iter.next()` / channels.

_RBE source: `flow_control/while_let.md`_

---
