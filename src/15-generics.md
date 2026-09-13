# Generics

_RBE chapter root maps here. Every nested leaf below has the full 5-part teaching block._

### 15.1 Generics

**What it is**

*Generics* is the topic of generalizing types and functionalities to broader
cases. This is extremely useful for reducing code duplication in many ways,
but can call for rather involved syntax. Namely, being generic requires
taking great care to specify over which types a generic type
is actually considered valid. The simplest and most common use of generics
is for type parameters.

**JS mental model**

Generics are like TypeScript generics <T>, but monomorphized (compiled per type) for speed — no runtime generic objects.

**Rust example**

```rust
// A concrete type `A`.
struct A;

// In defining the type `Single`, the first use of `A` is not preceded by `<A>`.
// Therefore, `Single` is a concrete type, and `A` is defined as above.
struct Single(A);
//            ^ Here is `Single`s first use of the type `A`.

// Here, `<T>` precedes the first use of `T`, so `SingleGen` is a generic type.
// Because the type parameter `T` is generic, it could be anything, including
// the concrete type `A` defined at the top.
struct SingleGen<T>(T);

fn main() {
    // `Single` is concrete and explicitly takes `A`.
    let _s = Single(A);

    // Create a variable `_char` of type `SingleGen<char>`
    // and give it the value `SingleGen('a')`.
    // Here, `SingleGen` has a type parameter explicitly specified.
    let _char: SingleGen<char> = SingleGen('a');

    // `SingleGen` can also have a type parameter implicitly specified:
    let _t    = SingleGen(A); // Uses `A` defined at the top.
    let _i32  = SingleGen(6); // Uses `i32`.
    let _char = SingleGen('a'); // Uses `char`.
}
```

```rust
fn foo<T>(arg: T) { ... }
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Turbofish ::<> helps when inference fails.
- Bounds say what traits T must implement.

_RBE source: `generics.md`_

---

### 15.1.1 Functions

**What it is**

The same set of rules can be applied to functions: a type `T` becomes
generic when preceded by `<T>`. Using generic functions sometimes requires explicitly specifying type
parameters. This may be the case if the function is called where the return type
is generic, or if the compiler doesn't have enough information to infer
the necessary type parameters. A function call with explicitly specified type parameters looks like:
`fun::<A, B, ...>()`.

**JS mental model**

Generic functions: fn foo<T>(x: T) like function foo<T>(x: T).

**Rust example**

```rust
struct A;          // Concrete type `A`.
struct S(A);       // Concrete type `S`.
struct SGen<T>(T); // Generic type `SGen`.

// The following functions all take ownership of the variable passed into
// them and immediately go out of scope, freeing the variable.

// Define a function `reg_fn` that takes an argument `_s` of type `S`.
// This has no `<T>` so this is not a generic function.
fn reg_fn(_s: S) {}

// Define a function `gen_spec_t` that takes an argument `_s` of type `SGen<T>`.
// It has been explicitly given the type parameter `A`, but because `A` has not
// been specified as a generic type parameter for `gen_spec_t`, it is not generic.
fn gen_spec_t(_s: SGen<A>) {}

// Define a function `gen_spec_i32` that takes an argument `_s` of type `SGen<i32>`.
// It has been explicitly given the type parameter `i32`, which is a specific type.
// Because `i32` is not a generic type, this function is also not generic.
fn gen_spec_i32(_s: SGen<i32>) {}

// Define a function `generic` that takes an argument `_s` of type `SGen<T>`.
// Because `SGen<T>` is preceded by `<T>`, this function is generic over `T`.
fn generic<T>(_s: SGen<T>) {}

fn main() {
    // Using the non-generic functions
    reg_fn(S(A));          // Concrete type.
    gen_spec_t(SGen(A));   // Implicitly specified type parameter `A`.
    gen_spec_i32(SGen(6)); // Implicitly specified type parameter `i32`.

    // Explicitly specified type parameter `char` to `generic()`.
    generic::<char>(SGen('a'));

    // Implicitly specified type parameter `char` to `generic()`.
    generic(SGen('c'));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Caller usually does not write <T> — inference does.

_RBE source: `generics/gen_fn.md`_

---

### 15.1.2 Implementation

**What it is**

Similar to functions, implementations require care to remain generic. See also:
[functions returning references][fn], [`impl`][methods], and [`struct`][structs]
[fn]: ../scope/lifetime/fn.md
[methods]: ../fn/methods.md
[specialization_plans]: https://blog.rust-lang.org/2015/05/11/traits.html#the-future
[structs]: ../custom_types/structs.md.

**JS mental model**

impl<T> Type<T> { ... } — methods for every T, like generic class methods.

**Rust example**

```rust
struct Val {
    val: f64,
}

struct GenVal<T> {
    gen_val: T,
}

// impl of Val
impl Val {
    fn value(&self) -> &f64 {
        &self.val
    }
}

// impl of GenVal for a generic type `T`
impl<T> GenVal<T> {
    fn value(&self) -> &T {
        &self.gen_val
    }
}

fn main() {
    let x = Val { val: 3.0 };
    let y = GenVal { gen_val: 3i32 };

    println!("{}, {}", x.value(), y.value());
}
```

```rust
struct S; // Concrete type `S`
struct GenericVal<T>(T); // Generic type `GenericVal`

// impl of GenericVal where we explicitly specify type parameters:
impl GenericVal<f32> {} // Specify `f32`
impl GenericVal<S> {} // Specify `S` as defined above

// `<T>` Must precede the type to remain generic
impl<T> GenericVal<T> {}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- You can also impl Type<Specific>.
- Separate impl blocks are fine.

_RBE source: `generics/impl.md`_

---

### 15.1.3 Traits

**What it is**

Of course `trait`s can also be generic. Here we define one which reimplements
the `Drop` `trait` as a generic method to `drop` itself and an input. See also:
[`Drop`][Drop], [`struct`][structs], and [`trait`][traits]
[Drop]: https://doc.rust-lang.org/std/ops/trait.Drop.html
[structs]: ../custom_types/structs.md
[traits]: ../trait.md.

**JS mental model**

Traits can be generic too — like generic interfaces.

**Rust example**

```rust
// Non-copyable types.
struct Empty;
struct Null;

// A trait generic over `T`.
trait DoubleDrop<T> {
    // Define a method on the caller type which takes an
    // additional single parameter `T` and does nothing with it.
    fn double_drop(self, _: T);
}

// Implement `DoubleDrop<T>` for any generic parameter `T` and
// caller `U`.
impl<T, U> DoubleDrop<T> for U {
    // This method takes ownership of both passed arguments,
    // deallocating both.
    fn double_drop(self, _: T) {}
}

fn main() {
    let empty = Empty;
    let null  = Null;

    // Deallocate `empty` and `null`.
    empty.double_drop(null);

    //empty;
    //null;
    // ^ TODO: Try uncommenting these lines.
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Associated types are often clearer than extra type params (see later).

_RBE source: `generics/gen_trait.md`_

---

### 15.1.4 Bounds

**What it is**

When working with generics, the type parameters often must use traits as *bounds* to
stipulate what functionality a type implements. For example, the following
example uses the trait `Display` to print and so it requires `T` to be bound
by `Display`; that is, `T` *must* implement `Display`. Bounding restricts the generic to types that conform to the bounds. That is:
Another effect of bounding is that generic instances are allowed to access the
[methods] of traits specified in the bounds.

**JS mental model**

T: Display means T must implement Display — like T extends Foo in TS.

**Rust example**

```rust
// A trait which implements the print marker: `{:?}`.
use std::fmt::Debug;

trait HasArea {
    fn area(&self) -> f64;
}

impl HasArea for Rectangle {
    fn area(&self) -> f64 { self.length * self.height }
}

#[derive(Debug)]
struct Rectangle { length: f64, height: f64 }
#[allow(dead_code)]
struct Triangle  { length: f64, height: f64 }

// The generic `T` must implement `Debug`. Regardless
// of the type, this will work properly.
fn print_debug<T: Debug>(t: &T) {
    println!("{:?}", t);
}

// `T` must implement `HasArea`. Any type which meets
// the bound can access `HasArea`'s function `area`.
fn area<T: HasArea>(t: &T) -> f64 { t.area() }

fn main() {
    let rectangle = Rectangle { length: 3.0, height: 4.0 };
    let _triangle = Triangle  { length: 3.0, height: 4.0 };

    print_debug(&rectangle);
    println!("Area: {}", area(&rectangle));

    //print_debug(&_triangle);
    //println!("Area: {}", area(&_triangle));
    // ^ TODO: Try uncommenting these.
    // | Error: Does not implement either `Debug` or `HasArea`.
}
```

```rust
// Define a function `printer` that takes a generic type `T` which
// must implement trait `Display`.
fn printer<T: Display>(t: T) {
    println!("{}", t);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Without bounds you can only do what all types allow (move, drop).
- where clauses help readability for complex bounds.

_RBE source: `generics/bounds.md`_

---

#### 15.1.4.1 Testcase: empty bounds

**What it is**

A consequence of how bounds work is that even if a `trait` doesn't
include any functionality, you can still use it as a bound. `Eq` and
`Copy` are examples of such `trait`s from the `std` library. See also:
[`std::cmp::Eq`][eq], [`std::marker::Copy`][copy], and [`trait`s][traits]
[eq]: https://doc.rust-lang.org/std/cmp/trait.Eq.html
[copy]: https://doc.rust-lang.org/std/marker/trait.Copy.html
[traits]: ../../trait.md.

**JS mental model**

Even an empty trait can be used as a bound to mark capabilities — a type-level tag.

**Rust example**

```rust
struct Cardinal;
struct BlueJay;
struct Turkey;

trait Red {}
trait Blue {}

impl Red for Cardinal {}
impl Blue for BlueJay {}

// These functions are only valid for types which implement these
// traits. The fact that the traits are empty is irrelevant.
fn red<T: Red>(_: &T)   -> &'static str { "red" }
fn blue<T: Blue>(_: &T) -> &'static str { "blue" }

fn main() {
    let cardinal = Cardinal;
    let blue_jay = BlueJay;
    let _turkey   = Turkey;

    // `red()` won't work on a blue jay nor vice versa
    // because of the bounds.
    println!("A cardinal is {}", red(&cardinal));
    println!("A blue jay is {}", blue(&blue_jay));
    //println!("A turkey is {}", red(&_turkey));
    // ^ TODO: Try uncommenting this line.
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Useful for grouping types intentionally.

_RBE source: `generics/bounds/testcase_empty.md`_

---

### 15.1.5 Multiple bounds

**What it is**

Multiple bounds for a single type can be applied with a `+`. Like normal, different types are
separated with `,`. See also:
[`std::fmt`][fmt] and [`trait`s][traits]
[fmt]: ../hello/print.md
[traits]: ../trait.md.

**JS mental model**

T: A + B means both traits — like intersection constraints.

**Rust example**

```rust
use std::fmt::{Debug, Display};

fn compare_prints<T: Debug + Display>(t: &T) {
    println!("Debug: `{:?}`", t);
    println!("Display: `{}`", t);
}

fn compare_types<T: Debug, U: Debug>(t: &T, u: &U) {
    println!("t: `{:?}`", t);
    println!("u: `{:?}`", u);
}

fn main() {
    let string = "words";
    let array = [1, 2, 3];
    let vec = vec![1, 2, 3];

    compare_prints(&string);
    //compare_prints(&array);
    // TODO ^ Try uncommenting this.

    compare_types(&array, &vec);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Order of bounds does not matter.

_RBE source: `generics/multi_bounds.md`_

---

### 15.1.6 Where clauses

**What it is**

A bound can also be expressed using a `where` clause immediately
before the opening `{`, rather than at the type's first mention. Additionally, `where` clauses can apply bounds to arbitrary types,
rather than just to type parameters. Some cases that a `where` clause is useful:
* When specifying generic types and bounds separately is clearer:
* When using a `where` clause is more expressive than using normal syntax. The `impl` in this example cannot be directly expressed without a `where` clause:
See also:
[RFC][where], [`struct`][struct], and [`trait`][trait]
[struct]: ../custom_types/structs.md
[trait]: ../trait.md
[where]: https://github.com/rust-lang/rfcs/blob/master/text/0135-where.md.

**JS mental model**

where moves complex bounds out of <...> for readability.

**Rust example**

```rust
use std::fmt::Debug;

trait PrintInOption {
    fn print_in_option(self);
}

// Because we would otherwise have to express this as `T: Debug` or
// use another method of indirect approach, this requires a `where` clause:
impl<T> PrintInOption for T where
    Option<T>: Debug {
    // We want `Option<T>: Debug` as our bound because that is what's
    // being printed. Doing otherwise would be using the wrong bound.
    fn print_in_option(self) {
        println!("{:?}", Some(self));
    }
}

fn main() {
    let vec = vec![1, 2, 3];

    vec.print_in_option();
}
```

```rust
impl <A: TraitB + TraitC, D: TraitE + TraitF> MyTrait<A, D> for YourType {}

// Expressing bounds with a `where` clause
impl <A, D> MyTrait<A, D> for YourType where
    A: TraitB + TraitC,
    D: TraitE + TraitF {}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Same meaning as inline bounds.

_RBE source: `generics/where.md`_

---

### 15.1.7 New Type Idiom

**What it is**

The `newtype` idiom gives compile time guarantees that the right type of value is supplied
to a program. For example, a function that measures distance in miles, *must* be given
a value of type `Miles`. Uncomment the last print statement to observe that the type supplied must be `Miles`. To obtain the `newtype`'s value as the base type, you may use the tuple or destructuring syntax like so:
See also:
[`structs`][struct]
[struct]: ../custom_types/structs.md.

**JS mental model**

Wrap a type in a tuple struct struct Inches(i32) for type safety — like branded types in TS.

**Rust example**

```rust
struct Miles(f64);

struct Kilometers(f64);

impl Miles {
    pub fn to_kilometers(&self) -> Kilometers {
        Kilometers(self.0 * 1.609344)
    }
}

impl Kilometers {
    pub fn to_miles(&self) -> Miles {
        Miles(self.0 / 1.609344)
    }
}

fn is_a_marathon(distance: &Miles) -> bool {
    distance.0 >= 26.2
}

fn main() {
    let distance = Miles(30.0);
    let distance_km = distance.to_kilometers();
    println!("Is a marathon? {}", is_a_marathon(&distance));
    println!("Is a marathon? {}", is_a_marathon(&distance_km.to_miles()));
    // println!("Is a marathon? {}", is_a_marathon(&distance_km));
}
```

```rust
struct Miles(f64);

fn main() {
    let distance = Miles(42.0);
    let distance_as_primitive_1: f64 = distance.0; // Tuple
    let Miles(distance_as_primitive_2) = distance; // Destructuring
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Newtypes are free at runtime (one field).
- Does not automatically inherit methods — deref or write wrappers.

_RBE source: `generics/new_types.md`_

---

### 15.1.8 Associated items

**What it is**

"Associated Items" refers to a set of rules pertaining to [`item`][items]s
of various types. It is an extension to `trait` generics, and allows
`trait`s to internally define new items. One such item is called an *associated type*, providing simpler usage
patterns when the `trait` is generic over its container type. See also:
[RFC][RFC]
[items]: https://doc.rust-lang.org/reference/items.html
[RFC]: https://github.com/rust-lang/rfcs/blob/master/text/0195-associated-items.md.

**JS mental model**

Associated items are constants/types/functions tied to a trait — like static members of an interface.

**Rust example**

```rust
trait Container {
    type Item;
    fn get(&self) -> Option<&Self::Item>;
}

fn main() {}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Associated types name an output type chosen by the implementor.

_RBE source: `generics/assoc_items.md`_

---

#### 15.1.8.1 The Problem

**What it is**

A `trait` that is generic over its container type has type specification
requirements - users of the `trait` *must* specify all of its generic types. In the example below, the `Contains` `trait` allows the use of the generic
types `A` and `B`. The trait is then implemented for the `Container` type,
specifying `i32` for `A` and `B` so that it can be used with `fn difference()`. Because `Contains` is generic, we are forced to explicitly state *all* of the
generic types for `fn difference()`.

**JS mental model**

Without associated types, container traits need awkward type parameters on the trait itself.

**Rust example**

```rust
struct Container(i32, i32);

// A trait which checks if 2 items are stored inside of container.
// Also retrieves first or last value.
trait Contains<A, B> {
    fn contains(&self, _: &A, _: &B) -> bool; // Explicitly requires `A` and `B`.
    fn first(&self) -> i32; // Doesn't explicitly require `A` or `B`.
    fn last(&self) -> i32;  // Doesn't explicitly require `A` or `B`.
}

impl Contains<i32, i32> for Container {
    // True if the numbers stored are equal.
    fn contains(&self, number_1: &i32, number_2: &i32) -> bool {
        (&self.0 == number_1) && (&self.1 == number_2)
    }

    // Grab the first number.
    fn first(&self) -> i32 { self.0 }

    // Grab the last number.
    fn last(&self) -> i32 { self.1 }
}

// `C` contains `A` and `B`. In light of that, having to express `A` and
// `B` again is a nuisance.
fn difference<A, B, C>(container: &C) -> i32 where
    C: Contains<A, B> {
    container.last() - container.first()
}

fn main() {
    let number_1 = 3;
    let number_2 = 10;

    let container = Container(number_1, number_2);

    println!("Does container contain {} and {}: {}",
        &number_1, &number_2,
        container.contains(&number_1, &number_2));
    println!("First number: {}", container.first());
    println!("Last number: {}", container.last());

    println!("The difference is: {}", difference(&container));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- This motivates associated types in the next page.

_RBE source: `generics/assoc_items/the_problem.md`_

---

#### 15.1.8.2 Associated types

**What it is**

The use of "Associated types" improves the overall readability of code
by moving inner types locally into a trait as *output* types. Syntax
for the `trait` definition is as follows:
Note that functions that use the `trait` `Contains` are no longer required
to express `A` or `B` at all:
Let's rewrite the example from the previous section using associated types:.

**JS mental model**

type Item; inside a trait — implementor picks the concrete type. Like interface with a nested type slot.

**Rust example**

```rust
struct Container(i32, i32);

// A trait which checks if 2 items are stored inside of container.
// Also retrieves first or last value.
trait Contains {
    // Define generic types here which methods will be able to utilize.
    type A;
    type B;

    fn contains(&self, _: &Self::A, _: &Self::B) -> bool;
    fn first(&self) -> i32;
    fn last(&self) -> i32;
}

impl Contains for Container {
    // Specify what types `A` and `B` are. If the `input` type
    // is `Container(i32, i32)`, the `output` types are determined
    // as `i32` and `i32`.
    type A = i32;
    type B = i32;

    // `&Self::A` and `&Self::B` are also valid here.
    fn contains(&self, number_1: &i32, number_2: &i32) -> bool {
        (&self.0 == number_1) && (&self.1 == number_2)
    }
    // Grab the first number.
    fn first(&self) -> i32 { self.0 }

    // Grab the last number.
    fn last(&self) -> i32 { self.1 }
}

fn difference<C: Contains>(container: &C) -> i32 {
    container.last() - container.first()
}

fn main() {
    let number_1 = 3;
    let number_2 = 10;

    let container = Container(number_1, number_2);

    println!("Does container contain {} and {}: {}",
        &number_1, &number_2,
        container.contains(&number_1, &number_2));
    println!("First number: {}", container.first());
    println!("Last number: {}", container.last());

    println!("The difference is: {}", difference(&container));
}
```

```rust
// `A` and `B` are defined in the trait via the `type` keyword.
// (Note: `type` in this context is different from `type` when used for
// aliases).
trait Contains {
    type A;
    type B;

    // Updated syntax to refer to these new types generically.
    fn contains(&self, _: &Self::A, _: &Self::B) -> bool;
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Iterator::Item is the classic example.

_RBE source: `generics/assoc_items/types.md`_

---

### 15.1.9 Phantom type parameters

**What it is**

A phantom type parameter is one that doesn't show up at runtime,
but is checked statically (and only) at compile time. Data types can use extra generic type parameters to act as markers
or to perform type checking at compile time. These extra parameters
hold no storage values, and have no runtime behavior. In the following example, we combine [std::marker::PhantomData]
with the phantom type parameter concept to create tuples containing
different data types.

**JS mental model**

PhantomData<T> marks that a type uses T without storing T — advanced type-level trick.

**Rust example**

```rust
use std::marker::PhantomData;

// A phantom tuple struct which is generic over `A` with hidden parameter `B`.
#[derive(PartialEq)] // Allow equality test for this type.
struct PhantomTuple<A, B>(A, PhantomData<B>);

// A phantom type struct which is generic over `A` with hidden parameter `B`.
#[derive(PartialEq)] // Allow equality test for this type.
struct PhantomStruct<A, B> { first: A, phantom: PhantomData<B> }

// Note: Storage is allocated for generic type `A`, but not for `B`.
//       Therefore, `B` cannot be used in computations.

fn main() {
    // Here, `f32` and `f64` are the hidden parameters.
    // PhantomTuple type specified as `<char, f32>`.
    let _tuple1: PhantomTuple<char, f32> = PhantomTuple('Q', PhantomData);
    // PhantomTuple type specified as `<char, f64>`.
    let _tuple2: PhantomTuple<char, f64> = PhantomTuple('Q', PhantomData);

    // Type specified as `<char, f32>`.
    let _struct1: PhantomStruct<char, f32> = PhantomStruct {
        first: 'Q',
        phantom: PhantomData,
    };
    // Type specified as `<char, f64>`.
    let _struct2: PhantomStruct<char, f64> = PhantomStruct {
        first: 'Q',
        phantom: PhantomData,
    };

    // Compile-time Error! Type mismatch so these cannot be compared:
    // println!("_tuple1 == _tuple2 yields: {}",
    //           _tuple1 == _tuple2);

    // Compile-time Error! Type mismatch so these cannot be compared:
    // println!("_struct1 == _struct2 yields: {}",
    //           _struct1 == _struct2);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Needed for drop check / variance in some APIs.
- Rare in everyday app code.

_RBE source: `generics/phantom.md`_

---

#### 15.1.9.1 Testcase: unit clarification

**What it is**

A useful method of unit conversions can be examined by implementing `Add`
with a phantom type parameter. The `Add` `trait` is examined below:
The whole implementation:
See also:
[Borrowing (`&`)], [Bounds (`X: Y`)], [enum], [impl & self],
[Overloading], [ref], [Traits (`X for Y`)], and [TupleStructs]. [Borrowing (`&`)]: ../../scope/borrow.md
[Bounds (`X: Y`)]: ../../generics/bounds.md
[enum]: ../../custom_types/enum.md
[impl & self]: ../../fn/methods.md
[Overloading]: ../../trait/ops.md
[ref]: ../../scope/borrow/ref.md
[Traits (`X for Y`)]: ../../trait.md
[TupleStructs]: ../../custom_types/structs.md
[std::marker::PhantomData]: https://doc.rust-lang.org/std/marker/struct.PhantomData.html.

**JS mental model**

Phantom types can encode units (mm vs inch) at compile time — like branded number types that cannot mix.

**Rust example**

```rust
use std::ops::Add;
use std::marker::PhantomData;

/// Create void enumerations to define unit types.
#[derive(Debug, Clone, Copy)]
enum Inch {}
#[derive(Debug, Clone, Copy)]
enum Mm {}

/// `Length` is a type with phantom type parameter `Unit`,
/// and is not generic over the length type (that is `f64`).
///
/// `f64` already implements the `Clone` and `Copy` traits.
#[derive(Debug, Clone, Copy)]
struct Length<Unit>(f64, PhantomData<Unit>);

/// The `Add` trait defines the behavior of the `+` operator.
impl<Unit> Add for Length<Unit> {
    type Output = Length<Unit>;

    // add() returns a new `Length` struct containing the sum.
    fn add(self, rhs: Length<Unit>) -> Length<Unit> {
        // `+` calls the `Add` implementation for `f64`.
        Length(self.0 + rhs.0, PhantomData)
    }
}

fn main() {
    // Specifies `one_foot` to have phantom type parameter `Inch`.
    let one_foot:  Length<Inch> = Length(12.0, PhantomData);
    // `one_meter` has phantom type parameter `Mm`.
    let one_meter: Length<Mm>   = Length(1000.0, PhantomData);

    // `+` calls the `add()` method we implemented for `Length<Unit>`.
    //
    // Since `Length` implements `Copy`, `add()` does not consume
    // `one_foot` and `one_meter` but copies them into `self` and `rhs`.
    let two_feet = one_foot + one_foot;
    let two_meters = one_meter + one_meter;

    // Addition works.
    println!("one foot + one_foot = {:?} in", two_feet.0);
    println!("one meter + one_meter = {:?} mm", two_meters.0);

    // Nonsensical operations fail as they should:
    // Compile-time Error: type mismatch.
    //let one_feter = one_foot + one_meter;
}
```

```rust
// This construction would impose: `Self + RHS = Output`
// where RHS defaults to Self if not specified in the implementation.
pub trait Add<RHS = Self> {
    type Output;

    fn add(self, rhs: RHS) -> Self::Output;
}

// `Output` must be `T<U>` so that `T<U> + T<U> = T<U>`.
impl<U> Add for T<U> {
    type Output = T<U>;
    ...
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Prevents mixing incompatible units without runtime cost.

_RBE source: `generics/phantom/testcase_units.md`_

---
