# Traits

_RBE chapter root maps here. Every nested leaf below has the full 5-part teaching block._

### 17.1 Traits

**What it is**

A `trait` is a collection of methods defined for an unknown type:
`Self`. They can access other methods declared in the same trait. Traits can be implemented for any data type. In the example below,
we define `Animal`, a group of methods.

**JS mental model**

Traits are like interfaces / protocols. Types opt in with impl Trait for Type.

**Rust example**

```rust
struct Sheep { naked: bool, name: &'static str }

trait Animal {
    // Associated function signature; `Self` refers to the implementor type.
    fn new(name: &'static str) -> Self;

    // Method signatures; these will return a string.
    fn name(&self) -> &'static str;
    fn noise(&self) -> &'static str;

    // Traits can provide default method definitions.
    fn talk(&self) {
        println!("{} says {}", self.name(), self.noise());
    }
}

impl Sheep {
    fn is_naked(&self) -> bool {
        self.naked
    }

    fn shear(&mut self) {
        if self.is_naked() {
            // Implementor methods can use the implementor's trait methods.
            println!("{} is already naked...", self.name());
        } else {
            println!("{} gets a haircut!", self.name);

            self.naked = true;
        }
    }
}

// Implement the `Animal` trait for `Sheep`.
impl Animal for Sheep {
    // `Self` is the implementor type: `Sheep`.
    fn new(name: &'static str) -> Sheep {
        Sheep { name: name, naked: false }
    }

    fn name(&self) -> &'static str {
        self.name
    }

    fn noise(&self) -> &'static str {
        if self.is_naked() {
            "baaaaah?"
        } else {
            "baaaaah!"
        }
    }

    // Default trait methods can be overridden.
    fn talk(&self) {
        // For example, we can add some quiet contemplation.
        println!("{} pauses briefly... {}", self.name, self.noise());
    }
}

fn main() {
    // Type annotation is necessary in this case.
    let mut dolly: Sheep = Animal::new("Dolly");
    // TODO ^ Try removing the type annotations.

    dolly.talk();
    dolly.shear();
    dolly.talk();
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Orphan rules limit impls — one of trait/type must be local.
- Default methods allowed.

_RBE source: `trait.md`_

---

### 17.1.1 Derive

**What it is**

The compiler is capable of providing basic implementations for some traits via
the `#[derive]` [attribute][attribute]. These traits can still be
manually implemented if a more complex behavior is required. The following is a list of derivable traits:
* Comparison traits:
  [`Eq`][eq], [`PartialEq`][partial-eq], [`Ord`][ord], [`PartialOrd`][partial-ord]. * [`Clone`][clone], to create `T` from `&T` via a copy.

**JS mental model**

#[derive(Debug, Clone)] auto-implements common traits — like generated methods.

**Rust example**

```rust
// `Centimeters`, a tuple struct that can be compared
#[derive(PartialEq, PartialOrd)]
struct Centimeters(f64);

// `Inches`, a tuple struct that can be printed
#[derive(Debug)]
struct Inches(i32);

impl Inches {
    fn to_centimeters(&self) -> Centimeters {
        let &Inches(inches) = self;

        Centimeters(inches as f64 * 2.54)
    }
}

// `Seconds`, a tuple struct with no additional attributes
struct Seconds(i32);

fn main() {
    let _one_second = Seconds(1);

    // Error: `Seconds` can't be printed; it doesn't implement the `Debug` trait
    //println!("One second looks like: {:?}", _one_second);
    // TODO ^ Try uncommenting this line

    // Error: `Seconds` can't be compared; it doesn't implement the `PartialEq` trait
    //let _this_is_true = (_one_second == _one_second);
    // TODO ^ Try uncommenting this line

    let foot = Inches(12);

    println!("One foot equals {:?}", foot);

    let meter = Centimeters(100.0);

    let cmp =
        if foot.to_centimeters() < meter {
            "smaller"
        } else {
            "bigger"
        };

    println!("One foot is {} than one meter.", cmp);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Only works for traits that support derive.
- Fields must also implement the trait.

_RBE source: `trait/derive.md`_

---

### 17.1.2 Returning Traits with dyn

**What it is**

The Rust compiler needs to know how much space every function's return type requires. This means all
your functions have to return a concrete type. Unlike other languages, if you have a trait like
`Animal`, you can't write a function that returns `Animal`, because its different implementations
will need different amounts of memory. However, there's an easy workaround.

**JS mental model**

Box<dyn Trait> is dynamic dispatch (vtable) like interface references in OOP. impl Trait is static.

**Rust example**

```rust
struct Sheep {}
struct Cow {}

trait Animal {
    // Instance method signature
    fn noise(&self) -> &'static str;
}

// Implement the `Animal` trait for `Sheep`.
impl Animal for Sheep {
    fn noise(&self) -> &'static str {
        "baaaaah!"
    }
}

// Implement the `Animal` trait for `Cow`.
impl Animal for Cow {
    fn noise(&self) -> &'static str {
        "moooooo!"
    }
}

// Returns some struct that implements Animal, but we don't know which one at compile time.
fn random_animal(random_number: f64) -> Box<dyn Animal> {
    if random_number < 0.5 {
        Box::new(Sheep {})
    } else {
        Box::new(Cow {})
    }
}

fn main() {
    let random_number = 0.234;
    let animal = random_animal(random_number);
    println!("You've randomly chosen an animal, and it says {}", animal.noise());
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- dyn has a small runtime cost; size must be boxed/referenced.
- Not all traits are object-safe.

_RBE source: `trait/dyn.md`_

---

### 17.1.3 Operator Overloading

**What it is**

In Rust, many of the operators can be overloaded via traits. That is, some operators can
be used to accomplish different tasks based on their input arguments. This is possible
because operators are syntactic sugar for method calls. For example, the `+` operator in
`a + b` calls the `add` method (as in `a.add(b)`).

**JS mental model**

Overload + etc. by implementing std::ops traits — explicit and typed.

**Rust example**

```rust
use std::ops;

struct Foo;
struct Bar;

#[derive(Debug)]
struct FooBar;

#[derive(Debug)]
struct BarFoo;

// The `std::ops::Add` trait is used to specify the functionality of `+`.
// Here, we make `Add<Bar>` - the trait for addition with a RHS of type `Bar`.
// The following block implements the operation: Foo + Bar = FooBar
impl ops::Add<Bar> for Foo {
    type Output = FooBar;

    fn add(self, _rhs: Bar) -> FooBar {
        println!("> Foo.add(Bar) was called");

        FooBar
    }
}

// By reversing the types, we end up implementing non-commutative addition.
// Here, we make `Add<Foo>` - the trait for addition with a RHS of type `Foo`.
// This block implements the operation: Bar + Foo = BarFoo
impl ops::Add<Foo> for Bar {
    type Output = BarFoo;

    fn add(self, _rhs: Foo) -> BarFoo {
        println!("> Bar.add(Foo) was called");

        BarFoo
    }
}

fn main() {
    println!("Foo + Bar = {:?}", Foo + Bar);
    println!("Bar + Foo = {:?}", Bar + Foo);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Keep operators intuitive.
- Prefer named methods if meaning is unclear.

_RBE source: `trait/ops.md`_

---

### 17.1.4 Drop

**What it is**

The [`Drop`][Drop] trait only has one method: `drop`, which is called automatically
when an object goes out of scope. The main use of the `Drop` trait is to free the
resources that the implementor instance owns. `Box`, `Vec`, `String`, `File`, and `Process` are some examples of types that
implement the `Drop` trait to free resources. The `Drop` trait can also be
manually implemented for any custom data type.

**JS mental model**

Custom destructor via Drop — like a finalizer that actually runs at known time.

**Rust example**

```rust
use std::fs::File;
use std::path::PathBuf;

struct TempFile {
    file: File,
    path: PathBuf,
}

impl TempFile {
    fn new(path: PathBuf) -> std::io::Result<Self> {
        // Note: File::create() will overwrite existing files
        let file = File::create(&path)?;

        Ok(Self { file, path })
    }
}

// When TempFile is dropped:
// 1. First, our custom drop implementation runs. The file is still open at this point,
//    but we can remove it from the filesystem by path.
// 2. Then, after our drop returns, Rust automatically drops each field,
//    so File's drop runs and closes the file handle.
impl Drop for TempFile {
    fn drop(&mut self) {
        // Note: the File is still open here — field destructors run after this method.
        if let Err(e) = std::fs::remove_file(&self.path) {
            eprintln!("Failed to remove temporary file: {}", e);
        }
        println!("> Dropped temporary file: {:?}", self.path);
        // After this method returns, Rust will drop each field (including `file`),
        // which closes the underlying file handle.
    }
}

fn main() -> std::io::Result<()> {
    // Create a new scope to demonstrate drop behavior
    {
        let temp = TempFile::new("test.txt".into())?;
        println!("Temporary file created");
        // File will be automatically cleaned up when temp goes out of scope
    }
    println!("End of scope - file should be cleaned up");

    // We can also manually drop if needed
    let temp2 = TempFile::new("another_test.txt".into())?;
    drop(temp2); // Explicitly drop the file
    println!("Manually dropped file");

    Ok(())
}
```

```rust
struct Droppable {
    name: &'static str,
}

// This trivial implementation of `drop` adds a print to console.
impl Drop for Droppable {
    fn drop(&mut self) {
        println!("> Dropping {}", self.name);
    }
}

fn main() {
    let _a = Droppable { name: "a" };

    // block A
    {
        let _b = Droppable { name: "b" };

        // block B
        {
            let _c = Droppable { name: "c" };
            let _d = Droppable { name: "d" };

            println!("Exiting block B");
        }
        println!("Just exited block B");

        println!("Exiting block A");
    }
    println!("Just exited block A");

    // Variable can be manually dropped using the `drop` function
    drop(_a);
    // TODO ^ Try commenting this line

    println!("end of the main function");

    // `_a` *won't* be `drop`ed again here, because it already has been
    // (manually) `drop`ed
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Do not panic in Drop casually.
- Drop order: fields after the Drop impl runs.

_RBE source: `trait/drop.md`_

---

### 17.1.5 Iterators

**What it is**

The [`Iterator`][iter] trait is used to implement iterators over collections
such as arrays. The trait requires only a method to be defined for the `next` element,
which may be manually defined in an `impl` block or automatically
defined (as in arrays and ranges). As a point of convenience for common situations, the `for` construct
turns some collections into iterators using the [`.into_iter()`][intoiter] method. [intoiter]: https://doc.rust-lang.org/std/iter/trait.IntoIterator.html
[iter]: https://doc.rust-lang.org/core/iter/trait.Iterator.html.

**JS mental model**

Implement Iterator for your type — like making an object iterable ([Symbol.iterator]).

**Rust example**

```rust
struct Fibonacci {
    curr: u32,
    next: u32,
}

// Implement `Iterator` for `Fibonacci`.
// The `Iterator` trait only requires a method to be defined for the `next` element,
// and an `associated type` to declare the return type of the iterator.
impl Iterator for Fibonacci {
    // We can refer to this type using Self::Item
    type Item = u32;

    // Here, we define the sequence using `.curr` and `.next`.
    // The return type is `Option<T>`:
    //     * When the `Iterator` is finished, `None` is returned.
    //     * Otherwise, the next value is wrapped in `Some` and returned.
    // We use Self::Item in the return type, so we can change
    // the type without having to update the function signatures.
    fn next(&mut self) -> Option<Self::Item> {
        let current = self.curr;

        self.curr = self.next;
        self.next = current + self.next;

        // Since there's no endpoint to a Fibonacci sequence, the `Iterator`
        // will never return `None`, and `Some` is always returned.
        Some(current)
    }
}

// Returns a Fibonacci sequence generator
fn fibonacci() -> Fibonacci {
    Fibonacci { curr: 0, next: 1 }
}

fn main() {
    // `0..3` is an `Iterator` that generates: 0, 1, and 2.
    let mut sequence = 0..3;

    println!("Four consecutive `next` calls on 0..3");
    println!("> {:?}", sequence.next());
    println!("> {:?}", sequence.next());
    println!("> {:?}", sequence.next());
    println!("> {:?}", sequence.next());

    // `for` works through an `Iterator` until it returns `None`.
    // Each `Some` value is unwrapped and bound to a variable (here, `i`).
    println!("Iterate through 0..3 using `for`");
    for i in 0..3 {
        println!("> {}", i);
    }

    // The `take(n)` method reduces an `Iterator` to its first `n` terms.
    println!("The first four terms of the Fibonacci sequence are: ");
    for i in fibonacci().take(4) {
        println!("> {}", i);
    }

    // The `skip(n)` method shortens an `Iterator` by dropping its first `n` terms.
    println!("The next four terms of the Fibonacci sequence are: ");
    for i in fibonacci().skip(4).take(4) {
        println!("> {}", i);
    }

    let array = [1u32, 3, 3, 7];

    // The `iter` method produces an `Iterator` over an array/slice.
    println!("Iterate the following array {:?}", &array);
    for i in array.iter() {
        println!("> {}", i);
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Just implement next returning Option<Item>.
- Adapters come for free.

_RBE source: `trait/iter.md`_

---

### 17.1.6 impl Trait

**What it is**

`impl Trait` can be used in two locations:
1. as an argument type
2. as a return type
As an argument type
If your function is generic over a trait but you don't mind the specific type, you can simplify the function declaration using `impl Trait` as the type of the argument. For example, consider the following code:
`parse_csv_document` is generic, allowing it to take any type which implements BufRead, such as `BufReader<File>` or `[u8]`,
but it's not important what type `R` is, and `R` is only used to declare the type of `src`, so the function can also be written as:
Note that using `impl Trait` as an argument type means that you cannot explicitly state what form of the function you use, i.e.

**JS mental model**

impl Trait in return position hides a concrete type — like returning some iterable without naming it.

**Rust example**

```rust
use std::iter;
use std::vec::IntoIter;

// This function combines two `Vec<i32>` and returns an iterator over it.
// Look how complicated its return type is!
fn combine_vecs_explicit_return_type(
    v: Vec<i32>,
    u: Vec<i32>,
) -> iter::Cycle<iter::Chain<IntoIter<i32>, IntoIter<i32>>> {
    v.into_iter().chain(u.into_iter()).cycle()
}

// This is the exact same function, but its return type uses `impl Trait`.
// Look how much simpler it is!
fn combine_vecs(
    v: Vec<i32>,
    u: Vec<i32>,
) -> impl Iterator<Item=i32> {
    v.into_iter().chain(u.into_iter()).cycle()
}

fn main() {
    let v1 = vec![1, 2, 3];
    let v2 = vec![4, 5];
    let mut v3 = combine_vecs(v1, v2);
    assert_eq!(Some(1), v3.next());
    assert_eq!(Some(2), v3.next());
    assert_eq!(Some(3), v3.next());
    assert_eq!(Some(4), v3.next());
    assert_eq!(Some(5), v3.next());
    println!("all done");
}
```

```rust
fn parse_csv_document<R: std::io::BufRead>(src: R) -> std::io::Result<Vec<Vec<String>>> {
    src.lines()
        .map(|line| {
            // For each line in the source
            line.map(|line| {
                // If the line was read successfully, process it, if not, return the error
                line.split(',') // Split the line separated by commas
                    .map(|entry| String::from(entry.trim())) // Remove leading and trailing whitespace
                    .collect() // Collect all strings in a row into a Vec<String>
            })
        })
        .collect() // Collect all lines into a Vec<Vec<String>>
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Also usable in argument position as anonymous generics.
- Different from dyn Trait.

_RBE source: `trait/impl_trait.md`_

---

### 17.1.7 Clone

**What it is**

When dealing with resources, the default behavior is to transfer them during
assignments or function calls. However, sometimes we need to make a
copy of the resource as well. The [`Clone`][clone] trait helps us do exactly this. Most commonly, we can
use the `.clone()` method defined by the `Clone` trait.

**JS mental model**

Clone is explicit copy via .clone() — JS copies are usually shallow by default and silent.

**Rust example**

```rust
// A unit struct without resources
// Note: Copy requires Clone, so we must derive both
#[derive(Debug, Clone, Copy)]
struct Unit;

// A tuple struct with resources that implements the `Clone` trait
// This CANNOT be Copy because Box<T> is not Copy
#[derive(Clone, Debug)]
struct Pair(Box<i32>, Box<i32>);

fn main() {
    // Instantiate `Unit`
    let unit = Unit;
    // Copy `Unit` - this is an implicit copy, not a move!
    // Because Unit implements Copy, the value is duplicated automatically
    let copied_unit = unit;

    // Both `Unit`s can be used independently
    println!("original: {:?}", unit);
    println!("copy: {:?}", copied_unit);

    // Instantiate `Pair`
    let pair = Pair(Box::new(1), Box::new(2));
    println!("original: {:?}", pair);

    // Move `pair` into `moved_pair`, moves resources
    // Pair does not implement Copy, so this is a move
    let moved_pair = pair;
    println!("moved: {:?}", moved_pair);

    // Error! `pair` has lost its resources
    //println!("original: {:?}", pair);
    // TODO ^ Try uncommenting this line

    // Clone `moved_pair` into `cloned_pair` (resources are included)
    // Unlike Copy, Clone is explicit - we must call .clone()
    let cloned_pair = moved_pair.clone();
    // Drop the moved original pair using std::mem::drop
    drop(moved_pair);

    // Error! `moved_pair` has been dropped
    //println!("moved and dropped: {:?}", moved_pair);
    // TODO ^ Try uncommenting this line

    // The result from .clone() can still be used!
    println!("clone: {:?}", cloned_pair);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Copy is implicit bitwise copy for simple types.
- Derive when all fields allow it.

_RBE source: `trait/clone.md`_

---

### 17.1.8 Supertraits

**What it is**

Rust doesn't have "inheritance", but you can define a trait as being a superset
of another trait. For example:
See also:
[The Rust Programming Language chapter on supertraits][trpl_supertraits]
[trpl_supertraits]: https://doc.rust-lang.org/book/ch19-03-advanced-traits.html#using-supertraits-to-require-one-traits-functionality-within-another-trait.

**JS mental model**

trait B: A means B requires A — like interface extends.

**Rust example**

```rust
trait Person {
    fn name(&self) -> String;
}

// Person is a supertrait of Student.
// Implementing Student requires you to also impl Person.
trait Student: Person {
    fn university(&self) -> String;
}

trait Programmer {
    fn fav_language(&self) -> String;
}

// CompSciStudent (computer science student) is a subtrait of both Programmer
// and Student. Implementing CompSciStudent requires you to impl both supertraits.
trait CompSciStudent: Programmer + Student {
    fn git_username(&self) -> String;
}

fn comp_sci_student_greeting(student: &dyn CompSciStudent) -> String {
    format!(
        "My name is {} and I attend {}. My favorite language is {}. My Git username is {}",
        student.name(),
        student.university(),
        student.fav_language(),
        student.git_username()
    )
}

struct CSStudent {
    name: String,
    university: String,
    fav_language: String,
    git_username: String
}

impl Programmer for CSStudent {
    fn fav_language(&self) -> String {
        self.fav_language.clone()
    }
}

impl Student for CSStudent {
    fn university(&self) -> String {
        self.university.clone()
    }
}

impl Person for CSStudent {
    fn name(&self) -> String {
        self.name.clone()
    }
}

impl CompSciStudent for CSStudent {
    fn git_username(&self) -> String {
        self.git_username.clone()
    }
}

fn main() {
    let student = CSStudent {
        name: String::from("Alice"),
        university: String::from("MIT"),
        fav_language: String::from("Rust"),
        git_username: String::from("alice_codes"),
    };

    let greeting = comp_sci_student_greeting(&student);
    println!("{}", greeting);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Implementing B requires A's methods too.

_RBE source: `trait/supertraits.md`_

---

### 17.1.9 Disambiguating overlapping traits

**What it is**

A type can implement many different traits. What if two traits both require
the same name for a function? For example, many traits might have a method
named `get()`. They might even have different return types!

**JS mental model**

When two traits define the same method name, write Trait::method(&x).

**Rust example**

```rust
trait UsernameWidget {
    // Get the selected username out of this widget
    fn get(&self) -> String;
}

trait AgeWidget {
    // Get the selected age out of this widget
    fn get(&self) -> u8;
}

// A form with both a UsernameWidget and an AgeWidget
struct Form {
    username: String,
    age: u8,
}

impl UsernameWidget for Form {
    fn get(&self) -> String {
        self.username.clone()
    }
}

impl AgeWidget for Form {
    fn get(&self) -> u8 {
        self.age
    }
}

fn main() {
    let form = Form {
        username: "rustacean".to_owned(),
        age: 28,
    };

    // If you uncomment this line, you'll get an error saying
    // "multiple `get` found". Because, after all, there are multiple methods
    // named `get`.
    // println!("{}", form.get());

    let username = <Form as UsernameWidget>::get(&form);
    assert_eq!("rustacean".to_owned(), username);
    let age = <Form as AgeWidget>::get(&form);
    assert_eq!(28, age);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Same idea as name clashes with mixins — Rust forces clarity.

_RBE source: `trait/disambiguating.md`_

---
