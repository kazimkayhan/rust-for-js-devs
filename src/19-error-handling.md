# Error handling

_RBE chapter root maps here. Every nested leaf below has the full 5-part teaching block._

### 19.1 Error handling

**What it is**

Error handling is the process of handling the possibility of failure. For
example, failing to read a file and then continuing to use that *bad* input
would clearly be problematic. Noticing and explicitly managing those errors
saves the rest of the program from various pitfalls. There are various ways to deal with errors in Rust, which are described in the
following subchapters.

**JS mental model**

Recoverable errors use Result; absence uses Option; crashes use panic!. No exceptions by default.

**Rust example**

```rust
fn fallible() -> Result<i32, &'static str> {
    Ok(1)
}

fn main() -> Result<(), &'static str> {
    let n = fallible()?;
    println!("{n}");
    Ok(())
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Prefer Result over panic for expected failures.
- ? propagates errors upward.

_RBE source: `error.md`_

---

### 19.1.1 panic

**What it is**

The simplest error handling mechanism we will see is `panic`. It prints an
error message, starts unwinding the stack, and usually exits the program. Here, we explicitly call `panic` on our error condition:
The first call to `drink` works. The second panics and thus the third is never called.

**JS mental model**

panic! aborts the current thread stack unwind (or aborts process) — like throwing without catch.

**Rust example**

```rust
fn drink(beverage: &str) {
    // You shouldn't drink too many sugary beverages.
    if beverage == "lemonade" { panic!("AAAaaaaa!!!!"); }

    println!("Some refreshing {} is all I need.", beverage);
}

fn main() {
    drink("water");
    drink("lemonade");
    drink("still water");
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Use for bugs / cannot continue.
- Not for normal validation failures.

_RBE source: `error/panic.md`_

---

### 19.1.2 abort & unwind

**What it is**

The previous section illustrates the error handling mechanism `panic`. Different code paths can be conditionally compiled based on the panic setting. The current values available are `unwind` and `abort`. Building on the prior lemonade example, we explicitly use the panic strategy to exercise different lines of code.

**JS mental model**

Profiles can abort-on-panic (smaller binaries) or unwind — like choosing process crash vs try/finally cleanup.

**Rust example**

```rust
fn drink(beverage: &str) {
    // You shouldn't drink too much sugary beverages.
    if beverage == "lemonade" {
        if cfg!(panic = "abort") {
            println!("This is not your party. Run!!!!");
        } else {
            println!("Spit it out!!!!");
        }
    } else {
        println!("Some refreshing {} is all I need.", beverage);
    }
}

fn main() {
    drink("water");
    drink("lemonade");
}
```

```rust
#[cfg(panic = "unwind")]
fn ah() {
    println!("Spit it out!!!!");
}

#[cfg(not(panic = "unwind"))]
fn ah() {
    println!("This is not your party. Run!!!!");
}

fn drink(beverage: &str) {
    if beverage == "lemonade" {
        ah();
    } else {
        println!("Some refreshing {} is all I need.", beverage);
    }
}

fn main() {
    drink("water");
    drink("lemonade");
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Libraries should be careful what they assume.
- Set in Cargo.toml profile.

_RBE source: `error/abort_unwind.md`_

---

### 19.1.3 Option & unwrap

**What it is**

In the last example, we showed that we can induce program failure at will. We told our program to `panic` if we drink a sugary lemonade. But what if we expect _some_ drink but don't receive one? That case would be just as bad, so it needs to be handled!

**JS mental model**

Option<T> is Some(value) or None — null safety built-in. unwrap means give value or panic.

**Rust example**

```rust
// The adult has seen it all, and can handle any drink well.
// All drinks are handled explicitly using `match`.
fn give_adult(drink: Option<&str>) {
    // Specify a course of action for each case.
    match drink {
        Some("lemonade") => println!("Yuck! Too sugary."),
        Some(inner)   => println!("{}? How nice.", inner),
        None          => println!("No drink? Oh well."),
    }
}

// Others will `panic` before drinking sugary drinks.
// All drinks are handled implicitly using `unwrap`.
fn drink(drink: Option<&str>) {
    // `unwrap` returns a `panic` when it receives a `None`.
    let inside = drink.unwrap();
    if inside == "lemonade" { panic!("AAAaaaaa!!!!"); }

    println!("I love {}s!!!!!", inside);
}

fn main() {
    let water  = Some("water");
    let lemonade = Some("lemonade");
    let void  = None;

    give_adult(water);
    give_adult(lemonade);
    give_adult(void);

    let coffee = Some("coffee");
    let nothing = None;

    drink(coffee);
    drink(nothing);
}
```

**JS equivalent**

```javascript
const x = maybe ?? defaultValue;
const y = maybe?.field;
```

**Watch out**

- Prefer ?, map, or match over unwrap in libraries.
- expect("msg") panics with a message.

_RBE source: `error/option_unwrap.md`_

---

#### 19.1.3.1 Unpacking options with ?

**What it is**

You can unpack `Option`s by using `match` statements, but it's often easier to
use the `?` operator. If `x` is an `Option`, then evaluating `x?` will return
the underlying value if `x` is `Some`, otherwise it will terminate whatever
function is being executed and return `None`. You can chain many `?`s together to make your code much more readable.

**JS mental model**

? on Option returns early with None — like optional chaining with early return.

**Rust example**

```rust
struct Person {
    job: Option<Job>,
}

#[derive(Clone, Copy)]
struct Job {
    phone_number: Option<PhoneNumber>,
}

#[derive(Clone, Copy)]
#[allow(dead_code)]
struct PhoneNumber {
    area_code: Option<u8>,
    number: u32,
}

impl Person {

    // Gets the area code of the phone number of the person's job, if it exists.
    fn work_phone_area_code(&self) -> Option<u8> {
        // This would need many nested `match` statements without the `?` operator.
        // It would take a lot more code - try writing it yourself and see which
        // is easier.
        self.job?.phone_number?.area_code
    }
}

fn main() {
    let p = Person {
        job: Some(Job {
            phone_number: Some(PhoneNumber {
                area_code: Some(61),
                number: 439222222,
            }),
        }),
    };

    assert_eq!(p.work_phone_area_code(), Some(61));
}
```

```rust
fn next_birthday(current_age: Option<u8>) -> Option<String> {
    // If `current_age` is `None`, this returns `None`.
    // If `current_age` is `Some`, the inner `u8` value + 1
    // gets assigned to `next_age`
    let next_age: u8 = current_age? + 1;
    Some(format!("Next year I will be {}", next_age))
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Function must return Option (or compatible).

_RBE source: `error/option_unwrap/question_mark.md`_

---

#### 19.1.3.2 Combinators: map

**What it is**

`match` is a valid method for handling `Option`s. However, you may
eventually find heavy usage tedious, especially with operations only valid
with an input. In these cases, [combinators][combinators] can be used to
manage control flow in a modular fashion. `Option` has a built in method called `map()`, a combinator for the simple
mapping of `Some -> Some` and `None -> None`.

**JS mental model**

opt.map(f) transforms Some — like optional map.

**Rust example**

```rust
#![allow(dead_code)]

#[derive(Debug)] enum Food { Apple, Carrot, Potato }

#[derive(Debug)] struct Peeled(Food);
#[derive(Debug)] struct Chopped(Food);
#[derive(Debug)] struct Cooked(Food);

// Peeling food. If there isn't any, then return `None`.
// Otherwise, return the peeled food.
fn peel(food: Option<Food>) -> Option<Peeled> {
    match food {
        Some(food) => Some(Peeled(food)),
        None       => None,
    }
}

// Chopping food. If there isn't any, then return `None`.
// Otherwise, return the chopped food.
fn chop(peeled: Option<Peeled>) -> Option<Chopped> {
    match peeled {
        Some(Peeled(food)) => Some(Chopped(food)),
        None               => None,
    }
}

// Cooking food. Here, we showcase `map()` instead of `match` for case handling.
fn cook(chopped: Option<Chopped>) -> Option<Cooked> {
    chopped.map(|Chopped(food)| Cooked(food))
}

// A function to peel, chop, and cook food all in sequence.
// We chain multiple uses of `map()` to simplify the code.
fn process(food: Option<Food>) -> Option<Cooked> {
    food.map(|f| Peeled(f))
        .map(|Peeled(f)| Chopped(f))
        .map(|Chopped(f)| Cooked(f))
}

// Check whether there's food or not before trying to eat it!
fn eat(food: Option<Cooked>) {
    match food {
        Some(food) => println!("Mmm. I love {:?}", food),
        None       => println!("Oh no! It wasn't edible."),
    }
}

fn main() {
    let apple = Some(Food::Apple);
    let carrot = Some(Food::Carrot);
    let potato = None;

    let cooked_apple = cook(chop(peel(apple)));
    let cooked_carrot = cook(chop(peel(carrot)));
    // Let's try the simpler looking `process()` now.
    let cooked_potato = process(potato);

    eat(cooked_apple);
    eat(cooked_carrot);
    eat(cooked_potato);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- None stays None.

_RBE source: `error/option_unwrap/map.md`_

---

#### 19.1.3.3 Combinators: and_then

**What it is**

`map()` was described as a chainable way to simplify `match` statements. However, using `map()` on a function that returns an `Option<T>` results
in the nested `Option<Option<T>>`. Chaining multiple calls together can
then become confusing. That's where another combinator called `and_then()`,
known in some languages as flatmap, comes in.

**JS mental model**

and_then is flatMap for Option — when f itself returns Option.

**Rust example**

```rust
#![allow(dead_code)]

#[derive(Debug)] enum Food { CordonBleu, Steak, Sushi }
#[derive(Debug)] enum Day { Monday, Tuesday, Wednesday }

// We don't have the ingredients to make Sushi.
fn have_ingredients(food: Food) -> Option<Food> {
    match food {
        Food::Sushi => None,
        _           => Some(food),
    }
}

// We have the recipe for everything except Cordon Bleu.
fn have_recipe(food: Food) -> Option<Food> {
    match food {
        Food::CordonBleu => None,
        _                => Some(food),
    }
}

// To make a dish, we need both the recipe and the ingredients.
// We can represent the logic with a chain of `match`es:
fn cookable_v1(food: Food) -> Option<Food> {
    match have_recipe(food) {
        None       => None,
        Some(food) => have_ingredients(food),
    }
}

// This can conveniently be rewritten more compactly with `and_then()`:
fn cookable_v3(food: Food) -> Option<Food> {
    have_recipe(food).and_then(have_ingredients)
}

// Otherwise we'd need to `flatten()` an `Option<Option<Food>>`
// to get an `Option<Food>`:
fn cookable_v2(food: Food) -> Option<Food> {
    have_recipe(food).map(have_ingredients).flatten()
}

fn eat(food: Food, day: Day) {
    match cookable_v3(food) {
        Some(food) => println!("Yay! On {:?} we get to eat {:?}.", day, food),
        None       => println!("Oh no. We don't get to eat on {:?}?", day),
    }
}

fn main() {
    let (cordon_bleu, steak, sushi) = (Food::CordonBleu, Food::Steak, Food::Sushi);

    eat(cordon_bleu, Day::Monday);
    eat(steak, Day::Tuesday);
    eat(sushi, Day::Wednesday);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Avoids Option<Option<T>>.

_RBE source: `error/option_unwrap/and_then.md`_

---

#### 19.1.3.4 Defaults: or, or_else, get_or_insert, get_or_insert_with

**What it is**

There is more than one way to unpack an `Option` and fall back on a default if it is `None`. To choose the one that meets our needs, we need to consider the following:
* do we need eager or lazy evaluation? * do we need to keep the original empty value intact, or modify it in place? `or()` is chainable, evaluates eagerly, keeps empty value intact
`or()`is chainable and eagerly evaluates its argument, as is shown in the following example.

**JS mental model**

or / or_else / unwrap_or / get_or_insert supply defaults — like ?? and lazy defaults.

**Rust example**

```rust
#[derive(Debug)]
enum Fruit { Apple, Orange, Banana, Kiwi, Lemon }

fn main() {
    let mut my_fruit: Option<Fruit> = None;
    let get_lemon_as_fallback = || {
        println!("Providing lemon as fallback");
        Fruit::Lemon
    };
    let first_available_fruit = my_fruit
        .get_or_insert_with(get_lemon_as_fallback);
    println!("first_available_fruit is: {:?}", first_available_fruit);
    println!("my_fruit is: {:?}", my_fruit);
    // Providing lemon as fallback
    // first_available_fruit is: Lemon
    // my_fruit is: Some(Lemon)

    // If the Option has a value, it is left unchanged, and the closure is not invoked
    let mut my_apple = Some(Fruit::Apple);
    let should_be_apple = my_apple.get_or_insert_with(get_lemon_as_fallback);
    println!("should_be_apple is: {:?}", should_be_apple);
    println!("my_apple is unchanged: {:?}", my_apple);
    // The output is a follows. Note that the closure `get_lemon_as_fallback` is not invoked
    // should_be_apple is: Apple
    // my_apple is unchanged: Some(Apple)
}
```

```rust
#[derive(Debug)]
enum Fruit { Apple, Orange, Banana, Kiwi, Lemon }

fn main() {
    let apple = Some(Fruit::Apple);
    let orange = Some(Fruit::Orange);
    let no_fruit: Option<Fruit> = None;

    let first_available_fruit = no_fruit.or(orange).or(apple);
    println!("first_available_fruit: {:?}", first_available_fruit);
    // first_available_fruit: Some(Orange)

    // `or` moves its argument.
    // In the example above, `or(orange)` returned a `Some`, so `or(apple)` was not invoked.
    // But the variable named `apple` has been moved regardless, and cannot be used anymore.
    // println!("Variable apple was moved, so this line won't compile: {:?}", apple);
    // TODO: uncomment the line above to see the compiler error
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- or_else is lazy (closure); or is eager.

_RBE source: `error/option_unwrap/defaults.md`_

---

### 19.1.4 Result

**What it is**

[`Result`][result] is a richer version of the [`Option`][option] type that
describes possible *error* instead of possible *absence*. That is, `Result<T, E>` could have one of two outcomes:
* `Ok(T)`: An element `T` was found
* `Err(E)`: An error was found with element `E`
By convention, the expected outcome is `Ok` while the unexpected outcome is `Err`. Like `Option`, `Result` has many methods associated with it. `unwrap()`, for
example, either yields the element `T` or `panic`s.

**JS mental model**

Result<T,E> is Ok(T) or Err(E) — success/failure without exceptions.

**Rust example**

```rust
fn multiply(first_number_str: &str, second_number_str: &str) -> i32 {
    // Let's try using `unwrap()` to get the number out. Will it bite us?
    let first_number = first_number_str.parse::<i32>().unwrap();
    let second_number = second_number_str.parse::<i32>().unwrap();
    first_number * second_number
}

fn main() {
    let twenty = multiply("10", "2");
    println!("double is {}", twenty);

    let tt = multiply("t", "2");
    println!("double is {}", tt);
}
```

```rust
use std::num::ParseIntError;

fn main() -> Result<(), ParseIntError> {
    let number_str = "10";
    let number = match number_str.parse::<i32>() {
        Ok(number)  => number,
        Err(e) => return Err(e),
    };
    println!("{}", number);
    Ok(())
}
```

**JS equivalent**

```javascript
try { doWork(); } catch (e) { handle(e); }
```

**Watch out**

- match / ? / combinators to handle.
- Errors are values.

_RBE source: `error/result.md`_

---

#### 19.1.4.1 map for Result

**What it is**

Panicking in the previous example's `multiply` does not make for robust code. Generally, we want to return the error to the caller so it can decide what is
the right way to respond to errors. We first need to know what kind of error type we are dealing with. To determine
the `Err` type, we look to [`parse()`][parse], which is implemented with the
[`FromStr`][from_str] trait for [`i32`][i32].

**JS mental model**

map / map_err transform Ok/Err sides — like Promise.then on success only.

**Rust example**

```rust
use std::num::ParseIntError;

// With the return type rewritten, we use pattern matching without `unwrap()`.
fn multiply(first_number_str: &str, second_number_str: &str) -> Result<i32, ParseIntError> {
    match first_number_str.parse::<i32>() {
        Ok(first_number)  => {
            match second_number_str.parse::<i32>() {
                Ok(second_number)  => {
                    Ok(first_number * second_number)
                },
                Err(e) => Err(e),
            }
        },
        Err(e) => Err(e),
    }
}

fn print(result: Result<i32, ParseIntError>) {
    match result {
        Ok(n)  => println!("n is {}", n),
        Err(e) => println!("Error: {}", e),
    }
}

fn main() {
    // This still presents a reasonable answer.
    let twenty = multiply("10", "2");
    print(twenty);

    // The following now provides a much more helpful error message.
    let tt = multiply("t", "2");
    print(tt);
}
```

```rust
use std::num::ParseIntError;

// As with `Option`, we can use combinators such as `map()`.
// This function is otherwise identical to the one above and reads:
// Multiply if both values can be parsed from str, otherwise pass on the error.
fn multiply(first_number_str: &str, second_number_str: &str) -> Result<i32, ParseIntError> {
    first_number_str.parse::<i32>().and_then(|first_number| {
        second_number_str.parse::<i32>().map(|second_number| first_number * second_number)
    })
}

fn print(result: Result<i32, ParseIntError>) {
    match result {
        Ok(n)  => println!("n is {}", n),
        Err(e) => println!("Error: {}", e),
    }
}

fn main() {
    // This still presents a reasonable answer.
    let twenty = multiply("10", "2");
    print(twenty);

    // The following now provides a much more helpful error message.
    let tt = multiply("t", "2");
    print(tt);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Leaves the other side unchanged.

_RBE source: `error/result/result_map.md`_

---

#### 19.1.4.2 aliases for Result

**What it is**

How about when we want to reuse a specific `Result` type many times? Recall that Rust allows us to create [aliases][typealias]. Conveniently,
we can define one for the specific `Result` in question. At a module level, creating aliases can be particularly helpful.

**JS mental model**

type AliasedResult<T> = Result<T, MyError> shortens signatures — like a shared error type alias.

**Rust example**

```rust
use std::num::ParseIntError;

// Define a generic alias for a `Result` with the error type `ParseIntError`.
type AliasedResult<T> = Result<T, ParseIntError>;

// Use the above alias to refer to our specific `Result` type.
fn multiply(first_number_str: &str, second_number_str: &str) -> AliasedResult<i32> {
    first_number_str.parse::<i32>().and_then(|first_number| {
        second_number_str.parse::<i32>().map(|second_number| first_number * second_number)
    })
}

// Here, the alias again allows us to save some space.
fn print(result: AliasedResult<i32>) {
    match result {
        Ok(n)  => println!("n is {}", n),
        Err(e) => println!("Error: {}", e),
    }
}

fn main() {
    print(multiply("10", "2"));
    print(multiply("t", "2"));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Common in modules with one error type.

_RBE source: `error/result/result_alias.md`_

---

#### 19.1.4.3 Early returns

**What it is**

In the previous example, we explicitly handled the errors using combinators. Another way to deal with this case analysis is to use a combination of
`match` statements and *early returns*. That is, we can simply stop executing the function and return the error if
one occurs. For some, this form of code can be easier to both read and
write.

**JS mental model**

Match on Err and return Err(...) manually — the pattern ? automates.

**Rust example**

```rust
use std::num::ParseIntError;

fn multiply(first_number_str: &str, second_number_str: &str) -> Result<i32, ParseIntError> {
    let first_number = match first_number_str.parse::<i32>() {
        Ok(first_number)  => first_number,
        Err(e) => return Err(e),
    };

    let second_number = match second_number_str.parse::<i32>() {
        Ok(second_number)  => second_number,
        Err(e) => return Err(e),
    };

    Ok(first_number * second_number)
}

fn print(result: Result<i32, ParseIntError>) {
    match result {
        Ok(n)  => println!("n is {}", n),
        Err(e) => println!("Error: {}", e),
    }
}

fn main() {
    print(multiply("10", "2"));
    print(multiply("t", "2"));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Good to understand before using ? everywhere.

_RBE source: `error/result/early_returns.md`_

---

#### 19.1.4.4 Introducing ?

**What it is**

Sometimes we just want the simplicity of `unwrap` without the possibility of
a `panic`. Until now, `unwrap` has forced us to nest deeper and deeper when
what we really wanted was to get the variable *out*. This is exactly the purpose of `?`. Upon finding an `Err`, there are two valid actions to take:
1.

**JS mental model**

? unwraps Ok or returns Err from the function — like error propagation without try/catch.

**Rust example**

```rust
// To compile and run this example without errors, while using Cargo, change the value
// of the `edition` field, in the `[package]` section of the `Cargo.toml` file, to "2015".

use std::num::ParseIntError;

fn multiply(first_number_str: &str, second_number_str: &str) -> Result<i32, ParseIntError> {
    let first_number = try!(first_number_str.parse::<i32>());
    let second_number = try!(second_number_str.parse::<i32>());

    Ok(first_number * second_number)
}

fn print(result: Result<i32, ParseIntError>) {
    match result {
        Ok(n)  => println!("n is {}", n),
        Err(e) => println!("Error: {}", e),
    }
}

fn main() {
    print(multiply("10", "2"));
    print(multiply("t", "2"));
}
```

```rust
use std::num::ParseIntError;

fn multiply(first_number_str: &str, second_number_str: &str) -> Result<i32, ParseIntError> {
    let first_number = first_number_str.parse::<i32>()?;
    let second_number = second_number_str.parse::<i32>()?;

    Ok(first_number * second_number)
}

fn print(result: Result<i32, ParseIntError>) {
    match result {
        Ok(n)  => println!("n is {}", n),
        Err(e) => println!("Error: {}", e),
    }
}

fn main() {
    print(multiply("10", "2"));
    print(multiply("t", "2"));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Converts compatible errors via From.
- Function return type must be Result (or Option, etc.).

_RBE source: `error/result/enter_question_mark.md`_

---

### 19.1.5 Multiple error types

**What it is**

The previous examples have always been very convenient; `Result`s interact
with other `Result`s and `Option`s interact with other `Option`s. Sometimes an `Option` needs to interact with a `Result`, or a
`Result<T, Error1>` needs to interact with a `Result<T, Error2>`. In those
cases, we want to manage our different error types in a way that makes them
composable and easy to interact with. In the following code, two instances of `unwrap` generate different error
types.

**JS mental model**

Real programs mix error types — need aliases, boxing, or custom wrappers.

**Rust example**

```rust
fn double_first(vec: Vec<&str>) -> i32 {
    let first = vec.first().unwrap(); // Generate error 1
    2 * first.parse::<i32>().unwrap() // Generate error 2
}

fn main() {
    let numbers = vec!["42", "93", "18"];
    let empty = vec![];
    let strings = vec!["tofu", "93", "18"];

    println!("The first doubled is {}", double_first(numbers));

    println!("The first doubled is {}", double_first(empty));
    // Error 1: the input vector is empty

    println!("The first doubled is {}", double_first(strings));
    // Error 2: the element doesn't parse to a number
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Pick one strategy per crate layer.

_RBE source: `error/multiple_error_types.md`_

---

#### 19.1.5.1 Pulling Results out of Options

**What it is**

The most basic way of handling mixed error types is to just embed them in each
other. There are times when we'll want to stop processing on errors (like with
[`?`][enter_question_mark]) but keep going when the `Option` is `None`. The `transpose` function comes in handy to swap the `Result` and `Option`. [enter_question_mark]: ../result/enter_question_mark.md.

**JS mental model**

Composing Option and Result needs careful conversion (ok_or, transpose).

**Rust example**

```rust
use std::num::ParseIntError;

fn double_first(vec: Vec<&str>) -> Option<Result<i32, ParseIntError>> {
    vec.first().map(|first| {
        first.parse::<i32>().map(|n| 2 * n)
    })
}

fn main() {
    let numbers = vec!["42", "93", "18"];
    let empty = vec![];
    let strings = vec!["tofu", "93", "18"];

    println!("The first doubled is {:?}", double_first(numbers));

    println!("The first doubled is {:?}", double_first(empty));
    // Error 1: the input vector is empty

    println!("The first doubled is {:?}", double_first(strings));
    // Error 2: the element doesn't parse to a number
}
```

```rust
use std::num::ParseIntError;

fn double_first(vec: Vec<&str>) -> Result<Option<i32>, ParseIntError> {
    let opt = vec.first().map(|first| {
        first.parse::<i32>().map(|n| 2 * n)
    });

    opt.transpose()
}

fn main() {
    let numbers = vec!["42", "93", "18"];
    let empty = vec![];
    let strings = vec!["tofu", "93", "18"];

    println!("The first doubled is {:?}", double_first(numbers));
    println!("The first doubled is {:?}", double_first(empty));
    println!("The first doubled is {:?}", double_first(strings));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- transpose turns Option<Result<T,E>> into Result<Option<T>,E>.

_RBE source: `error/multiple_error_types/option_result.md`_

---

#### 19.1.5.2 Defining an error type

**What it is**

Sometimes it simplifies the code to mask all of the different errors with a
single type of error. We'll show this with a custom error. Rust allows us to define our own error types. In general, a "good" error type:
* Represents different errors with the same type
* Presents nice error messages to the user
* Is easy to compare with other types
  * Good: `Err(EmptyVec)`
  * Bad: `Err("Please use a vector with at least one element".to_owned())`
* Can hold information about the error
  * Good: `Err(BadChar(c, position))`
  * Bad: `Err("+ cannot be used here".to_owned())`
* Composes well with other errors.

**JS mental model**

Define your own error enum — like a custom Error subclass hierarchy but as data.

**Rust example**

```rust
use std::fmt;

type Result<T> = std::result::Result<T, DoubleError>;

// Define our error types. These may be customized for our error handling cases.
// Now we will be able to write our own errors, defer to an underlying error
// implementation, or do something in between.
#[derive(Debug, Clone)]
struct DoubleError;

// Generation of an error is completely separate from how it is displayed.
// There's no need to be concerned about cluttering complex logic with the display style.
//
// Note that we don't store any extra info about the errors. This means we can't state
// which string failed to parse without modifying our types to carry that information.
impl fmt::Display for DoubleError {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "invalid first item to double")
    }
}

fn double_first(vec: Vec<&str>) -> Result<i32> {
    vec.first()
        // Change the error to our new type.
        .ok_or(DoubleError)
        .and_then(|s| {
            s.parse::<i32>()
                // Update to the new error type here also.
                .map_err(|_| DoubleError)
                .map(|i| 2 * i)
        })
}

fn print(result: Result<i32>) {
    match result {
        Ok(n) => println!("The first doubled is {}", n),
        Err(e) => println!("Error: {}", e),
    }
}

fn main() {
    let numbers = vec!["42", "93", "18"];
    let empty = vec![];
    let strings = vec!["tofu", "93", "18"];

    print(double_first(numbers));
    print(double_first(empty));
    print(double_first(strings));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Implement Display + Error.
- thiserror crate helps later.

_RBE source: `error/multiple_error_types/define_error_type.md`_

---

#### 19.1.5.3 Boxing errors

**What it is**

A way to write simple code while preserving the original errors is to [`Box`][box]
them. The drawback is that the underlying error type is only known at runtime and not
[statically determined][dynamic_dispatch]. The stdlib helps in boxing our errors by having `Box` implement conversion from
any type that implements the `Error` trait into the trait object `Box<Error>`,
via [`From`][from]. See also:
[Dynamic dispatch][dynamic_dispatch] and [`Error` trait][error]
[box]: https://doc.rust-lang.org/std/boxed/struct.Box.html
[dynamic_dispatch]: https://doc.rust-lang.org/book/ch17-02-trait-objects.html#trait-objects-perform-dynamic-dispatch
[error]: https://doc.rust-lang.org/std/error/trait.Error.html
[from]: https://doc.rust-lang.org/std/convert/trait.From.html.

**JS mental model**

Box<dyn Error> erases error type — like catching unknown error interface.

**Rust example**

```rust
use std::error;
use std::fmt;

// Change the alias to use `Box<dyn error::Error>`.
type Result<T> = std::result::Result<T, Box<dyn error::Error>>;

#[derive(Debug, Clone)]
struct EmptyVec;

impl fmt::Display for EmptyVec {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "invalid first item to double")
    }
}

impl error::Error for EmptyVec {}

fn double_first(vec: Vec<&str>) -> Result<i32> {
    vec.first()
        .ok_or_else(|| EmptyVec.into()) // Converts to Box using Into trait.
        .and_then(|s| {
            s.parse::<i32>()
                .map_err(From::from) // Converts to Box using From::from fn pointer.
                .map(|i| 2 * i)
        })
}

fn print(result: Result<i32>) {
    match result {
        Ok(n) => println!("The first doubled is {}", n),
        Err(e) => println!("Error: {}", e),
    }
}

fn main() {
    let numbers = vec!["42", "93", "18"];
    let empty = vec![];
    let strings = vec!["tofu", "93", "18"];

    print(double_first(numbers));
    print(double_first(empty));
    print(double_first(strings));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Simple for apps; libraries often prefer concrete errors.
- Lose static matching on variants.

_RBE source: `error/multiple_error_types/boxing_errors.md`_

---

#### 19.1.5.4 Other uses of ?

**What it is**

Notice in the previous example that our immediate reaction to calling
`parse` is to `map` the error from a library error into a boxed
error:
Since this is a simple and common operation, it would be convenient if it
could be elided. Alas, because `and_then` is not sufficiently flexible, it
cannot. However, we can instead use `?`. `?` was previously explained as either `unwrap` or `return Err(err)`.

**JS mental model**

? can convert errors via From — one ? can map underlying errors into yours.

**Rust example**

```rust
use std::error;
use std::fmt;

// Change the alias to use `Box<dyn error::Error>`.
type Result<T> = std::result::Result<T, Box<dyn error::Error>>;

#[derive(Debug)]
struct EmptyVec;

impl fmt::Display for EmptyVec {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "invalid first item to double")
    }
}

impl error::Error for EmptyVec {}

// The same structure as before but rather than chain all `Results`
// and `Options` along, we `?` to get the inner value out immediately.
fn double_first(vec: Vec<&str>) -> Result<i32> {
    let first = vec.first().ok_or(EmptyVec)?;
    let parsed = first.parse::<i32>()?;
    Ok(2 * parsed)
}

fn print(result: Result<i32>) {
    match result {
        Ok(n)  => println!("The first doubled is {}", n),
        Err(e) => println!("Error: {}", e),
    }
}

fn main() {
    let numbers = vec!["42", "93", "18"];
    let empty = vec![];
    let strings = vec!["tofu", "93", "18"];

    print(double_first(numbers));
    print(double_first(empty));
    print(double_first(strings));
}
```

```rust
.and_then(|s| s.parse::<i32>())
    .map_err(|e| e.into())
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Implement From<Other> for MyError.

_RBE source: `error/multiple_error_types/reenter_question_mark.md`_

---

#### 19.1.5.5 Wrapping errors

**What it is**

An alternative to boxing errors is to wrap them in your own error type. This adds a bit more boilerplate for handling errors and might not be needed in
all applications. There are some libraries that can take care of the boilerplate
for you. See also:
[`From::from`][from] and [`Enums`][enums]
[`Crates for handling errors`][crates-errors]
[from]: https://doc.rust-lang.org/std/convert/trait.From.html
[enums]: ../../custom_types/enum.md
[crates-errors]: https://crates.io/keywords/error-handling.

**JS mental model**

Wrap underlying errors in your enum to add context — like cause chaining.

**Rust example**

```rust
use std::error;
use std::error::Error;
use std::num::ParseIntError;
use std::fmt;

type Result<T> = std::result::Result<T, DoubleError>;

#[derive(Debug)]
enum DoubleError {
    EmptyVec,
    // We will defer to the parse error implementation for their error.
    // Supplying extra info requires adding more data to the type.
    Parse(ParseIntError),
}

impl fmt::Display for DoubleError {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        match *self {
            DoubleError::EmptyVec =>
                write!(f, "please use a vector with at least one element"),
            // The wrapped error contains additional information and is available
            // via the source() method.
            DoubleError::Parse(..) =>
                write!(f, "the provided string could not be parsed as int"),
        }
    }
}

impl error::Error for DoubleError {
    fn source(&self) -> Option<&(dyn error::Error + 'static)> {
        match *self {
            DoubleError::EmptyVec => None,
            // The cause is the underlying implementation error type. Is implicitly
            // cast to the trait object `&error::Error`. This works because the
            // underlying type already implements the `Error` trait.
            DoubleError::Parse(ref e) => Some(e),
        }
    }
}

// Implement the conversion from `ParseIntError` to `DoubleError`.
// This will be automatically called by `?` if a `ParseIntError`
// needs to be converted into a `DoubleError`.
impl From<ParseIntError> for DoubleError {
    fn from(err: ParseIntError) -> DoubleError {
        DoubleError::Parse(err)
    }
}

fn double_first(vec: Vec<&str>) -> Result<i32> {
    let first = vec.first().ok_or(DoubleError::EmptyVec)?;
    // Here we implicitly use the `ParseIntError` implementation of `From` (which
    // we defined above) in order to create a `DoubleError`.
    let parsed = first.parse::<i32>()?;

    Ok(2 * parsed)
}

fn print(result: Result<i32>) {
    match result {
        Ok(n)  => println!("The first doubled is {}", n),
        Err(e) => {
            println!("Error: {}", e);
            if let Some(source) = e.source() {
                println!("  Caused by: {}", source);
            }
        },
    }
}

fn main() {
    let numbers = vec!["42", "93", "18"];
    let empty = vec![];
    let strings = vec!["tofu", "93", "18"];

    print(double_first(numbers));
    print(double_first(empty));
    print(double_first(strings));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- source() on std::error::Error enables chains.
- thiserror/anyhow are popular helpers.

_RBE source: `error/multiple_error_types/wrap_error.md`_

---

### 19.1.6 Iterating over Results

**What it is**

An `Iter::map` operation might fail, for example:
Let's step through strategies for handling this. Ignore the failed items with `filter_map()`
`filter_map` calls a function and filters out the results that are `None`. Collect the failed items with `map_err()` and `filter_map()`
`map_err` calls a function with the error, so by adding that to the previous
`filter_map` solution we can save them off to the side while iterating. Fail the entire operation with `collect()`
`Result` implements `FromIterator` so that a vector of results (`Vec<Result<T, E>>`)
can be turned into a result with a vector (`Result<Vec<T>, E>`).

**JS mental model**

Iterators of Results: collect into Result<Vec<_>, E> or partition successes/failures.

**Rust example**

```rust
fn main() {
    let strings = vec!["tofu", "93", "18"];
    let (numbers, errors): (Vec<_>, Vec<_>) = strings
        .into_iter()
        .map(|s| s.parse::<i32>())
        .partition(Result::is_ok);
    let numbers: Vec<_> = numbers.into_iter().map(Result::unwrap).collect();
    let errors: Vec<_> = errors.into_iter().map(Result::unwrap_err).collect();
    println!("Numbers: {:?}", numbers);
    println!("Errors: {:?}", errors);
}
```

```rust
fn main() {
    let strings = vec!["42", "tofu", "93", "999", "18"];
    let mut errors = vec![];
    let numbers: Vec<_> = strings
        .into_iter()
        .map(|s| s.parse::<u8>())
        .filter_map(|r| r.map_err(|e| errors.push(e)).ok())
        .collect();
    println!("Numbers: {:?}", numbers);
    println!("Errors: {:?}", errors);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- collect::<Result<Vec<_>,_>>() stops at first Err.
- Decide fail-fast vs gather-all.

_RBE source: `error/iter_result.md`_

---
