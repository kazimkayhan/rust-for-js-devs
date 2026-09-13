# Scoping rules: ownership, borrowing, lifetimes

_RBE chapter root maps here. Every nested leaf below has the full 5-part teaching block._

### 16.1 Scoping rules

**What it is**

Scoping rules decide when values are created and destroyed. Rust uses ownership instead of a garbage collector. When the owner goes out of scope, the value is dropped.

**JS mental model**

This chapter is the big difference from JS: ownership, borrowing, lifetimes. No GC — rules at compile time.

**Rust example**

```rust
fn main() {
    let s = String::from("own");
    let t = s; // move
    // println!("{s}"); // error after move
    println!("{t}");
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Re-read this chapter often.
- Compiler errors here are normal while learning.

_RBE source: `scope.md`_

---

### 16.1.1 RAII

**What it is**

Variables in Rust do more than just hold data in the stack: they also *own*
resources, e.g. `Box<T>` owns memory in the heap. Rust enforces [RAII][raii]
(Resource Acquisition Is Initialization), so whenever an object goes out of
scope, its destructor is called and its owned resources are freed. This behavior shields against *resource leak* bugs, so you'll never have to
manually free memory or worry about memory leaks again!

**JS mental model**

RAII: acquire resource when a value is created, release when it drops — like using/try-finally automatic via scope.

**Rust example**

```rust
// raii.rs
fn create_box() {
    // Allocate an integer on the heap
    let _box1 = Box::new(3i32);

    // `_box1` is destroyed here, and memory gets freed
}

fn main() {
    // Allocate an integer on the heap
    let _box2 = Box::new(5i32);

    // A nested scope:
    {
        // Allocate an integer on the heap
        let _box3 = Box::new(4i32);

        // `_box3` is destroyed here, and memory gets freed
    }

    // Creating lots of boxes just for fun
    // There's no need to manually free memory!
    for _ in 0u32..1_000 {
        create_box();
    }

    // `_box2` is destroyed here, and memory gets freed
}
```

```rust
struct ToDrop;

impl Drop for ToDrop {
    fn drop(&mut self) {
        println!("ToDrop is being dropped");
    }
}

fn main() {
    let x = ToDrop;
    println!("Made a ToDrop!");
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Drop runs deterministically at end of scope.
- Files, locks, memory all use this pattern.

_RBE source: `scope/raii.md`_

---

### 16.1.2 Ownership and moves

**What it is**

Because variables are in charge of freeing their own resources,
**resources can only have one owner**. This prevents resources
from being freed more than once. Note that not all variables own
resources (e.g. When doing assignments (`let x = y`) or passing function arguments by value
(`foo(x)`), the *ownership* of the resources is transferred.

**JS mental model**

Assignment/passing often moves ownership (invalidates the old name) — not a shared reference like JS objects.

**Rust example**

```rust
// This function takes ownership of the heap allocated memory
fn destroy_box(c: Box<i32>) {
    println!("Destroying a box that contains {}", c);

    // `c` is destroyed and the memory freed
}

fn main() {
    // _Stack_ allocated integer
    let x = 5u32;

    // *Copy* `x` into `y` - no resources are moved
    let y = x;

    // Both values can be independently used
    println!("x is {}, and y is {}", x, y);

    // `a` is a pointer to a _heap_ allocated integer
    let a = Box::new(5i32);

    println!("a contains: {}", a);

    // *Move* `a` into `b`
    let b = a;
    // The pointer address of `a` is copied (not the data) into `b`.
    // Both are now pointers to the same heap allocated data, but
    // `b` now owns it.

    // Error! `a` can no longer access the data, because it no longer owns the
    // heap memory
    //println!("a contains: {}", a);
    // TODO ^ Try uncommenting this line

    // This function takes ownership of the heap allocated memory from `b`
    destroy_box(b);

    // Since the heap memory has been freed at this point, this action would
    // result in dereferencing freed memory, but it's forbidden by the compiler
    // Error! Same reason as the previous Error
    //println!("b contains: {}", b);
    // TODO ^ Try uncommenting this line
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- After move, using the old name is a compile error.
- Copy types (numbers) are duplicated instead.

_RBE source: `scope/move.md`_

---

#### 16.1.2.1 Mutability

**What it is**

Mutability of data can be changed when ownership is transferred.

**JS mental model**

You can mutate after taking ownership if the binding is mut.

**Rust example**

```rust
fn main() {
    let immutable_box = Box::new(5u32);

    println!("immutable_box contains {}", immutable_box);

    // Mutability error
    //*immutable_box = 4;

    // *Move* the box, changing the ownership (and mutability)
    let mut mutable_box = immutable_box;

    println!("mutable_box contains {}", mutable_box);

    // Modify the contents of the box
    *mutable_box = 4;

    println!("mutable_box now contains {}", mutable_box);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Ownership transfer can change mutability at the new binding.

_RBE source: `scope/move/mut.md`_

---

#### 16.1.2.2 Partial moves

**What it is**

Within the [destructuring] of a single variable, both `by-move` and
`by-reference` pattern bindings can be used at the same time. Doing
this will result in a _partial move_ of the variable, which means
that parts of the variable will be moved while other parts stay. In
such a case, the parent variable cannot be used afterwards as a
whole, however the parts that are only referenced (and not moved)
can still be used. Note that types that implement the
[`Drop` trait][droptrait] cannot be partially moved from, because
its `drop` method would use it afterwards as a whole.

**JS mental model**

Moving one field out of a struct can partially invalidate the rest — careful.

**Rust example**

```rust
fn main() {
    #[derive(Debug)]
    struct Person {
        name: String,
        age: Box<u8>,
    }

    // Error! cannot move out of a type which implements the `Drop` trait
    //impl Drop for Person {
    //    fn drop(&mut self) {
    //        println!("Dropping the person struct {:?}", self)
    //    }
    //}
    // TODO ^ Try uncommenting these lines

    let person = Person {
        name: String::from("Alice"),
        age: Box::new(20),
    };

    // `name` is moved out of person, but `age` is referenced
    let Person { name, ref age } = person;

    println!("The person's age is {}", age);

    println!("The person's name is {}", name);

    // Error! borrow of partially moved value: `person` partial move occurs
    //println!("The person struct is {:?}", person);

    // `person` cannot be used but `person.age` can be used as it is not moved
    println!("The person's age from person struct is {}", person.age);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Pattern match carefully; use references to avoid partial moves.

_RBE source: `scope/move/partial_move.md`_

---

### 16.1.3 Borrowing

**What it is**

Most of the time, we'd like to access data without taking ownership over
it. To accomplish this, Rust uses a *borrowing* mechanism. Instead of
passing objects by value (`T`), objects can be passed by reference (`&T`). The compiler statically guarantees (via its borrow checker) that references
*always* point to valid objects.

**JS mental model**

Borrowing &T / &mut T is temporary access without taking ownership — like passing a read-only or exclusive view.

**Rust example**

```rust
// This function takes ownership of a box and destroys it
fn eat_box_i32(boxed_i32: Box<i32>) {
    println!("Destroying box that contains {}", boxed_i32);
}

// This function borrows an i32
fn borrow_i32(borrowed_i32: &i32) {
    println!("This int is: {}", borrowed_i32);
}

fn main() {
    // Create a boxed i32 in the heap, and an i32 on the stack
    // Remember: numbers can have arbitrary underscores added for readability
    // 5_i32 is the same as 5i32
    let boxed_i32 = Box::new(5_i32);
    let stacked_i32 = 6_i32;

    // Borrow the contents of the box. Ownership is not taken,
    // so the contents can be borrowed again.
    borrow_i32(&boxed_i32);
    borrow_i32(&stacked_i32);

    {
        // Take a reference to the data contained inside the box
        let _ref_to_i32: &i32 = &boxed_i32;

        // Error!
        // Can't destroy `boxed_i32` while the inner value is borrowed later in scope.
        eat_box_i32(boxed_i32);
        // FIXME ^ Comment out this line

        // Attempt to borrow `_ref_to_i32` after inner value is destroyed
        borrow_i32(_ref_to_i32);
        // `_ref_to_i32` goes out of scope and is no longer borrowed.
    }

    // `boxed_i32` can now give up ownership to `eat_box_i32` and be destroyed
    eat_box_i32(boxed_i32);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Many readers OR one writer — not both.
- Borrows must not outlive the owner.

_RBE source: `scope/borrow.md`_

---

#### 16.1.3.1 Mutability

**What it is**

Mutable data can be mutably borrowed using `&mut T`. This is called
a *mutable reference* and gives read/write access to the borrower. In contrast, `&T` borrows the data via an immutable reference, and
the borrower can read the data but not modify it:
See also:
[`static`][static]
[static]: ../lifetime/static_lifetime.md.

**JS mental model**

&mut T exclusive mutable borrow — no other aliases at the same time.

**Rust example**

```rust
#[allow(dead_code)]
#[derive(Clone, Copy)]
struct Book {
    // `&'static str` is a reference to a string allocated in read only memory
    author: &'static str,
    title: &'static str,
    year: u32,
}

// This function takes a reference to a book
fn borrow_book(book: &Book) {
    println!("I immutably borrowed {} - {} edition", book.title, book.year);
}

// This function takes a reference to a mutable book and changes `year` to 2014
fn new_edition(book: &mut Book) {
    book.year = 2014;
    println!("I mutably borrowed {} - {} edition", book.title, book.year);
}

fn main() {
    // Create an immutable Book named `immutabook`
    let immutabook = Book {
        // string literals have type `&'static str`
        author: "Douglas Hofstadter",
        title: "Gödel, Escher, Bach",
        year: 1979,
    };

    // Create a mutable copy of `immutabook` and call it `mutabook`
    let mut mutabook = immutabook;

    // Immutably borrow an immutable object
    borrow_book(&immutabook);

    // Immutably borrow a mutable object
    borrow_book(&mutabook);

    // Borrow a mutable object as mutable
    new_edition(&mut mutabook);

    // Error! Cannot borrow an immutable object as mutable
    new_edition(&mut immutabook);
    // FIXME ^ Comment out this line
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- This prevents data races and many iterator invalidation bugs.

_RBE source: `scope/borrow/mut.md`_

---

#### 16.1.3.2 Aliasing

**What it is**

Data can be immutably borrowed any number of times, but while immutably
borrowed, the original data can't be mutably borrowed. On the other hand, only
*one* mutable borrow is allowed at a time. The original data can be borrowed
again only *after* the mutable reference has been used for the last time.

**JS mental model**

Aliasing rules: multiple &T OK; &mut T stands alone.

**Rust example**

```rust
struct Point { x: i32, y: i32, z: i32 }

fn main() {
    let mut point = Point { x: 0, y: 0, z: 0 };

    let borrowed_point = &point;
    let another_borrow = &point;

    // Data can be accessed via the references and the original owner
    println!("Point has coordinates: ({}, {}, {})",
                borrowed_point.x, another_borrow.y, point.z);

    // Error! Can't borrow `point` as mutable because it's currently
    // borrowed as immutable.
    // let mutable_borrow = &mut point;
    // TODO ^ Try uncommenting this line

    // The borrowed values are used again here
    println!("Point has coordinates: ({}, {}, {})",
                borrowed_point.x, another_borrow.y, point.z);

    // The immutable references are no longer used for the rest of the code so
    // it is possible to reborrow with a mutable reference.
    let mutable_borrow = &mut point;

    // Change data via mutable reference
    mutable_borrow.x = 5;
    mutable_borrow.y = 2;
    mutable_borrow.z = 1;

    // Error! Can't borrow `point` as immutable because it's currently
    // borrowed as mutable.
    // let y = &point.y;
    // TODO ^ Try uncommenting this line

    // Error! Can't print because `println!` takes an immutable reference.
    // println!("Point Z coordinate is {}", point.z);
    // TODO ^ Try uncommenting this line

    // Ok! Mutable references can be passed as immutable to `println!`
    println!("Point has coordinates: ({}, {}, {})",
                mutable_borrow.x, mutable_borrow.y, mutable_borrow.z);

    // The mutable reference is no longer used for the rest of the code so it
    // is possible to reborrow
    let new_borrowed_point = &point;
    println!("Point now has coordinates: ({}, {}, {})",
             new_borrowed_point.x, new_borrowed_point.y, new_borrowed_point.z);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Interior mutability (RefCell, Mutex) is the escape hatch with runtime checks.

_RBE source: `scope/borrow/alias.md`_

---

#### 16.1.3.3 The ref pattern

**What it is**

When doing pattern matching or destructuring via the `let` binding, the `ref`
keyword can be used to take references to the fields of a struct/tuple. The
example below shows a few instances where this can be useful:.

**JS mental model**

ref in patterns creates a reference to a matched value — older style; & patterns are common now.

**Rust example**

```rust
#[derive(Clone, Copy)]
struct Point { x: i32, y: i32 }

fn main() {
    let c = 'Q';

    // A `ref` borrow on the left side of an assignment is equivalent to
    // an `&` borrow on the right side.
    let ref ref_c1 = c;
    let ref_c2 = &c;

    println!("ref_c1 equals ref_c2: {}", *ref_c1 == *ref_c2);

    let point = Point { x: 0, y: 0 };

    // `ref` is also valid when destructuring a struct.
    let _copy_of_x = {
        // `ref_to_x` is a reference to the `x` field of `point`.
        let Point { x: ref ref_to_x, y: _ } = point;

        // Return a copy of the `x` field of `point`.
        *ref_to_x
    };

    // A mutable copy of `point`
    let mut mutable_point = point;

    {
        // `ref` can be paired with `mut` to take mutable references.
        let Point { x: _, y: ref mut mut_ref_to_y } = mutable_point;

        // Mutate the `y` field of `mutable_point` via a mutable reference.
        *mut_ref_to_y = 1;
    }

    println!("point is ({}, {})", point.x, point.y);
    println!("mutable_point is ({}, {})", mutable_point.x, mutable_point.y);

    // A mutable tuple that includes a pointer
    let mut mutable_tuple = (Box::new(5u32), 3u32);

    {
        // Destructure `mutable_tuple` to change the value of `last`.
        let (_, ref mut last) = mutable_tuple;
        *last = 2u32;
    }

    println!("tuple is {:?}", mutable_tuple);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Useful in match without moving out.

_RBE source: `scope/borrow/ref.md`_

---

### 16.1.4 Lifetimes

**What it is**

A *lifetime* is a construct the compiler (or more specifically, its *borrow
checker*) uses to ensure all borrows are valid. Specifically, a variable's
lifetime begins when it is created and ends when it is destroyed. While
lifetimes and scopes are often referred to together, they are not the same. Take, for example, the case where we borrow a variable via `&`.

**JS mental model**

Lifetimes 'a are names for how long a borrow is valid. Often inferred; sometimes annotated.

**Rust example**

```rust
// Lifetimes are annotated below with lines denoting the creation
// and destruction of each variable.
// `i` has the longest lifetime because its scope entirely encloses
// both `borrow1` and `borrow2`. The duration of `borrow1` compared
// to `borrow2` is irrelevant since they are disjoint.
fn main() {
    let i = 3; // Lifetime for `i` starts. ────────────────┐
    //                                                     │
    { //                                                   │
        let borrow1 = &i; // `borrow1` lifetime starts. ──┐│
        //                                                ││
        println!("borrow1: {}", borrow1); //              ││
    } // `borrow1` ends. ─────────────────────────────────┘│
    //                                                     │
    //                                                     │
    { //                                                   │
        let borrow2 = &i; // `borrow2` lifetime starts. ──┐│
        //                                                ││
        println!("borrow2: {}", borrow2); //              ││
    } // `borrow2` ends. ─────────────────────────────────┘│
    //                                                     │
}   // Lifetime ends. ─────────────────────────────────────┘
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Lifetimes do not create values — they describe relationships.
- JS does not annotate this because GC keeps objects alive.

_RBE source: `scope/lifetime.md`_

---

#### 16.1.4.1 Explicit annotation

**What it is**

The borrow checker uses explicit lifetime annotations to determine
how long references should be valid. In cases where lifetimes are not
elided[^1], Rust requires explicit annotations to determine what the
lifetime of a reference should be. The syntax for explicitly annotating
a lifetime uses an apostrophe character as follows:
Similar to [closures][anonymity], using lifetimes requires generics. Additionally, this lifetime syntax indicates that the lifetime of `foo`
may not exceed that of `'a`.

**JS mental model**

Write <'a> and &'a T when the compiler cannot infer relationships.

**Rust example**

```rust
// `print_refs` takes two references to `i32` which have different
// lifetimes `'a` and `'b`. These two lifetimes must both be at
// least as long as the function `print_refs`.
fn print_refs<'a, 'b>(x: &'a i32, y: &'b i32) {
    println!("x is {} and y is {}", x, y);
}

// A function which takes no arguments, but has a lifetime parameter `'a`.
fn failed_borrow<'a>() {
    let _x = 12;

    // ERROR: `_x` does not live long enough
    let _y: &'a i32 = &_x;
    // Attempting to use the lifetime `'a` as an explicit type annotation
    // inside the function will fail because the lifetime of `&_x` is shorter
    // than that of `_y`. A short lifetime cannot be coerced into a longer one.
}

fn main() {
    // Create variables to be borrowed below.
    let (four, nine) = (4, 9);

    // Borrows (`&`) of both variables are passed into the function.
    print_refs(&four, &nine);
    // Any input which is borrowed must outlive the borrower.
    // In other words, the lifetime of `four` and `nine` must
    // be longer than that of `print_refs`.

    failed_borrow();
    // `failed_borrow` contains no references to force `'a` to be
    // longer than the lifetime of the function, but `'a` is longer.
    // Because the lifetime is never constrained, it defaults to `'static`.
}
```

```rust
foo<'a, 'b>
// `foo` has lifetime parameters `'a` and `'b`
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Start from compiler error suggestions — they are good.

_RBE source: `scope/lifetime/explicit.md`_

---

#### 16.1.4.2 Functions

**What it is**

Ignoring [elision], function signatures with lifetimes have a few constraints:
* any reference *must* have an annotated lifetime. * any reference being returned *must* have the same lifetime as an input or
be `static`. Additionally, note that returning references without input is banned if it
would result in returning references to invalid data. The following example shows
off some valid forms of functions with lifetimes:
See also:
[Functions][fn]
[fn]: ../../fn.md
[elision]: elision.md.

**JS mental model**

Function signatures relate input borrow lifetimes to output borrows.

**Rust example**

```rust
// One input reference with lifetime `'a` which must live
// at least as long as the function.
fn print_one<'a>(x: &'a i32) {
    println!("`print_one`: x is {}", x);
}

// Mutable references are possible with lifetimes as well.
fn add_one<'a>(x: &'a mut i32) {
    *x += 1;
}

// Multiple elements with different lifetimes. In this case, it
// would be fine for both to have the same lifetime `'a`, but
// in more complex cases, different lifetimes may be required.
fn print_multi<'a, 'b>(x: &'a i32, y: &'b i32) {
    println!("`print_multi`: x is {}, y is {}", x, y);
}

// Returning references that have been passed in is acceptable.
// However, the correct lifetime must be returned.
fn pass_x<'a, 'b>(x: &'a i32, _: &'b i32) -> &'a i32 { x }

//fn invalid_output<'a>() -> &'a String { &String::from("foo") }
// The above is invalid: `'a` must live longer than the function.
// Here, `&String::from("foo")` would create a `String`, followed by a
// reference. Then the data is dropped upon exiting the scope, leaving
// a reference to invalid data to be returned.

fn main() {
    let x = 7;
    let y = 9;

    print_one(&x);
    print_multi(&x, &y);

    let z = pass_x(&x, &y);
    print_one(z);

    let mut t = 3;
    add_one(&mut t);
    print_one(&t);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Returning a reference to a local is illegal — dangling pointer prevented.

_RBE source: `scope/lifetime/fn.md`_

---

#### 16.1.4.3 Methods

**What it is**

Methods are annotated similarly to functions:
See also:
[methods]
[methods]: ../../fn/methods.md.

**JS mental model**

Methods can annotate lifetimes on &self and returned references.

**Rust example**

```rust
struct Owner(i32);

impl Owner {
    // Annotate lifetimes as in a standalone function.
    fn add_one<'a>(&'a mut self) { self.0 += 1; }
    fn print<'a>(&'a self) {
        println!("`print`: {}", self.0);
    }
}

fn main() {
    let mut owner = Owner(18);

    owner.add_one();
    owner.print();
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Often elided — write them when returning fields that borrow self.

_RBE source: `scope/lifetime/methods.md`_

---

#### 16.1.4.4 Structs

**What it is**

Annotation of lifetimes in structures are also similar to functions:
See also:
[`struct`s][structs]
[structs]: ../../custom_types/structs.md.

**JS mental model**

Structs that store references need lifetime fields: struct S<'a> { x: &'a str }.

**Rust example**

```rust
// A type `Borrowed` which houses a reference to an
// `i32`. The reference to `i32` must outlive `Borrowed`.
#[derive(Debug)]
struct Borrowed<'a>(&'a i32);

// Similarly, both references here must outlive this structure.
#[derive(Debug)]
struct NamedBorrowed<'a> {
    x: &'a i32,
    y: &'a i32,
}

// An enum which is either an `i32` or a reference to one.
#[derive(Debug)]
enum Either<'a> {
    Num(i32),
    Ref(&'a i32),
}

fn main() {
    let x = 18;
    let y = 15;

    let single = Borrowed(&x);
    let double = NamedBorrowed { x: &x, y: &y };
    let reference = Either::Ref(&x);
    let number    = Either::Num(y);

    println!("x is borrowed in {:?}", single);
    println!("x and y are borrowed in {:?}", double);
    println!("x is borrowed in {:?}", reference);
    println!("y is *not* borrowed in {:?}", number);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- The struct cannot outlive the data it borrows.

_RBE source: `scope/lifetime/struct.md`_

---

#### 16.1.4.5 Traits

**What it is**

Annotation of lifetimes in trait methods basically are similar to functions. Note that `impl` may have annotation of lifetimes too. See also:
[`trait`s][trait]
[trait]: ../../trait.md.

**JS mental model**

Traits can involve lifetimes too — e.g. methods returning borrows.

**Rust example**

```rust
// A struct with annotation of lifetimes.
#[derive(Debug)]
struct Borrowed<'a> {
    x: &'a i32,
}

// Annotate lifetimes to impl.
impl<'a> Default for Borrowed<'a> {
    fn default() -> Self {
        Self {
            x: &10,
        }
    }
}

fn main() {
    let b: Borrowed = Default::default();
    println!("b is {:?}", b);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Object safety / dyn can complicate lifetimes — advanced.

_RBE source: `scope/lifetime/trait.md`_

---

#### 16.1.4.6 Bounds

**What it is**

Just like generic types can be bounded, lifetimes (themselves generic)
use bounds as well. The `:` character has a slightly different meaning here,
but `+` is the same. Note how the following read:
1. `T: 'a`: *All* references in `T` must outlive lifetime `'a`.

**JS mental model**

T: 'a means T must live at least as long as 'a.

**Rust example**

```rust
use std::fmt::Debug; // Trait to bound with.

#[derive(Debug)]
struct Ref<'a, T: 'a>(&'a T);
// `Ref` contains a reference to a generic type `T` that has
// some lifetime `'a` unknown by `Ref`. `T` is bounded such that any
// *references* in `T` must outlive `'a`. Additionally, the lifetime
// of `Ref` may not exceed `'a`.

// A generic function which prints using the `Debug` trait.
fn print<T>(t: T) where
    T: Debug {
    println!("`print`: t is {:?}", t);
}

// Here a reference to `T` is taken where `T` implements
// `Debug` and all *references* in `T` outlive `'a`. In
// addition, `'a` must outlive the function.
fn print_ref<'a, T>(t: &'a T) where
    T: Debug + 'a {
    println!("`print_ref`: t is {:?}", t);
}

fn main() {
    let x = 7;
    let ref_x = Ref(&x);

    print_ref(&ref_x);
    print(ref_x);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Common when storing references alongside generic data.

_RBE source: `scope/lifetime/lifetime_bounds.md`_

---

#### 16.1.4.7 Coercion

**What it is**

A longer lifetime can be coerced into a shorter one
so that it works inside a scope it normally wouldn't work in. This comes in the form of inferred coercion by the Rust compiler,
and also in the form of declaring a lifetime difference:.

**JS mental model**

Longer lifetimes can coerce to shorter ones (subtype-ish).

**Rust example**

```rust
// Here, Rust infers a lifetime that is as short as possible.
// The two references are then coerced to that lifetime.
fn multiply<'a>(first: &'a i32, second: &'a i32) -> i32 {
    first * second
}

// `<'a: 'b, 'b>` reads as lifetime `'a` is at least as long as `'b`.
// Here, we take in an `&'a i32` and return a `&'b i32` as a result of coercion.
fn choose_first<'a: 'b, 'b>(first: &'a i32, _: &'b i32) -> &'b i32 {
    first
}

fn main() {
    let first = 2; // Longer lifetime

    {
        let second = 3; // Shorter lifetime

        println!("The product is {}", multiply(&first, &second));
        println!("{} is the first", choose_first(&first, &second));
    };
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- You can shorten; you cannot invent longer lifetimes.

_RBE source: `scope/lifetime/lifetime_coercion.md`_

---

#### 16.1.4.8 Static

**What it is**

Rust has a few reserved lifetime names. One of those is `'static`. You
might encounter it in two situations:
Both are related but subtly different and this is a common source for
confusion when learning Rust. Here are some examples for each situation:
Reference lifetime
As a reference lifetime `'static` indicates that the data pointed to by
the reference lives for the remaining lifetime of the running program.

**JS mental model**

 'static lives for the whole program — string literals are 'static.

**Rust example**

```rust
// Make a constant with `'static` lifetime.
static NUM: i32 = 18;

// Returns a reference to `NUM` where its `'static`
// lifetime is coerced to that of the input argument.
fn coerce_static<'a>(_: &'a i32) -> &'a i32 {
    &NUM
}

fn main() {
    {
        // Make a `string` literal and print it:
        let static_string = "I'm in read-only memory";
        println!("static_string: {}", static_string);

        // When `static_string` goes out of scope, the reference
        // can no longer be used, but the data remains in the binary.
    }

    {
        // Make an integer to use for `coerce_static`:
        let lifetime_num = 9;

        // Coerce `NUM` to lifetime of `lifetime_num`:
        let coerced_static = coerce_static(&lifetime_num);

        println!("coerced_static: {}", coerced_static);
    }

    println!("NUM: {} stays accessible!", NUM);
}
```

```rust
use std::fmt::Debug;

fn print_it(input: impl Debug + 'static) {
    println!("'static value passed in is: {:?}", input);
}

fn main() {
    // i is owned and contains no references, thus it's 'static:
    let i = 5;
    print_it(i);

    // oops, &i only has the lifetime defined by the scope of
    // main(), so it's not 'static:
    print_it(&i);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Not every &'static needs to be a literal — also leaked/owned globals.
- Do not overuse 'static bounds on generics.

_RBE source: `scope/lifetime/static_lifetime.md`_

---

#### 16.1.4.9 Elision

**What it is**

Some lifetime patterns are overwhelmingly common and so the borrow checker
will allow you to omit them to save typing and to improve readability. This is known as elision. Elision exists in Rust solely because these patterns
are common. The following code shows a few examples of elision.

**JS mental model**

Lifetime rules let you omit lifetimes in common function shapes.

**Rust example**

```rust
// `elided_input` and `annotated_input` essentially have identical signatures
// because the lifetime of `elided_input` is inferred by the compiler:
fn elided_input(x: &i32) {
    println!("`elided_input`: {}", x);
}

fn annotated_input<'a>(x: &'a i32) {
    println!("`annotated_input`: {}", x);
}

// Similarly, `elided_pass` and `annotated_pass` have identical signatures
// because the lifetime is added implicitly to `elided_pass`:
fn elided_pass(x: &i32) -> &i32 { x }

fn annotated_pass<'a>(x: &'a i32) -> &'a i32 { x }

fn main() {
    let x = 3;

    elided_input(&x);
    annotated_input(&x);

    println!("`elided_pass`: {}", elided_pass(&x));
    println!("`annotated_pass`: {}", annotated_pass(&x));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- If elision cannot decide, the compiler asks for explicit annotations.

_RBE source: `scope/lifetime/elision.md`_

---
