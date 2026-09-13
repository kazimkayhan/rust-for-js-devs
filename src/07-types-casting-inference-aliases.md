# Types, casting, inference, aliases

_RBE chapter root maps here. Every nested leaf below has the full 5-part teaching block._

### 7.1 Types

**What it is**

Rust provides several mechanisms to change or define the type of primitive and
user defined types. The following sections cover:
* [Casting] between primitive types
* Specifying the desired type of [literals]
* Using [type inference]
* [Aliasing] types
[Casting]: types/cast.md
[literals]: types/literals.md
[type inference]: types/inference.md
[Aliasing]: types/alias.md.

**JS mental model**

JS types are runtime tags. Rust types are checked at compile time and often erased at runtime (zero-cost).

**Rust example**

```rust
fn main() {
    let x: i32 = 5;
    let y = x as i64;
    println!("{x} {y}");
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Trust the compiler — fix type errors instead of casting blindly.

_RBE source: `types.md`_

---

### 7.1.1 Casting

**What it is**

Rust provides no implicit type conversion (coercion) between primitive types. But, explicit type conversion (casting) can be performed using the `as` keyword. Rules for converting between integral types follow C conventions generally,
except in cases where C has undefined behavior. The behavior of all casts
between integral types is well defined in Rust.

**JS mental model**

`as` casts are explicit (like Number(x) but more dangerous for integers). No implicit widening in many cases.

**Rust example**

```rust
// Suppress all errors from casts which overflow.
#![allow(overflowing_literals)]

fn main() {
    let decimal = 65.4321_f32;

    // Error! No implicit conversion
    let integer: u8 = decimal;
    // FIXME ^ Comment out this line

    // Explicit conversion
    let integer = decimal as u8;
    let character = integer as char;

    // Error! There are limitations in conversion rules.
    // A float cannot be directly converted to a char.
    let character = decimal as char;
    // FIXME ^ Comment out this line

    println!("Casting: {} -> {} -> {}", decimal, integer, character);

    // when casting any value to an unsigned type, T,
    // T::MAX + 1 is added or subtracted until the value
    // fits into the new type ONLY when the #![allow(overflowing_literals)]
    // lint is specified like above. Otherwise there will be a compiler error.

    // 1000 already fits in a u16
    println!("1000 as a u16 is: {}", 1000 as u16);

    // 1000 - 256 - 256 - 256 = 232
    // Under the hood, the first 8 least significant bits (LSB) are kept,
    // while the rest towards the most significant bit (MSB) get truncated.
    println!("1000 as a u8 is : {}", 1000 as u8);
    // -1 + 256 = 255
    println!("  -1 as a u8 is : {}", (-1i8) as u8);

    // For positive numbers, this is the same as the modulus
    println!("1000 mod 256 is : {}", 1000 % 256);

    // When casting to a signed type, the (bitwise) result is the same as
    // first casting to the corresponding unsigned type. If the most significant
    // bit of that value is 1, then the value is negative.

    // Unless it already fits, of course.
    println!(" 128 as a i16 is: {}", 128 as i16);

    // In boundary case 128 value in 8-bit two's complement representation is -128
    println!(" 128 as a i8 is : {}", 128 as i8);

    // repeating the example above
    // 1000 as u8 -> 232
    println!("1000 as a u8 is : {}", 1000 as u8);
    // and the value of 232 in 8-bit two's complement representation is -24
    println!(" 232 as a i8 is : {}", 232 as i8);

    // Since Rust 1.45, the `as` keyword performs a *saturating cast*
    // when casting from float to int. If the floating point value exceeds
    // the upper bound or is less than the lower bound, the returned value
    // will be equal to the bound crossed.

    // 300.0 as u8 is 255
    println!(" 300.0 as u8 is : {}", 300.0_f32 as u8);
    // -100.0 as u8 is 0
    println!("-100.0 as u8 is : {}", -100.0_f32 as u8);
    // nan as u8 is 0
    println!("   nan as u8 is : {}", f32::NAN as u8);

    // This behavior incurs a small runtime cost and can be avoided
    // with unsafe methods, however the results might overflow and
    // return **unsound values**. Use these methods wisely:
    unsafe {
        // 300.0 as u8 is 44
        println!(" 300.0 as u8 is : {}", 300.0_f32.to_int_unchecked::<u8>());
        // -100.0 as u8 is 156
        println!("-100.0 as u8 is : {}", (-100.0_f32).to_int_unchecked::<u8>());
        // nan as u8 is 0
        println!("   nan as u8 is : {}", f32::NAN.to_int_unchecked::<u8>());
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Casting can truncate. Prefer `From`/`TryFrom`.
- Float→int truncates toward zero.

_RBE source: `types/cast.md`_

---

### 7.1.2 Literals

**What it is**

Numeric literals can be type annotated by adding the type as a suffix. As an example,
to specify that the literal `42` should have the type `i32`, write `42i32`. The type of unsuffixed numeric literals will depend on how they are used. If no
constraint exists, the compiler will use `i32` for integers, and `f64` for
floating-point numbers.

**JS mental model**

Type suffixes (`42i32`) and inference from usage — TS-like inference but stricter.

**Rust example**

```rust
fn main() {
    // Suffixed literals, their types are known at initialization
    let x = 1u8;
    let y = 2u32;
    let z = 3f32;

    // Unsuffixed literals, their types depend on how they are used
    let i = 1;
    let f = 1.0;

    // `size_of_val` returns the size of a variable in bytes
    println!("size of `x` in bytes: {}", std::mem::size_of_val(&x));
    println!("size of `y` in bytes: {}", std::mem::size_of_val(&y));
    println!("size of `z` in bytes: {}", std::mem::size_of_val(&z));
    println!("size of `i` in bytes: {}", std::mem::size_of_val(&i));
    println!("size of `f` in bytes: {}", std::mem::size_of_val(&f));
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- If inference fails, add a type annotation on `let` or a suffix.

_RBE source: `types/literals.md`_

---

### 7.1.3 Inference

**What it is**

The type inference engine is pretty smart. It does more than looking at the
type of the value expression
during an initialization. It also looks at how the variable is used afterwards
to infer its type. Here's an advanced example of type inference:
No type annotation of variables was needed, the compiler is happy and so is the
programmer!

**JS mental model**

Compiler fills in types from context — similar to TS inference, but must be unambiguous.

**Rust example**

```rust
fn main() {
    // Because of the annotation, the compiler knows that `elem` has type u8.
    let elem = 5u8;

    // Create an empty vector (a growable array).
    let mut vec = Vec::new();
    // At this point the compiler doesn't know the exact type of `vec`, it
    // just knows that it's a vector of something (`Vec<_>`).

    // Insert `elem` in the vector.
    vec.push(elem);
    // Aha! Now the compiler knows that `vec` is a vector of `u8`s (`Vec<u8>`)
    // TODO ^ Try commenting out the `vec.push(elem)` line

    println!("{:?}", vec);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Sometimes annotate the collection: `Vec::<i32>::new()` turbofish.

_RBE source: `types/inference.md`_

---

### 7.1.4 Aliasing

**What it is**

The `type` statement can be used to give a new name to an existing type. Types
must have `UpperCamelCase` names, or the compiler will raise a warning. The
exception to this rule are the primitive types: `usize`, `f32`, etc. The main use of aliases is to reduce boilerplate; for example the `io::Result<T>` type
is an alias for the `Result<T, io::Error>` type.

**JS mental model**

`type Name = Existing;` is like `type` aliases in TS — no new type, just a name.

**Rust example**

```rust
// `NanoSecond`, `Inch`, and `U64` are new names for `u64`.
type NanoSecond = u64;
type Inch = u64;
type U64 = u64;

fn main() {
    // `NanoSecond` = `Inch` = `U64` = `u64`.
    let nanoseconds: NanoSecond = 5 as u64;
    let inches: Inch = 2 as U64;

    // Note that type aliases *don't* provide any extra type safety, because
    // aliases are *not* new types
    println!("{} nanoseconds + {} inches = {} unit?",
             nanoseconds,
             inches,
             nanoseconds + inches);
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Aliases do not create type safety; newtype idiom does (see Generics).

_RBE source: `types/alias.md`_

---
