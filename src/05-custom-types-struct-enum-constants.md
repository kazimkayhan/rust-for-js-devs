# Custom types: struct, enum, constants

_RBE chapter root maps here. Every nested leaf below has the full 5-part teaching block._

### 5.1 Custom Types

**What it is**

Rust custom data types are formed mainly through the two keywords:
* `struct`: define a structure
* `enum`: define an enumeration
Constants can also be created via the `const` and `static` keywords.

**JS mental model**

JS objects/classes vs Rust `struct` + `enum`. Enums are tagged unions (like TS discriminated unions).

**Rust example**

```rust
struct Point { x: i32, y: i32 }
enum Msg { Quit, Move { x: i32, y: i32 } }

fn main() {
    let p = Point { x: 1, y: 2 };
    let _m = Msg::Quit;
    println!("{}, {}", p.x, p.y);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Fields are private by default outside the module.
- Prefer enums over magic strings/numbers.

_RBE source: `custom_types.md`_

---

### 5.1.1 Structures

**What it is**

There are three types of structures ("structs") that can be created using the
`struct` keyword:
* Tuple structs, which are, basically, named tuples. * The classic [C structs][c_struct]
* Unit structs, which are field-less, are useful for generics. Add a function `rect_area` which calculates the area of a `Rectangle` (try
   using nested destructuring). Add a function `square` which takes a `Point` and a `f32` as arguments, and
   returns a `Rectangle` with its top left corner on the point, and a width and
   height corresponding to the `f32`.

**JS mental model**

Like a class/object shape without inheritance. Methods live in `impl` blocks.

**Rust example**

```rust
// An attribute to hide warnings for unused code.
#![allow(dead_code)]

#[derive(Debug)]
struct Person {
    name: String,
    age: u8,
}

// A unit struct
struct Unit;

// A tuple struct
struct Pair(i32, f32);

// A struct with two fields
struct Point {
    x: f32,
    y: f32,
}

// Structs can be reused as fields of another struct
struct Rectangle {
    // A rectangle can be specified by where the top left and bottom right
    // corners are in space.
    top_left: Point,
    bottom_right: Point,
}

fn main() {
    // Create struct with field init shorthand
    let name = String::from("Peter");
    let age = 27;
    let peter = Person { name, age };

    // Print debug struct
    println!("{:?}", peter);

    // Instantiate a `Point`
    let point: Point = Point { x: 5.2, y: 0.4 };
    let another_point: Point = Point { x: 10.3, y: 0.2 };

    // Access the fields of the point
    println!("point coordinates: ({}, {})", point.x, point.y);

    // Make a new point by using struct update syntax to use the fields of our
    // other one
    let bottom_right = Point { x: 10.3, ..another_point };

    // `bottom_right.y` will be the same as `another_point.y` because we used that field
    // from `another_point`
    println!("second point: ({}, {})", bottom_right.x, bottom_right.y);

    // Destructure the point using a `let` binding
    let Point { x: left_edge, y: top_edge } = point;

    let _rectangle = Rectangle {
        // struct instantiation is an expression too
        top_left: Point { x: left_edge, y: top_edge },
        bottom_right: bottom_right,
    };

    // Instantiate a unit struct
    let _unit = Unit;

    // Instantiate a tuple struct
    let pair = Pair(1, 0.1);

    // Access the fields of a tuple struct
    println!("pair contains {:?} and {:?}", pair.0, pair.1);

    // Destructure a tuple struct
    let Pair(integer, decimal) = pair;

    println!("pair contains {:?} and {:?}", integer, decimal);
}
```

**JS equivalent**

```javascript
const p = { x: 1, y: 2 }; // no class required
```

**Watch out**

- Update syntax: `Point { x: 1, ..old }` like object spread but move/copy aware.
- Tuple structs and unit structs exist too.

_RBE source: `custom_types/structs.md`_

---

### 5.1.2 Enums

**What it is**

The `enum` keyword allows the creation of a type which may be one of a few
different variants. Any variant which is valid as a `struct` is also valid in
an `enum`. Type aliases
If you use a type alias, you can refer to each enum variant via its alias. This might be useful if the enum's name is too long or too generic, and you
want to rename it.

**JS mental model**

TS discriminated unions / algebraic data types. Each variant can carry different data.

**Rust example**

```rust
// Create an `enum` to classify a web event. Note how both
// names and type information together specify the variant:
// `PageLoad != PageUnload` and `KeyPress(char) != Paste(String)`.
// Each is different and independent.
enum WebEvent {
    // An `enum` variant may either be `unit-like`,
    PageLoad,
    PageUnload,
    // like tuple structs,
    KeyPress(char),
    Paste(String),
    // or c-like structures.
    Click { x: i64, y: i64 },
}

// A function which takes a `WebEvent` enum as an argument and
// returns nothing.
fn inspect(event: WebEvent) {
    match event {
        WebEvent::PageLoad => println!("page loaded"),
        WebEvent::PageUnload => println!("page unloaded"),
        // Destructure `c` from inside the `enum` variant.
        WebEvent::KeyPress(c) => println!("pressed '{}'.", c),
        WebEvent::Paste(s) => println!("pasted \"{}\".", s),
        // Destructure `Click` into `x` and `y`.
        WebEvent::Click { x, y } => {
            println!("clicked at x={}, y={}.", x, y);
        },
    }
}

fn main() {
    let pressed = WebEvent::KeyPress('x');
    // `to_owned()` creates an owned `String` from a string slice.
    let pasted  = WebEvent::Paste("my text".to_owned());
    let click   = WebEvent::Click { x: 20, y: 80 };
    let load    = WebEvent::PageLoad;
    let unload  = WebEvent::PageUnload;

    inspect(pressed);
    inspect(pasted);
    inspect(click);
    inspect(load);
    inspect(unload);
}
```

```rust
enum VeryVerboseEnumOfThingsToDoWithNumbers {
    Add,
    Subtract,
}

// Creates a type alias
type Operations = VeryVerboseEnumOfThingsToDoWithNumbers;

fn main() {
    // We can refer to each variant via its alias, not its long and inconvenient
    // name.
    let x = Operations::Add;
}
```

**JS equivalent**

```javascript
/** @typedef {{type:'Quit'}|{type:'Move',x:number,y:number}} Msg */
```

**Watch out**

- Must handle all variants in `match` (or use `_`).
- `Option` and `Result` are enums.

_RBE source: `custom_types/enum.md`_

---

#### 5.1.2.1 use

**What it is**

The `use` declaration can be used to avoid typing the full module path to access a name:
See also:
[`match`][match] and [`use`][use]
[use]: ../../mod/use.md
[match]: ../../flow_control/match.md.

**JS mental model**

`use Enum::Variant` is like importing an enum member so you can write `Variant` instead of `Enum::Variant`.

**Rust example**

```rust
// An attribute to hide warnings for unused code.
#![allow(dead_code)]

enum Stage {
    Beginner,
    Advanced,
}

enum Role {
    Student,
    Teacher,
}

fn main() {
    // Explicitly `use` each name so they are available without
    // manual scoping.
    use Stage::{Beginner, Advanced};
    // Automatically `use` each name inside `Role`.
    use Role::*;

    // Equivalent to `Stage::Beginner`.
    let stage = Beginner;
    // Equivalent to `Role::Student`.
    let role = Student;

    match stage {
        // Note the lack of scoping because of the explicit `use` above.
        Beginner => println!("Beginners are starting their learning journey!"),
        Advanced => println!("Advanced learners are mastering their subjects..."),
    }

    match role {
        // Note again the lack of scoping.
        Student => println!("Students are acquiring knowledge!"),
        Teacher => println!("Teachers are spreading knowledge!"),
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- `use` is compile-time name import, not runtime require.

_RBE source: `custom_types/enum/enum_use.md`_

---

#### 5.1.2.2 C-like

**What it is**

`enum` can also be used as C-like enums. See also:
[casting][cast]
[cast]: ../../types/cast.md.

**JS mental model**

Like a numeric enum in TS (`enum Color { Red = 0 }`).

**Rust example**

```rust
// An attribute to hide warnings for unused code.
#![allow(dead_code)]

// enum with implicit discriminator (starts at 0)
enum Number {
    Zero,
    One,
    Two,
}

// enum with explicit discriminator
enum Color {
    Red = 0xff0000,
    Green = 0x00ff00,
    Blue = 0x0000ff,
}

fn main() {
    // `enums` can be cast as integers.
    println!("zero is {}", Number::Zero as i32);
    println!("one is {}", Number::One as i32);

    println!("roses are #{:06x}", Color::Red as u32);
    println!("violets are #{:06x}", Color::Blue as u32);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Cast with `as` carefully.
- Prefer richer enums with data when possible.

_RBE source: `custom_types/enum/c_like.md`_

---

#### 5.1.2.3 Testcase: linked-list

**What it is**

A common way to implement a linked-list is via `enums`:
See also:
[`Box`][box] and [methods][methods]
[box]: ../../std/box.md
[methods]: ../../fn/methods.md.

**JS mental model**

Building a linked list with `enum List { Cons(i32, Box<List>), Nil }` — recursion needs `Box` because size must be known.

**Rust example**

```rust
use crate::List::*;

enum List {
    // Cons: Tuple struct that wraps an element and a pointer to the next node
    Cons(u32, Box<List>),
    // Nil: A node that signifies the end of the linked list
    Nil,
}

// Methods can be attached to an enum
impl List {
    // Create an empty list
    fn new() -> List {
        // `Nil` has type `List`
        Nil
    }

    // Consume a list, and return the same list with a new element at its front
    fn prepend(self, elem: u32) -> List {
        // `Cons` also has type List
        Cons(elem, Box::new(self))
    }

    // Return the length of the list
    fn len(&self) -> u32 {
        // `self` has to be matched, because the behavior of this method
        // depends on the variant of `self`
        // `self` has type `&List`, and `*self` has type `List`, matching on a
        // concrete type `T` is preferred over a match on a reference `&T`
        // after Rust 2018 you can use self here and tail (with no ref) below as well,
        // rust will infer &s and ref tail.
        // See https://doc.rust-lang.org/edition-guide/rust-2018/ownership-and-lifetimes/default-match-bindings.html
        match *self {
            // Can't take ownership of the tail, because `self` is borrowed;
            // instead take a reference to the tail
            // And it's a non-tail recursive call which may cause stack overflow for long lists.
            Cons(_, ref tail) => 1 + tail.len(),
            // Base Case: An empty list has zero length
            Nil => 0
        }
    }

    // Return representation of the list as a (heap allocated) string
    fn stringify(&self) -> String {
        match *self {
            Cons(head, ref tail) => {
                // `format!` is similar to `print!`, but returns a heap
                // allocated string instead of printing to the console
                format!("{}, {}", head, tail.stringify())
            },
            Nil => {
                format!("Nil")
            },
        }
    }
}

fn main() {
    // Create an empty linked list
    let mut list = List::new();

    // Prepend some elements
    list = list.prepend(1);
    list = list.prepend(2);
    list = list.prepend(3);

    // Show the final state of the list
    println!("linked list has length: {}", list.len());
    println!("{}", list.stringify());
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Recursive types need indirection (`Box`).
- In real code prefer `Vec` over hand-rolled lists.

_RBE source: `custom_types/enum/testcase_linked_list.md`_

---

### 5.1.3 constants

**What it is**

Rust has two different types of constants which can be declared in any scope
including global. Both require explicit type annotation:
* `const`: An unchangeable value (the common case). * `static`: A possibly mutable variable with [`'static`][static] lifetime. The static lifetime is inferred and does not have to be specified.

**JS mental model**

`const` is like a frozen compile-time constant. `static` is a long-lived global (careful with mut).

**Rust example**

```rust
// Globals are declared outside all other scopes.
static LANGUAGE: &str = "Rust";
const THRESHOLD: i32 = 10;

fn is_big(n: i32) -> bool {
    // Access constant in some function
    n > THRESHOLD
}

fn main() {
    let n = 16;

    // Access constant in the main thread
    println!("This is {}", LANGUAGE);
    println!("The threshold is {}", THRESHOLD);
    println!("{} is {}", n, if is_big(n) { "big" } else { "small" });

    // Error! Cannot modify a `const`.
    THRESHOLD = 5;
    // FIXME ^ Comment out this line
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- `const` can be inlined; `static` has a fixed address.
- `static mut` is unsafe — avoid.

_RBE source: `custom_types/constants.md`_

---
