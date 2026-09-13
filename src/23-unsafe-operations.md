# Unsafe operations

_RBE chapter root maps here. Every nested leaf below has the full 5-part teaching block._

### 23.1 Unsafe Operations

**What it is**

Unsafe Rust lets you do five extra things: dereference raw pointers, call unsafe functions, implement unsafe traits, access mutable statics, and access fields of unions. The rest of the language stays the same. Safety obligations move to you.

**JS mental model**

unsafe unlocks a few extra powers (raw pointers, FFI, mutable statics, unions) — you promise to uphold invariants the compiler cannot check.

**Rust example**

```rust
use std::slice;

fn main() {
    let some_vector = vec![1, 2, 3, 4];

    let pointer = some_vector.as_ptr();
    let length = some_vector.len();

    unsafe {
        let my_slice: &[u32] = slice::from_raw_parts(pointer, length);

        assert_eq!(some_vector.as_slice(), my_slice);
    }
}
```

```rust
fn main() {
    let raw_p: *const u32 = &10;

    unsafe {
        assert!(*raw_p == 10);
    }
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Keep unsafe blocks small.
- Prefer safe wrappers.
- Never use unsafe to silence borrow errors casually.

_RBE source: `unsafe.md`_

---

### 23.1.1 Inline assembly

**What it is**

The asm! macro inserts assembly instructions into the compiler output. You specify template strings plus in/out registers. It is advanced, architecture-specific, and always unsafe. Most JS-coming developers will never need it — know it exists.

**JS mental model**

Inline assembly embeds CPU instructions — like writing a tiny asm block; almost never needed in app JS-land, sometimes in kernels/drivers.

**Rust example**

```rust
use std::arch::asm;

#[cfg(target_arch = "x86_64")]
fn main() {
    // three entries of four bytes each
    let mut name_buf = [0_u8; 12];
    // String is stored as ascii in ebx, edx, ecx in order
    // Because ebx is reserved, the asm needs to preserve the value of it.
    // So we push and pop it around the main asm.
    // 64 bit mode on 64 bit processors does not allow pushing/popping of
    // 32 bit registers (like ebx), so we have to use the extended rbx register instead.

    unsafe {
        asm!(
            "push rbx",
            "cpuid",
            "mov [rdi], ebx",
            "mov [rdi + 4], edx",
            "mov [rdi + 8], ecx",
            "pop rbx",
            // We use a pointer to an array for storing the values to simplify
            // the Rust code at the cost of a couple more asm instructions
            // This is more explicit with how the asm works however, as opposed
            // to explicit register outputs such as `out("ecx") val`
            // The *pointer itself* is only an input even though it's written behind
            in("rdi") name_buf.as_mut_ptr(),
            // select cpuid 0, also specify eax as clobbered
            inout("eax") 0 => _,
            // cpuid clobbers these registers too
            out("ecx") _,
            out("edx") _,
        );
    }

    let name = core::str::from_utf8(&name_buf).unwrap();
    println!("CPU Manufacturer ID: {}", name);
}

#[cfg(not(target_arch = "x86_64"))]
fn main() {}
```

```rust
#[cfg(target_arch = "x86_64")] {
use std::arch::asm;

extern "C" fn foo(arg: i32) -> i32 {
    println!("arg = {}", arg);
    arg * 2
}

fn call_foo(arg: i32) -> i32 {
    unsafe {
        let result;
        asm!(
            "call {}",
            // Function pointer to call
            in(reg) foo,
            // 1st argument in rdi
            in("rdi") arg,
            // Return value in rax
            out("rax") result,
            // Mark all registers which are not preserved by the "C" calling
            // convention as clobbered.
            clobber_abi("C"),
        );
        result
    }
}
}
```

**JS equivalent**

_No perfect one-liner — use the mental model above._

**Watch out**

- Always inside unsafe.
- Architecture-specific.
- Easy to invent UB — avoid unless you know why.

_RBE source: `unsafe/asm.md`_

---
