# Functions, methods, closures, HOFs

_RBE chapter root maps here. Every nested leaf below has the full 5-part teaching block._

### 11.1 Functions

**What it is**

Functions are declared using the `fn` keyword. Its arguments are type
annotated, just like variables, and, if the function returns a value, the
return type must be specified after an arrow `->`. The final expression in the function will be used as return value. Alternatively, the `return` statement can be used to return a value earlier
from within the function, even from inside loops or `if` statements.

**JS mental model**

Functions are like JS functions but with typed params/returns. No default params — use `Option` or builders.

**Rust example**

```rust
// Unlike C/C++, there's no restriction on the order of function definitions
fn main() {
    // We can use this function here, and define it somewhere later
    fizzbuzz_to(100);
}

// Function that returns a boolean value
fn is_divisible_by(lhs: u32, rhs: u32) -> bool {
    // Corner case, early return
    if rhs == 0 {
        return false;
    }

    // This is an expression, the `return` keyword is not necessary here
    lhs % rhs == 0
}

// Functions that "don't" return a value, actually return the unit type `()`
fn fizzbuzz(n: u32) -> () {
    if is_divisible_by(n, 15) {
        println!("fizzbuzz");
    } else if is_divisible_by(n, 3) {
        println!("fizz");
    } else if is_divisible_by(n, 5) {
        println!("buzz");
    } else {
        println!("{}", n);
    }
}

// When a function returns `()`, the return type can be omitted from the
// signature
fn fizzbuzz_to(n: u32) {
    for n in 1..=n {
        fizzbuzz(n);
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Return type after `->`.
- Last expression without `;` is the return value.

_RBE source: `fn.md`_

---

### 11.1.1 Methods

**What it is**

Some functions are connected to a particular type. These come in two forms:
associated functions, and methods. Associated functions are functions that
are defined on a type generally, while methods are associated functions that are
called on a particular instance of a type.

**JS mental model**

`impl Type { fn method(&self) }` ≈ class methods. Associated fns like `Type::new` ≈ static methods.

**Rust example**

```rust
struct Point {
    x: f64,
    y: f64,
}

// Implementation block, all `Point` associated functions & methods go in here
impl Point {
    // This is an "associated function" because this function is associated with
    // a particular type, that is, Point.
    //
    // Associated functions don't need to be called with an instance.
    // These functions are generally used like constructors.
    fn origin() -> Point {
        Point { x: 0.0, y: 0.0 }
    }

    // Another associated function, taking two arguments:
    fn new(x: f64, y: f64) -> Point {
        Point { x: x, y: y }
    }
}

struct Rectangle {
    p1: Point,
    p2: Point,
}

impl Rectangle {
    // This is a method
    // `&self` is sugar for `self: &Self`, where `Self` is the type of the
    // caller object. In this case `Self` = `Rectangle`
    fn area(&self) -> f64 {
        // `self` gives access to the struct fields via the dot operator
        let Point { x: x1, y: y1 } = self.p1;
        let Point { x: x2, y: y2 } = self.p2;

        // `abs` is a `f64` method that returns the absolute value of the
        // caller
        ((x1 - x2) * (y1 - y2)).abs()
    }

    fn perimeter(&self) -> f64 {
        let Point { x: x1, y: y1 } = self.p1;
        let Point { x: x2, y: y2 } = self.p2;

        2.0 * ((x1 - x2).abs() + (y1 - y2).abs())
    }

    // This method requires the caller object to be mutable
    // `&mut self` desugars to `self: &mut Self`
    fn translate(&mut self, x: f64, y: f64) {
        self.p1.x += x;
        self.p2.x += x;

        self.p1.y += y;
        self.p2.y += y;
    }
}

// `Pair` owns resources: two heap allocated integers
struct Pair(Box<i32>, Box<i32>);

impl Pair {
    // This method "consumes" the resources of the caller object
    // `self` desugars to `self: Self`
    fn destroy(self) {
        // Destructure `self`
        let Pair(first, second) = self;

        println!("Destroying Pair({}, {})", first, second);

        // `first` and `second` go out of scope and get freed
    }
}

fn main() {
    let rectangle = Rectangle {
        // Associated functions are called using double colons
        p1: Point::origin(),
        p2: Point::new(3.0, 4.0),
    };

    // Methods are called using the dot operator
    // Note that the first argument `&self` is implicitly passed, i.e.
    // `rectangle.perimeter()` === `Rectangle::perimeter(&rectangle)`
    println!("Rectangle perimeter: {}", rectangle.perimeter());
    println!("Rectangle area: {}", rectangle.area());

    let mut square = Rectangle {
        p1: Point::origin(),
        p2: Point::new(1.0, 1.0),
    };

    // Error! `rectangle` is immutable, but this method requires a mutable
    // object
    //rectangle.translate(1.0, 0.0);
    // TODO ^ Try uncommenting this line

    // Okay! Mutable objects can call mutable methods
    square.translate(1.0, 1.0);

    let pair = Pair(Box::new(1), Box::new(2));

    pair.destroy();

    // Error! Previous `destroy` call "consumed" `pair`
    //pair.destroy();
    // TODO ^ Try uncommenting this line
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- `&self` / `&mut self` / `self` choose borrow vs move.
- No implicit `this`.

_RBE source: `fn/methods.md`_

---

### 11.1.2 Closures

**What it is**

Closures are functions that can capture the enclosing environment. For
example, a closure that captures the `x` variable:
The syntax and capabilities of closures make them very convenient for
on the fly usage. Calling a closure is exactly like calling a function. However, both input and return types *can* be inferred and input
variable names *must* be specified.

**JS mental model**

Arrow functions: `|x| x + 1`. Can capture environment.

**Rust example**

```rust
fn main() {
    let outer_var = 42;

    // A regular function can't refer to variables in the enclosing environment
    //fn function(i: i32) -> i32 { i + outer_var }
    // TODO: uncomment the line above and see the compiler error. The compiler
    // suggests that we define a closure instead.

    // Closures are anonymous, here we are binding them to references.
    // Annotation is identical to function annotation but is optional
    // as are the `{}` wrapping the body. These nameless functions
    // are assigned to appropriately named variables.
    let closure_annotated = |i: i32| -> i32 { i + outer_var };
    let closure_inferred  = |i     |          i + outer_var  ;

    // Call the closures.
    println!("closure_annotated: {}", closure_annotated(1));
    println!("closure_inferred: {}", closure_inferred(1));
    // Once closure's type has been inferred, it cannot be inferred again with another type.
    //println!("cannot reuse closure_inferred with another type: {}", closure_inferred(42i64));
    // TODO: uncomment the line above and see the compiler error.

    // A closure taking no arguments which returns an `i32`.
    // The return type is inferred.
    let one = || 1;
    println!("closure returning one: {}", one());

}
```

**JS equivalent**

```javascript
const add = (a, b) => a + b;
```

**Watch out**

- Closures have unique anonymous types.
- Often passed as `impl Fn…` or boxed `Box<dyn Fn…>`.

_RBE source: `fn/closures.md`_

---

#### 11.1.2.1 Capturing

**What it is**

Closures are inherently flexible and will do what the functionality requires
to make the closure work without annotation. This allows capturing to
flexibly adapt to the use case, sometimes moving and sometimes borrowing. Closures can capture variables:
* by reference: `&T`
* by mutable reference: `&mut T`
* by value: `T`
They preferentially capture variables by reference and only go lower when
required. Using `move` before vertical pipes forces closure
to take ownership of captured variables:
See also:
[`Box`][box] and [`std::mem::drop`][drop]
[box]: ../../std/box.md
[drop]: https://doc.rust-lang.org/std/mem/fn.drop.html.

**JS mental model**

Captures by reference by default; `move` forces ownership into the closure (like binding vars before async).

**Rust example**

```rust
fn main() {
    use std::mem;

    let color = String::from("green");

    // A closure to print `color` which immediately borrows (`&`) `color` and
    // stores the borrow and closure in the `print` variable. It will remain
    // borrowed until `print` is used the last time.
    //
    // `println!` only requires arguments by immutable reference so it doesn't
    // impose anything more restrictive.
    let print = || println!("`color`: {}", color);

    // Call the closure using the borrow.
    print();

    // `color` can be borrowed immutably again, because the closure only holds
    // an immutable reference to `color`.
    let _reborrow = &color;
    print();

    // A move or reborrow is allowed after the final use of `print`
    let _color_moved = color;


    let mut count = 0;
    // A closure to increment `count` could take either `&mut count` or `count`
    // but `&mut count` is less restrictive so it takes that. Immediately
    // borrows `count`.
    //
    // A `mut` is required on `inc` because a `&mut` is stored inside. Thus,
    // calling the closure mutates `count` which requires a `mut`.
    let mut inc = || {
        count += 1;
        println!("`count`: {}", count);
    };

    // Call the closure using a mutable borrow.
    inc();

    // The closure still mutably borrows `count` because it is called later.
    // An attempt to reborrow will lead to an error.
    // let _reborrow = &count;
    // ^ TODO: try uncommenting this line.
    inc();

    // The closure no longer needs to borrow `&mut count`. Therefore, it is
    // possible to reborrow without an error
    let _count_reborrowed = &mut count;


    // A non-copy type.
    let movable = Box::new(3);

    // `mem::drop` requires `T` so this must take by value. A copy type
    // would copy into the closure leaving the original untouched.
    // A non-copy must move and so `movable` immediately moves into
    // the closure.
    let consume = || {
        println!("`movable`: {:?}", movable);
        mem::drop(movable);
    };

    // `consume` consumes the variable so this can only be called once.
    consume();
    // consume();
    // ^ TODO: Try uncommenting this line.
}
```

```rust
fn main() {
    // `Vec` has non-copy semantics.
    let haystack = vec![1, 2, 3];

    let contains = move |needle| haystack.contains(needle);

    println!("{}", contains(&1));
    println!("{}", contains(&4));

    // println!("There're {} elements in vec", haystack.len());
    // ^ Uncommenting above line will result in compile-time error
    // because borrow checker doesn't allow re-using variable after it
    // has been moved.

    // Removing `move` from closure's signature will cause closure
    // to borrow _haystack_ variable immutably, hence _haystack_ is still
    // available and uncommenting above line will not cause an error.
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- `move` is common when spawning threads.
- Capture mode inferred from usage.

_RBE source: `fn/closures/capture.md`_

---

#### 11.1.2.2 As input parameters

**What it is**

While Rust chooses how to capture variables on the fly mostly without type
annotation, this ambiguity is not allowed when writing functions. When
taking a closure as an input parameter, the closure's complete type must be
annotated using one of a few `traits`, and they're determined by what the
closure does with captured value. In order of decreasing restriction,
they are:
* `Fn`: the closure uses the captured value by reference (`&T`)
* `FnMut`: the closure uses the captured value by mutable reference (`&mut T`)
* `FnOnce`: the closure uses the captured value by value (`T`)
On a variable-by-variable basis, the compiler will capture variables in the
least restrictive manner possible. For instance, consider a parameter annotated as `FnOnce`.

**JS mental model**

`Fn` / `FnMut` / `FnOnce` — how the closure may use captures. Like knowing if a callback is sync-pure vs mutating.

**Rust example**

```rust
// A function which takes a closure as an argument and calls it.
// <F> denotes that F is a "Generic type parameter"
fn apply<F>(f: F) where
    // The closure takes no input and returns nothing.
    F: FnOnce() {
    // ^ TODO: Try changing this to `Fn` or `FnMut`.

    f();
}

// A function which takes a closure and returns an `i32`.
fn apply_to_3<F>(f: F) -> i32 where
    // The closure takes an `i32` and returns an `i32`.
    F: Fn(i32) -> i32 {

    f(3)
}

fn main() {
    use std::mem;

    let greeting = "hello";
    // A non-copy type.
    // `to_owned` creates owned data from borrowed one
    let mut farewell = "goodbye".to_owned();

    // Capture 2 variables: `greeting` by reference and
    // `farewell` by value.
    let diary = || {
        // `greeting` is by reference: requires `Fn`.
        println!("I said {}.", greeting);

        // Mutation forces `farewell` to be captured by
        // mutable reference. Now requires `FnMut`.
        farewell.push_str("!!!");
        println!("Then I screamed {}.", farewell);
        println!("Now I can sleep. zzzzz");

        // Manually calling drop forces `farewell` to
        // be captured by value. Now requires `FnOnce`.
        mem::drop(farewell);
    };

    // Call the function which applies the closure.
    apply(diary);

    // `double` satisfies `apply_to_3`'s trait bound
    let double = |x| 2 * x;

    println!("3 doubled: {}", apply_to_3(double));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- `FnOnce` can run once (consumes captures).
- Prefer the least restrictive bound you need? Actually take the most general that still works — usually `FnOnce` as a parameter bound is most accepting.

_RBE source: `fn/closures/input_parameters.md`_

---

#### 11.1.2.3 Type anonymity

**What it is**

Closures succinctly capture variables from enclosing scopes. Does this have
any consequences? Observe how using a closure as a function
parameter requires [generics], which is necessary because of how they are
defined:
When a closure is defined, the compiler implicitly creates a new
anonymous structure to store the captured variables inside, meanwhile
implementing the functionality via one of the `traits`: `Fn`, `FnMut`, or
`FnOnce` for this unknown type. This type is assigned to the variable which
is stored until calling.

**JS mental model**

Each closure has a distinct unnameable type — you talk about them via traits (`impl Fn`).

**Rust example**

```rust
// `F` must implement `Fn` for a closure which takes no
// inputs and returns nothing - exactly what is required
// for `print`.
fn apply<F>(f: F) where
    F: Fn() {
    f();
}

fn main() {
    let x = 7;

    // Capture `x` into an anonymous type and implement
    // `Fn` for it. Store it in `print`.
    let print = || println!("{}", x);

    apply(print);
}
```

```rust
// `F` must be generic.
fn apply<F>(f: F) where
    F: FnOnce() {
    f();
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- You cannot write the concrete closure type name — use `impl Fn` or generics.

_RBE source: `fn/closures/anonymity.md`_

---

#### 11.1.2.4 Input functions

**What it is**

Since closures may be used as arguments, you might wonder if the same can be said
about functions. If you declare a function that takes a
closure as parameter, then any function that satisfies the trait bound of that
closure can be passed as a parameter. As an additional note, the `Fn`, `FnMut`, and `FnOnce` `traits` dictate how
a closure captures variables from the enclosing scope. See also:
[`Fn`][fn], [`FnMut`][fn_mut], and [`FnOnce`][fn_once]
[fn]: https://doc.rust-lang.org/std/ops/trait.Fn.html
[fn_mut]: https://doc.rust-lang.org/std/ops/trait.FnMut.html
[fn_once]: https://doc.rust-lang.org/std/ops/trait.FnOnce.html.

**JS mental model**

Function pointers `fn(i32)->i32` are like passing a bare function; closures that do not capture coerce to `fn`.

**Rust example**

```rust
// Define a function which takes a generic `F` argument
// bounded by `Fn`, and calls it
fn call_me<F: Fn()>(f: F) {
    f();
}

// Define a wrapper function satisfying the `Fn` bound
fn function() {
    println!("I'm a function!");
}

fn main() {
    // Define a closure satisfying the `Fn` bound
    let closure = || println!("I'm a closure!");

    call_me(closure);
    call_me(function);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Capturing closures are not `fn` pointers.

_RBE source: `fn/closures/input_functions.md`_

---

#### 11.1.2.5 As output parameters

**What it is**

Closures as input parameters are possible, so returning closures as
output parameters should also be possible. However, anonymous
closure types are, by definition, unknown, so we have to use
`impl Trait` to return them. The valid traits for returning a closure are:
* `Fn`
* `FnMut`
* `FnOnce`
Beyond this, the `move` keyword must be used, which signals that all captures
occur by value. This is required because any captures by reference would be
dropped as soon as the function exited, leaving invalid references in the
closure.

**JS mental model**

Returning closures requires `impl Fn` or `Box<dyn Fn>` because types are anonymous.

**Rust example**

```rust
fn create_fn() -> impl Fn() {
    let text = "Fn".to_owned();

    move || println!("This is a: {}", text)
}

fn create_fnmut() -> impl FnMut() {
    let text = "FnMut".to_owned();

    move || println!("This is a: {}", text)
}

fn create_fnonce() -> impl FnOnce() {
    let text = "FnOnce".to_owned();

    move || println!("This is a: {}", text)
}

fn main() {
    let fn_plain = create_fn();
    let mut fn_mut = create_fnmut();
    let fn_once = create_fnonce();

    fn_plain();
    fn_mut();
    fn_once();
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- `move` often needed so the closure owns what it returns with.

_RBE source: `fn/closures/output_parameters.md`_

---

#### 11.1.2.6 Examples in std

**What it is**

This section contains a few examples of using closures from the `std` library.

**JS mental model**

Std iterators take closures everywhere — like array methods `some`, `find`, `map`.

**Rust example**

```rust
fn main() {
    let v = vec![1, 2, 3];
    assert!(v.iter().any(|&x| x == 2));
    assert_eq!(v.iter().find(|&&x| x > 1), Some(&2));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Iterator adapters are lazy until consumed.

_RBE source: `fn/closures/closure_examples.md`_

---

##### 11.1.2.6.1 Iterator::any

**What it is**

`Iterator::any` is a function which when passed an iterator, will return
`true` if any element satisfies the predicate. Its
signature:
See also:
[`std::iter::Iterator::any`][any]
[any]: https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.any.

**JS mental model**

`.any(|x| …)` ≡ `array.some(…)`

**Rust example**

```rust
fn main() {
    let vec1 = vec![1, 2, 3];
    let vec2 = vec![4, 5, 6];

    // `iter()` for vecs yields `&i32`. Destructure to `i32`.
    println!("2 in vec1: {}", vec1.iter()     .any(|&x| x == 2));
    // `into_iter()` for vecs yields `i32`. No destructuring required.
    println!("2 in vec2: {}", vec2.into_iter().any(|x| x == 2));

    // `iter()` only borrows `vec1` and its elements, so they can be used again
    println!("vec1 len: {}", vec1.len());
    println!("First element of vec1 is: {}", vec1[0]);
    // `into_iter()` does move `vec2` and its elements, so they cannot be used again
    // println!("First element of vec2 is: {}", vec2[0]);
    // println!("vec2 len: {}", vec2.len());
    // TODO: uncomment two lines above and see compiler errors.

    let array1 = [1, 2, 3];
    let array2 = [4, 5, 6];

    // `iter()` for arrays yields `&i32`.
    println!("2 in array1: {}", array1.iter()     .any(|&x| x == 2));
    // `into_iter()` for arrays yields `i32`.
    println!("2 in array2: {}", array2.into_iter().any(|x| x == 2));
}
```

```rust
pub trait Iterator {
    // The type being iterated over.
    type Item;

    // `any` takes `&mut self` meaning the caller may be borrowed
    // and modified, but not consumed.
    fn any<F>(&mut self, f: F) -> bool where
        // `FnMut` meaning any captured variable may at most be
        // modified, not consumed. `Self::Item` is the closure parameter type,
        // which is determined by the iterator (e.g., `&T` for `.iter()`,
        // `T` for `.into_iter()`).
        F: FnMut(Self::Item) -> bool;
}
```

**JS equivalent**

```javascript
[2,3,4].some(x => x > 5); // false
```

**Watch out**

- Stops early when true found.

_RBE source: `fn/closures/closure_examples/iter_any.md`_

---

##### 11.1.2.6.2 Searching through iterators

**What it is**

`Iterator::find` is a function which iterates over an iterator and searches for the
first value which satisfies some condition. If none of the values satisfy the
condition, it returns `None`. Its signature:
`Iterator::find` gives you a reference to the item. But if you want the _index_ of the
item, use `Iterator::position`.

**JS mental model**

`.find(|x| …)` ≡ `array.find(…)` returning `Option`

**Rust example**

```rust
fn main() {
    let vec1 = vec![1, 2, 3];
    let vec2 = vec![4, 5, 6];

    // `vec1.iter()` yields `&i32`.
    let mut iter = vec1.iter();
    // `vec2.into_iter()` yields `i32`.
    let mut into_iter = vec2.into_iter();

    // `iter()` yields `&i32`, and `find` passes `&Item` to the predicate.
    // Since `Item = &i32`, the closure argument has type `&&i32`,
    // which we pattern-match to dereference down to `i32`.
    println!("Find 2 in vec1: {:?}", iter.find(|&&x| x == 2));
    
    // `into_iter()` yields `i32`, and `find` passes `&Item` to the predicate.
    // Since `Item = i32`, the closure argument has type `&i32`,
    // which we pattern-match to dereference down to `i32`.
    println!("Find 2 in vec2: {:?}", into_iter.find(|&x| x == 2));

    let array1 = [1, 2, 3];
    let array2 = [4, 5, 6];

    // `array1.iter()` yields `&i32`, and `find` passes `&Item` to the
    // predicate. Since `Item = &i32`, the closure argument has type `&&i32`.
    println!("Find 2 in array1: {:?}", array1.iter().find(|&&x| x == 2));
    // `array2.into_iter()` yields `i32` (since Rust 2021 edition), and
    // `find` passes `&Item` to the predicate. Since `Item = i32`, the
    // closure argument has type `&i32`.
    println!("Find 2 in array2: {:?}", array2.into_iter().find(|&x| x == 2));
}
```

```rust
fn main() {
    let vec = vec![1, 9, 3, 3, 13, 2];

    // `position` passes the iterator’s `Item` by value to the predicate.
    // `vec.iter()` yields `&i32`, so the predicate receives `&i32`,
    // which we pattern-match to dereference to `i32`.
    let index_of_first_even_number = vec.iter().position(|&x| x % 2 == 0);
    assert_eq!(index_of_first_even_number, Some(5));

    // `vec.into_iter()` yields `i32`, so the predicate receives `i32` directly.
    let index_of_first_negative_number = vec.into_iter().position(|x| x < 0);
    assert_eq!(index_of_first_negative_number, None);
}
```

**JS equivalent**

```javascript
[2,3,4].find(x => x > 2); // 3
```

**Watch out**

- Returns `Option<&T>` for `.iter()`.

_RBE source: `fn/closures/closure_examples/iter_find.md`_

---

### 11.1.3 Higher Order Functions

**What it is**

Rust provides Higher Order Functions (HOF). These are functions that
take one or more functions and/or produce a more useful function. HOFs
and lazy iterators give Rust its functional flavor. [Option][option]
and
[Iterator][iter]
implement their fair share of HOFs.

**JS mental model**

Higher-order functions: functions that take/return functions — same idea as JS HOFs.

**Rust example**

```rust
fn is_odd(n: u32) -> bool {
    n % 2 == 1
}

fn main() {
    println!("Find the sum of all the numbers with odd squares under 1000");
    let upper = 1000;

    // Imperative approach
    // Declare accumulator variable
    let mut acc = 0;
    // Iterate: 0, 1, 2, ... to infinity
    for n in 0.. {
        // Square the number
        let n_squared = n * n;

        if n_squared >= upper {
            // Break loop if exceeded the upper limit
            break;
        } else if is_odd(n_squared) {
            // Accumulate value, if it's odd
            acc += n;
        }
    }
    println!("imperative style: {}", acc);

    // Functional approach
    let sum: u32 =
        (0..).take_while(|&n| n * n < upper) // Below upper limit
             .filter(|&n| is_odd(n * n))     // That are odd
             .sum();                         // Sum them
    println!("functional style: {}", sum);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Combine with iterators for functional style without JS GC costs.

_RBE source: `fn/hof.md`_

---

### 11.1.4 Diverging functions

**What it is**

Diverging functions never return. They are marked using `!`, which is an empty type. As opposed to all the other types, this one cannot be instantiated, because the
set of all possible values this type can have is empty. Note that, it is
different from the `()` type, which has exactly one possible value.

**JS mental model**

`-> !` never returns (panic/infinite loop). Like a function that always throws.

**Rust example**

```rust
fn main() {
    fn sum_odd_numbers(up_to: u32) -> u32 {
        let mut acc = 0;
        for i in 0..up_to {
            // Notice that the return type of this match expression must be u32
            // because of the type of the "addition" variable.
            let addition: u32 = match i%2 == 1 {
                // The "i" variable is of type u32, which is perfectly fine.
                true => i,
                // On the other hand, the "continue" expression does not return
                // u32, but it is still fine, because it never returns and therefore
                // does not violate the type requirements of the match expression.
                false => continue,
            };
            acc += addition;
        }
        acc
    }
    println!("Sum of odd numbers up to 9 (excluding): {}", sum_odd_numbers(9));
}
```

```rust
#![feature(never_type)]

fn main() {
    let x: ! = panic!("This call never returns.");
    println!("You will never see this line!");
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- `!` can coerce to any type in type-checking (never-type).

_RBE source: `fn/diverging.md`_

---
