# Std library types

_RBE chapter root maps here. Every nested leaf below has the full 5-part teaching block._

### 20.1 Std library types

**What it is**

The `std` library provides many custom types which expands drastically on
the `primitives`. Some of these include:
* growable `String`s like: `"hello world"`
* growable vectors: `[1, 2, 3]`
* optional types: `Option<i32>`
* error handling types: `Result<i32, i32>`
* heap allocated pointers: `Box<i32>`
See also:
[primitives] and [the std library][std]
[primitives]: primitives.md
[std]: https://doc.rust-lang.org/std/.

**JS mental model**

The standard library gives you Vec, String, HashMap, Option, Result, smart pointers — like JS built-ins but explicit about allocation and ownership.

**Rust example**

```rust
fn main() {
    let v = vec![1, 2, 3];
    let s = String::from("hi");
    println!("{v:?} {s}");
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- prelude imports common items automatically.
- std assumes an OS; core is freestanding.

_RBE source: `std.md`_

---

### 20.1.1 Box, stack and heap

**What it is**

The stack holds fixed-size local data. The heap holds dynamically sized or long-lived data. Box<T> owns a heap value of type T. When the Box drops, the heap memory is freed.

**JS mental model**

Box<T> is a heap allocation — like a single-object malloc with ownership. JS objects are heap by default; Rust puts many things on the stack unless boxed.

**Rust example**

```rust
use std::mem;

#[allow(dead_code)]
#[derive(Debug, Clone, Copy)]
struct Point {
    x: f64,
    y: f64,
}

// A Rectangle can be specified by where its top left and bottom right
// corners are in space
#[allow(dead_code)]
struct Rectangle {
    top_left: Point,
    bottom_right: Point,
}

fn origin() -> Point {
    Point { x: 0.0, y: 0.0 }
}

fn boxed_origin() -> Box<Point> {
    // Allocate this point on the heap, and return a pointer to it
    Box::new(Point { x: 0.0, y: 0.0 })
}

fn main() {
    // (all the type annotations are superfluous)
    // Stack allocated variables
    let point: Point = origin();
    let rectangle: Rectangle = Rectangle {
        top_left: origin(),
        bottom_right: Point { x: 3.0, y: -4.0 }
    };

    // Heap allocated rectangle
    let boxed_rectangle: Box<Rectangle> = Box::new(Rectangle {
        top_left: origin(),
        bottom_right: Point { x: 3.0, y: -4.0 },
    });

    // The output of functions can be boxed
    let boxed_point: Box<Point> = Box::new(origin());

    // Double indirection
    let box_in_a_box: Box<Box<Point>> = Box::new(boxed_origin());

    println!("Point occupies {} bytes on the stack",
             mem::size_of_val(&point));
    println!("Rectangle occupies {} bytes on the stack",
             mem::size_of_val(&rectangle));

    // box size == pointer size
    println!("Boxed point occupies {} bytes on the stack",
             mem::size_of_val(&boxed_point));
    println!("Boxed rectangle occupies {} bytes on the stack",
             mem::size_of_val(&boxed_rectangle));
    println!("Boxed box occupies {} bytes on the stack",
             mem::size_of_val(&box_in_a_box));

    // Copy the data contained in `boxed_point` into `unboxed_point`
    let unboxed_point: Point = *boxed_point;
    println!("Unboxed point occupies {} bytes on the stack",
             mem::size_of_val(&unboxed_point));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Use Box for recursive types or large data you want on the heap.
- Deref lets you use Box<T> like &T.

_RBE source: `std/box.md`_

---

### 20.1.2 Vectors

**What it is**

Vectors are re-sizable arrays. Like slices, their size is not known at compile
time, but they can grow or shrink at any time. A vector is represented using
3 parameters:
- pointer to the data
- length
- capacity
The capacity indicates how much memory is reserved for the vector. The vector
can grow as long as the length is smaller than the capacity.

**JS mental model**

Vec<T> is a growable array — the real JS Array analogue.

**Rust example**

```rust
fn main() {
    // Iterators can be collected into vectors
    let collected_iterator: Vec<i32> = (0..10).collect();
    println!("Collected (0..10) into: {:?}", collected_iterator);

    // The `vec!` macro can be used to initialize a vector
    let mut xs = vec![1i32, 2, 3];
    println!("Initial vector: {:?}", xs);

    // Insert new element at the end of the vector
    println!("Push 4 into the vector");
    xs.push(4);
    println!("Vector: {:?}", xs);

    // Error! Immutable vectors can't grow
    collected_iterator.push(0);
    // FIXME ^ Comment out this line

    // The `len` method yields the number of elements currently stored in a vector
    println!("Vector length: {}", xs.len());

    // Indexing is done using the square brackets (indexing starts at 0)
    println!("Second element: {}", xs[1]);

    // `pop` removes the last element from the vector and returns it
    println!("Pop last element: {:?}", xs.pop());

    // Out of bounds indexing yields a panic
    println!("Fourth element: {}", xs[3]);
    // FIXME ^ Comment out this line

    // `Vector`s can be easily iterated over
    println!("Contents of xs:");
    for x in xs.iter() {
        println!("> {}", x);
    }

    // A `Vector` can also be iterated over while the iteration
    // count is enumerated in a separate variable (`i`)
    for (i, x) in xs.iter().enumerate() {
        println!("In position {} we have value {}", i, x);
    }

    // Thanks to `iter_mut`, mutable `Vector`s can also be iterated
    // over in a way that allows modifying each value
    for x in xs.iter_mut() {
        *x *= 3;
    }
    println!("Updated vector: {:?}", xs);
}
```

**JS equivalent**

```javascript
const v = []; v.push(1); v.push(2);
```

**Watch out**

- Indexing out of bounds panics.
- Prefer iter() over indexes when possible.
- vec! macro constructs quickly.

_RBE source: `std/vec.md`_

---

### 20.1.3 Strings

**What it is**

The two most used string types in Rust are `String` and `&str`. A `String` is stored as a vector of bytes (`Vec<u8>`), but guaranteed to
always be a valid UTF-8 sequence. `String` is heap allocated, growable and not
null terminated. `&str` is a slice (`&[u8]`) that always points to a valid UTF-8 sequence, and
can be used to view into a `String`, just like `&[T]` is a view into `Vec<T>`.

**JS mental model**

&str is a view into UTF-8 (like a string slice). String is owned growable UTF-8 (like a mutable string buffer). JS strings are closer to immutable UTF-16.

**Rust example**

```rust
fn main() {
    // (all the type annotations are superfluous)
    // A reference to a string allocated in read only memory
    let pangram: &'static str = "the quick brown fox jumps over the lazy dog";
    println!("Pangram: {}", pangram);

    // Iterate over words in reverse, no new string is allocated
    println!("Words in reverse");
    for word in pangram.split_whitespace().rev() {
        println!("> {}", word);
    }

    // Copy chars into a vector, sort and remove duplicates
    let mut chars: Vec<char> = pangram.chars().collect();
    chars.sort();
    chars.dedup();

    // Create an empty and growable `String`
    let mut string = String::new();
    for c in chars {
        // Insert a char at the end of string
        string.push(c);
        // Insert a string at the end of string
        string.push_str(", ");
    }

    // The trimmed string is a slice to the original string, hence no new
    // allocation is performed
    let chars_to_trim: &[char] = &[' ', ','];
    let trimmed_str: &str = string.trim_matches(chars_to_trim);
    println!("Used characters: {}", trimmed_str);

    // Heap allocate a string
    let alice = String::from("I like dogs");
    // Allocate new memory and store the modified string there
    let bob: String = alice.replace("dog", "cat");

    println!("Alice says: {}", alice);
    println!("Bob says: {}", bob);
}
```

```rust
use std::str;

fn main() {
    // Note that this is not actually a `&str`
    let bytestring: &[u8; 21] = b"this is a byte string";

    // Byte arrays don't have the `Display` trait, so printing them is a bit limited
    println!("A byte string: {:?}", bytestring);

    // Byte strings can have byte escapes...
    let escaped = b"\x52\x75\x73\x74 as bytes";
    // ...but no unicode escapes
    // let escaped = b"\u{211D} is not allowed";
    println!("Some escaped bytes: {:?}", escaped);


    // Raw byte strings work just like raw strings
    let raw_bytestring = br"\u{211D} is not escaped here";
    println!("{:?}", raw_bytestring);

    // Converting a byte array to `str` can fail
    if let Ok(my_str) = str::from_utf8(raw_bytestring) {
        println!("And the same as text: '{}'", my_str);
    }

    let _quotes = br#"You can also use "fancier" formatting, \
                    like with normal raw strings"#;

    // Byte strings don't have to be UTF-8
    let shift_jis = b"\x82\xe6\x82\xa8\x82\xb1\x82\xbb"; // "ようこそ" in SHIFT-JIS

    // But then they can't always be converted to `str`
    match str::from_utf8(shift_jis) {
        Ok(my_str) => println!("Conversion successful: '{}'", my_str),
        Err(e) => println!("Conversion failed: {:?}", e),
    };
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Indexing by byte can panic mid-char — use chars()/get.
- String + &str works; &str + &str needs to_owned/format!.

_RBE source: `std/str.md`_

---

### 20.1.4 Option

**What it is**

Sometimes it's desirable to catch the failure of some parts of a program
instead of calling `panic!`; this can be accomplished using the `Option` enum. The `Option<T>` enum has two variants:
* `None`, to indicate failure or lack of value, and
* `Some(value)`, a tuple struct that wraps a `value` with type `T`.

**JS mental model**

Option in std is the same enum you use everywhere — first-class nullable.

**Rust example**

```rust
// An integer division that doesn't `panic!`
fn checked_division(dividend: i32, divisor: i32) -> Option<i32> {
    if divisor == 0 {
        // Failure is represented as the `None` variant
        None
    } else {
        // Result is wrapped in a `Some` variant
        Some(dividend / divisor)
    }
}

// This function handles a division that may not succeed
fn try_division(dividend: i32, divisor: i32) {
    // `Option` values can be pattern matched, just like other enums
    match checked_division(dividend, divisor) {
        None => println!("{} / {} failed!", dividend, divisor),
        Some(quotient) => {
            println!("{} / {} = {}", dividend, divisor, quotient)
        },
    }
}

fn main() {
    try_division(4, 2);
    try_division(1, 0);

    // Binding `None` to a variable needs to be type annotated
    let none: Option<i32> = None;
    let _equivalent_none = None::<i32>;

    let optional_float = Some(0f32);

    // Unwrapping a `Some` variant will extract the value wrapped.
    println!("{:?} unwraps to {:?}", optional_float, optional_float.unwrap());

    // Unwrapping a `None` variant will `panic!`
    println!("{:?} unwraps to {:?}", none, none.unwrap());
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Methods: map, and_then, unwrap_or, ok_or, ...

_RBE source: `std/option.md`_

---

### 20.1.5 Result

**What it is**

We've seen that the `Option` enum can be used as a return value from functions
that may fail, where `None` can be returned to indicate failure. However,
sometimes it is important to express *why* an operation failed. To do this we
have the `Result` enum. The `Result<T, E>` enum has two variants:
* `Ok(value)` which indicates that the operation succeeded, and wraps the
  `value` returned by the operation.

**JS mental model**

Result in std is the standard success/error sum type.

**Rust example**

```rust
mod checked {
    // Mathematical "errors" we want to catch
    #[derive(Debug)]
    pub enum MathError {
        DivisionByZero,
        NonPositiveLogarithm,
        NegativeSquareRoot,
    }

    pub type MathResult = Result<f64, MathError>;

    pub fn div(x: f64, y: f64) -> MathResult {
        if y == 0.0 {
            // This operation would `fail`, instead let's return the reason of
            // the failure wrapped in `Err`
            Err(MathError::DivisionByZero)
        } else {
            // This operation is valid, return the result wrapped in `Ok`
            Ok(x / y)
        }
    }

    pub fn sqrt(x: f64) -> MathResult {
        if x < 0.0 {
            Err(MathError::NegativeSquareRoot)
        } else {
            Ok(x.sqrt())
        }
    }

    pub fn ln(x: f64) -> MathResult {
        if x <= 0.0 {
            Err(MathError::NonPositiveLogarithm)
        } else {
            Ok(x.ln())
        }
    }
}

// `op(x, y)` === `sqrt(ln(x / y))`
fn op(x: f64, y: f64) -> f64 {
    // This is a three level match pyramid!
    match checked::div(x, y) {
        Err(why) => panic!("{:?}", why),
        Ok(ratio) => match checked::ln(ratio) {
            Err(why) => panic!("{:?}", why),
            Ok(ln) => match checked::sqrt(ln) {
                Err(why) => panic!("{:?}", why),
                Ok(sqrt) => sqrt,
            },
        },
    }
}

fn main() {
    // Will this fail?
    println!("{}", op(1.0, 10.0));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Combine with From for ? conversions.

_RBE source: `std/result.md`_

---

#### 20.1.5.1 ?

**What it is**

Chaining results using match can get pretty untidy; luckily, the `?` operator
can be used to make things pretty again. `?` is used at the end of an expression
returning a `Result`, and is equivalent to a match expression, where the
`Err(err)` branch expands to an early `return Err(From::from(err))`, and the `Ok(ok)`
branch expands to an `ok` expression. Be sure to check the [documentation][docs],
as there are many methods to map/compose `Result`. [docs]: https://doc.rust-lang.org/std/result/index.html.

**JS mental model**

? works on Result in functions that return Result — main can return Result too in modern Rust.

**Rust example**

```rust
mod checked {
    #[derive(Debug)]
    enum MathError {
        DivisionByZero,
        NonPositiveLogarithm,
        NegativeSquareRoot,
    }

    type MathResult = Result<f64, MathError>;

    fn div(x: f64, y: f64) -> MathResult {
        if y == 0.0 {
            Err(MathError::DivisionByZero)
        } else {
            Ok(x / y)
        }
    }

    fn sqrt(x: f64) -> MathResult {
        if x < 0.0 {
            Err(MathError::NegativeSquareRoot)
        } else {
            Ok(x.sqrt())
        }
    }

    fn ln(x: f64) -> MathResult {
        if x <= 0.0 {
            Err(MathError::NonPositiveLogarithm)
        } else {
            Ok(x.ln())
        }
    }

    // Intermediate function
    fn op_(x: f64, y: f64) -> MathResult {
        // if `div` "fails", then `DivisionByZero` will be `return`ed
        let ratio = div(x, y)?;

        // if `ln` "fails", then `NonPositiveLogarithm` will be `return`ed
        let ln = ln(ratio)?;

        sqrt(ln)
    }

    pub fn op(x: f64, y: f64) {
        match op_(x, y) {
            Err(why) => panic!("{}", match why {
                MathError::NonPositiveLogarithm
                    => "logarithm of non-positive number",
                MathError::DivisionByZero
                    => "division by zero",
                MathError::NegativeSquareRoot
                    => "square root of negative number",
            }),
            Ok(value) => println!("{}", value),
        }
    }
}

fn main() {
    checked::op(1.0, 10.0);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- main -> Result<(), Box<dyn Error>> is a common pattern.

_RBE source: `std/result/question_mark.md`_

---

### 20.1.6 panic!

**What it is**

The `panic!` macro can be used to generate a panic and start unwinding
its stack. While unwinding, the runtime will take care of freeing all the
resources *owned* by the thread by calling the destructor of all its objects. Since we are dealing with programs with only one thread, `panic!` will cause the
program to report the panic message and exit. Let's check that `panic!` doesn't leak memory.

**JS mental model**

panic! as a library tool for unrecoverable situations; catch_unwind exists but is not like try/catch for normal flow.

**Rust example**

```rust
// Re-implementation of integer division (/)
fn division(dividend: i32, divisor: i32) -> i32 {
    if divisor == 0 {
        // Division by zero triggers a panic
        panic!("division by zero");
    } else {
        dividend / divisor
    }
}

// The `main` task
fn main() {
    // Heap allocated integer
    let _x = Box::new(0i32);

    // This operation will trigger a task failure
    division(3, 0);

    println!("This point won't be reached!");

    // `_x` should get destroyed at this point
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Do not use panic for expected I/O errors.

_RBE source: `std/panic.md`_

---

### 20.1.7 HashMap

**What it is**

Where vectors store values by an integer index, `HashMap`s store values by key. `HashMap` keys can be booleans, integers, strings,
or any other type that implements the `Eq` and `Hash` traits. More on this in the next section. Like vectors, `HashMap`s are growable, but HashMaps can also shrink themselves
when they have excess space.

**JS mental model**

HashMap<K,V> is like a Map or object-as-dict, but keys must be hashable+eq and ownership matters.

**Rust example**

```rust
use std::collections::HashMap;

fn call(number: &str) -> &str {
    match number {
        "798-1364" => "We're sorry, the call cannot be completed as dialed.
            Please hang up and try again.",
        "645-7689" => "Hello, this is Mr. Awesome's Pizza. My name is Fred.
            What can I get for you today?",
        _ => "Hi! Who is this again?"
    }
}

fn main() {
    let mut contacts = HashMap::new();

    contacts.insert("Daniel", "798-1364");
    contacts.insert("Ashley", "645-7689");
    contacts.insert("Katie", "435-8291");
    contacts.insert("Robert", "956-1745");

    // Takes a reference and returns Option<&V>
    match contacts.get(&"Daniel") {
        Some(&number) => println!("Calling Daniel: {}", call(number)),
        _ => println!("Don't have Daniel's number."),
    }

    // `HashMap::insert()` returns `None`
    // if the inserted value is new, `Some(value)` otherwise
    contacts.insert("Daniel", "164-6743");

    match contacts.get(&"Ashley") {
        Some(&number) => println!("Calling Ashley: {}", call(number)),
        _ => println!("Don't have Ashley's number."),
    }

    contacts.remove(&"Ashley");

    // `HashMap::iter()` returns an iterator that yields
    // (&'a key, &'a value) pairs in arbitrary order.
    for (contact, &number) in contacts.iter() {
        println!("Calling {}: {}", contact, call(number));
    }
}
```

**JS equivalent**

```javascript
const m = new Map(); m.set("a", 1);
```

**Watch out**

- entry API is excellent for insert-or-update.
- Default hasher is not cryptographic.

_RBE source: `std/hash.md`_

---

#### 20.1.7.1 Alternate/custom key types

**What it is**

You can use your own types as HashMap keys if they implement Eq and Hash. Unlike JS object keys (strings) or Map (reference equality for objects), Rust keys compare by value equality you define.

**JS mental model**

Custom keys need Eq + Hash — like defining value equality for Map keys in JS (which uses reference equality for objects).

**Rust example**

```rust
use std::collections::HashMap;

// Eq requires that you derive PartialEq on the type.
#[derive(PartialEq, Eq, Hash)]
struct Account<'a>{
    username: &'a str,
    password: &'a str,
}

struct AccountInfo<'a>{
    name: &'a str,
    email: &'a str,
}

type Accounts<'a> = HashMap<Account<'a>, AccountInfo<'a>>;

fn try_logon<'a>(accounts: &Accounts<'a>,
        username: &'a str, password: &'a str){
    println!("Username: {}", username);
    println!("Password: {}", password);
    println!("Attempting logon...");

    let logon = Account {
        username,
        password,
    };

    match accounts.get(&logon) {
        Some(account_info) => {
            println!("Successful logon!");
            println!("Name: {}", account_info.name);
            println!("Email: {}", account_info.email);
        },
        _ => println!("Login failed!"),
    }
}

fn main(){
    let mut accounts: Accounts = HashMap::new();

    let account = Account {
        username: "j.everyman",
        password: "password123",
    };

    let account_info = AccountInfo {
        name: "John Everyman",
        email: "j.everyman@email.com",
    };

    accounts.insert(account, account_info);

    try_logon(&accounts, "j.everyman", "psasword123");

    try_logon(&accounts, "j.everyman", "password123");
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Derive Hash/Eq when possible.
- Be consistent: equal keys must hash equal.

_RBE source: `std/hash/alt_key_types.md`_

---

#### 20.1.7.2 HashSet

**What it is**

HashSet stores unique values. Inserting a duplicate does nothing. Great for membership tests and deduplication.

**JS mental model**

HashSet<T> is a set — like JS Set.

**Rust example**

```rust
use std::collections::HashSet;

fn main() {
    let mut a: HashSet<i32> = vec![1i32, 2, 3].into_iter().collect();
    let mut b: HashSet<i32> = vec![2i32, 3, 4].into_iter().collect();

    assert!(a.insert(4));
    assert!(a.contains(&4));

    // `HashSet::insert()` returns false if
    // there was a value already present.
    assert!(b.insert(4), "Value 4 is already in set B!");
    // FIXME ^ Comment out this line

    b.insert(5);

    // If a collection's element type implements `Debug`,
    // then the collection implements `Debug`.
    // It usually prints its elements in the format `[elem1, elem2, ...]`
    println!("A: {:?}", a);
    println!("B: {:?}", b);

    // Print [1, 2, 3, 4, 5] in arbitrary order
    println!("Union: {:?}", a.union(&b).collect::<Vec<&i32>>());

    // This should print [1]
    println!("Difference: {:?}", a.difference(&b).collect::<Vec<&i32>>());

    // Print [2, 3, 4] in arbitrary order.
    println!("Intersection: {:?}", a.intersection(&b).collect::<Vec<&i32>>());

    // Print [1, 5]
    println!("Symmetric Difference: {:?}",
             a.symmetric_difference(&b).collect::<Vec<&i32>>());
}
```

**JS equivalent**

```javascript
const s = new Set([1,2,2]); // {1,2}
```

**Watch out**

- Built on HashMap.
- Union/intersection methods available.

_RBE source: `std/hash/hashset.md`_

---

### 20.1.8 Rc

**What it is**

When multiple ownership is needed, `Rc`(Reference Counting) can be used. `Rc`
keeps track of the number of the references which means the number of owners of
the value wrapped inside an `Rc`. Reference count of an `Rc` increases by 1 whenever an `Rc` is cloned, and
decreases by 1 whenever one cloned `Rc` is dropped out of the scope. When an
`Rc`'s reference count becomes zero (which means there are no remaining owners),
both the `Rc` and the value are all dropped.

**JS mental model**

Rc<T> is single-threaded reference counting — shared ownership like multiple JS references, but only on one thread.

**Rust example**

```rust
use std::rc::Rc;

fn main() {
    let rc_examples = "Rc examples".to_string();
    {
        println!("--- rc_a is created ---");

        let rc_a: Rc<String> = Rc::new(rc_examples);
        println!("Reference Count of rc_a: {}", Rc::strong_count(&rc_a));

        {
            println!("--- rc_a is cloned to rc_b ---");

            let rc_b: Rc<String> = Rc::clone(&rc_a);
            println!("Reference Count of rc_b: {}", Rc::strong_count(&rc_b));
            println!("Reference Count of rc_a: {}", Rc::strong_count(&rc_a));

            // Two `Rc`s are equal if their inner values are equal
            println!("rc_a and rc_b are equal: {}", rc_a.eq(&rc_b));

            // We can use methods of a value directly
            println!("Length of the value inside rc_a: {}", rc_a.len());
            println!("Value of rc_b: {}", rc_b);

            println!("--- rc_b is dropped out of scope ---");
        }

        println!("Reference Count of rc_a: {}", Rc::strong_count(&rc_a));

        println!("--- rc_a is dropped out of scope ---");
    }

    // Error! `rc_examples` already moved into `rc_a`
    // And when `rc_a` is dropped, `rc_examples` is dropped together
    // println!("rc_examples: {}", rc_examples);
    // TODO ^ Try uncommenting this line
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- clone() bumps the count (cheap pointer copy).
- RefCell often paired for interior mutability.
- Cycles leak without Weak.

_RBE source: `std/rc.md`_

---

### 20.1.9 Arc

**What it is**

When shared ownership between threads is needed, `Arc`(Atomically Reference
Counted) can be used. This struct, via the `Clone` implementation can create
a reference pointer for the location of a value in the memory heap while
increasing the reference counter. As it shares ownership between threads, when
the last reference pointer to a value is out of scope, the variable is dropped.

**JS mental model**

Arc<T> is atomic Rc — shared ownership across threads. Like sharing an object across workers with explicit sync.

**Rust example**

```rust
use std::time::Duration;
use std::sync::Arc;
use std::thread;

fn main() {
    // This variable declaration is where its value is specified.
    let apple = Arc::new("the same apple");

    for _ in 0..10 {
        // Here there is no value specification as it is a pointer to a
        // reference in the memory heap.
        let apple = Arc::clone(&apple);

        thread::spawn(move || {
            // As Arc was used, threads can be spawned using the value allocated
            // in the Arc variable pointer's location.
            println!("{:?}", apple);
        });
    }

    // Make sure all Arc instances are printed from spawned threads.
    thread::sleep(Duration::from_secs(1));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Combine with Mutex/RwLock for mutation.
- clone() is cheap; data is shared.

_RBE source: `std/arc.md`_

---
