# RBE coverage checklist

Every leaf from `SUMMARY.md` mapped to a heading in `RUST_FROM_JS_ZERO_TO_HERO.md`.

Total leaves: **197**

| Status | RBE path | RBE title | Guide heading |
|---|---|---|---|
| DONE | `index.md` | Introduction | 1b / 1.0 RBE Introduction leaf |
| DONE | `hello.md` | Hello World | 3.1 Hello World |
| DONE | `hello/comment.md` | Comments | 3.1.1 Comments |
| DONE | `hello/print.md` | Formatted print | 3.1.2 Formatted print |
| DONE | `hello/print/print_debug.md` | Debug | 3.1.2.1 Debug |
| DONE | `hello/print/print_display.md` | Display | 3.1.2.2 Display |
| DONE | `hello/print/print_display/testcase_list.md` | Testcase: List | 3.1.2.2.1 Testcase: List |
| DONE | `hello/print/fmt.md` | Formatting | 3.1.2.3 Formatting |
| DONE | `primitives.md` | Primitives | 4.1 Primitives |
| DONE | `primitives/literals.md` | Literals and operators | 4.1.1 Literals and operators |
| DONE | `primitives/tuples.md` | Tuples | 4.1.2 Tuples |
| DONE | `primitives/array.md` | Arrays and Slices | 4.1.3 Arrays and Slices |
| DONE | `custom_types.md` | Custom Types | 5.1 Custom Types |
| DONE | `custom_types/structs.md` | Structures | 5.1.1 Structures |
| DONE | `custom_types/enum.md` | Enums | 5.1.2 Enums |
| DONE | `custom_types/enum/enum_use.md` | use | 5.1.2.1 use |
| DONE | `custom_types/enum/c_like.md` | C-like | 5.1.2.2 C-like |
| DONE | `custom_types/enum/testcase_linked_list.md` | Testcase: linked-list | 5.1.2.3 Testcase: linked-list |
| DONE | `custom_types/constants.md` | constants | 5.1.3 constants |
| DONE | `variable_bindings.md` | Variable Bindings | 6.1 Variable Bindings |
| DONE | `variable_bindings/mut.md` | Mutability | 6.1.1 Mutability |
| DONE | `variable_bindings/scope.md` | Scope and Shadowing | 6.1.2 Scope and Shadowing |
| DONE | `variable_bindings/declare.md` | Declare first | 6.1.3 Declare first |
| DONE | `variable_bindings/freeze.md` | Freezing | 6.1.4 Freezing |
| DONE | `types.md` | Types | 7.1 Types |
| DONE | `types/cast.md` | Casting | 7.1.1 Casting |
| DONE | `types/literals.md` | Literals | 7.1.2 Literals |
| DONE | `types/inference.md` | Inference | 7.1.3 Inference |
| DONE | `types/alias.md` | Aliasing | 7.1.4 Aliasing |
| DONE | `conversion.md` | Conversion | 8.1 Conversion |
| DONE | `conversion/from_into.md` | `From` and `Into` | 8.1.1 From and Into |
| DONE | `conversion/try_from_try_into.md` | `TryFrom` and `TryInto` | 8.1.2 TryFrom and TryInto |
| DONE | `conversion/string.md` | To and from `String`s | 8.1.3 To and from Strings |
| DONE | `expression.md` | Expressions | 9.1 Expressions |
| DONE | `flow_control.md` | Flow of Control | 10.1 Flow of Control |
| DONE | `flow_control/if_else.md` | if/else | 10.1.1 if/else |
| DONE | `flow_control/loop.md` | loop | 10.1.2 loop |
| DONE | `flow_control/loop/nested.md` | Nesting and labels | 10.1.2.1 Nesting and labels |
| DONE | `flow_control/loop/return.md` | Returning from loops | 10.1.2.2 Returning from loops |
| DONE | `flow_control/while.md` | while | 10.1.3 while |
| DONE | `flow_control/for.md` | for and range | 10.1.4 for and range |
| DONE | `flow_control/match.md` | match | 10.1.5 match |
| DONE | `flow_control/match/destructuring.md` | Destructuring | 10.1.5.1 Destructuring |
| DONE | `flow_control/match/destructuring/destructure_tuple.md` | tuples | 10.1.5.1.1 tuples |
| DONE | `flow_control/match/destructuring/destructure_slice.md` | arrays/slices | 10.1.5.1.2 arrays/slices |
| DONE | `flow_control/match/destructuring/destructure_enum.md` | enums | 10.1.5.1.3 enums |
| DONE | `flow_control/match/destructuring/destructure_pointers.md` | pointers/ref | 10.1.5.1.4 pointers/ref |
| DONE | `flow_control/match/destructuring/destructure_structures.md` | structs | 10.1.5.1.5 structs |
| DONE | `flow_control/match/guard.md` | Guards | 10.1.5.2 Guards |
| DONE | `flow_control/match/binding.md` | Binding | 10.1.5.3 Binding |
| DONE | `flow_control/if_let.md` | if let | 10.1.6 if let |
| DONE | `flow_control/let_else.md` | let-else | 10.1.7 let-else |
| DONE | `flow_control/while_let.md` | while let | 10.1.8 while let |
| DONE | `fn.md` | Functions | 11.1 Functions |
| DONE | `fn/methods.md` | Methods | 11.1.1 Methods |
| DONE | `fn/closures.md` | Closures | 11.1.2 Closures |
| DONE | `fn/closures/capture.md` | Capturing | 11.1.2.1 Capturing |
| DONE | `fn/closures/input_parameters.md` | As input parameters | 11.1.2.2 As input parameters |
| DONE | `fn/closures/anonymity.md` | Type anonymity | 11.1.2.3 Type anonymity |
| DONE | `fn/closures/input_functions.md` | Input functions | 11.1.2.4 Input functions |
| DONE | `fn/closures/output_parameters.md` | As output parameters | 11.1.2.5 As output parameters |
| DONE | `fn/closures/closure_examples.md` | Examples in `std` | 11.1.2.6 Examples in std |
| DONE | `fn/closures/closure_examples/iter_any.md` | Iterator::any | 11.1.2.6.1 Iterator::any |
| DONE | `fn/closures/closure_examples/iter_find.md` | Searching through iterators | 11.1.2.6.2 Searching through iterators |
| DONE | `fn/hof.md` | Higher Order Functions | 11.1.3 Higher Order Functions |
| DONE | `fn/diverging.md` | Diverging functions | 11.1.4 Diverging functions |
| DONE | `mod.md` | Modules | 12.1 Modules |
| DONE | `mod/visibility.md` | Visibility | 12.1.1 Visibility |
| DONE | `mod/struct_visibility.md` | Struct visibility | 12.1.2 Struct visibility |
| DONE | `mod/use.md` | The `use` declaration | 12.1.3 The use declaration |
| DONE | `mod/super.md` | `super` and `self` | 12.1.4 super and self |
| DONE | `mod/split.md` | File hierarchy | 12.1.5 File hierarchy |
| DONE | `crates.md` | Crates | 13.1 Crates |
| DONE | `crates/lib.md` | Creating a Library | 13.1.1 Creating a Library |
| DONE | `crates/using_lib.md` | Using a Library | 13.1.2 Using a Library |
| DONE | `cargo.md` | Cargo | 13.1.3 Cargo |
| DONE | `cargo/deps.md` | Dependencies | 13.1.3.1 Dependencies |
| DONE | `cargo/conventions.md` | Conventions | 13.1.3.2 Conventions |
| DONE | `cargo/test.md` | Tests | 13.1.3.3 Tests |
| DONE | `cargo/build_scripts.md` | Build Scripts | 13.1.3.4 Build Scripts |
| DONE | `attribute.md` | Attributes | 14.1 Attributes |
| DONE | `attribute/unused.md` | `dead_code` | 14.1.1 dead_code |
| DONE | `attribute/crate.md` | Crates | 14.1.2 Crates |
| DONE | `attribute/cfg.md` | `cfg` | 14.1.3 cfg |
| DONE | `attribute/cfg/custom.md` | Custom | 14.1.3.1 Custom |
| DONE | `generics.md` | Generics | 15.1 Generics |
| DONE | `generics/gen_fn.md` | Functions | 15.1.1 Functions |
| DONE | `generics/impl.md` | Implementation | 15.1.2 Implementation |
| DONE | `generics/gen_trait.md` | Traits | 15.1.3 Traits |
| DONE | `generics/bounds.md` | Bounds | 15.1.4 Bounds |
| DONE | `generics/bounds/testcase_empty.md` | Testcase: empty bounds | 15.1.4.1 Testcase: empty bounds |
| DONE | `generics/multi_bounds.md` | Multiple bounds | 15.1.5 Multiple bounds |
| DONE | `generics/where.md` | Where clauses | 15.1.6 Where clauses |
| DONE | `generics/new_types.md` | New Type Idiom | 15.1.7 New Type Idiom |
| DONE | `generics/assoc_items.md` | Associated items | 15.1.8 Associated items |
| DONE | `generics/assoc_items/the_problem.md` | The Problem | 15.1.8.1 The Problem |
| DONE | `generics/assoc_items/types.md` | Associated types | 15.1.8.2 Associated types |
| DONE | `generics/phantom.md` | Phantom type parameters | 15.1.9 Phantom type parameters |
| DONE | `generics/phantom/testcase_units.md` | Testcase: unit clarification | 15.1.9.1 Testcase: unit clarification |
| DONE | `scope.md` | Scoping rules | 16.1 Scoping rules |
| DONE | `scope/raii.md` | RAII | 16.1.1 RAII |
| DONE | `scope/move.md` | Ownership and moves | 16.1.2 Ownership and moves |
| DONE | `scope/move/mut.md` | Mutability | 16.1.2.1 Mutability |
| DONE | `scope/move/partial_move.md` | Partial moves | 16.1.2.2 Partial moves |
| DONE | `scope/borrow.md` | Borrowing | 16.1.3 Borrowing |
| DONE | `scope/borrow/mut.md` | Mutability | 16.1.3.1 Mutability |
| DONE | `scope/borrow/alias.md` | Aliasing | 16.1.3.2 Aliasing |
| DONE | `scope/borrow/ref.md` | The ref pattern | 16.1.3.3 The ref pattern |
| DONE | `scope/lifetime.md` | Lifetimes | 16.1.4 Lifetimes |
| DONE | `scope/lifetime/explicit.md` | Explicit annotation | 16.1.4.1 Explicit annotation |
| DONE | `scope/lifetime/fn.md` | Functions | 16.1.4.2 Functions |
| DONE | `scope/lifetime/methods.md` | Methods | 16.1.4.3 Methods |
| DONE | `scope/lifetime/struct.md` | Structs | 16.1.4.4 Structs |
| DONE | `scope/lifetime/trait.md` | Traits | 16.1.4.5 Traits |
| DONE | `scope/lifetime/lifetime_bounds.md` | Bounds | 16.1.4.6 Bounds |
| DONE | `scope/lifetime/lifetime_coercion.md` | Coercion | 16.1.4.7 Coercion |
| DONE | `scope/lifetime/static_lifetime.md` | Static | 16.1.4.8 Static |
| DONE | `scope/lifetime/elision.md` | Elision | 16.1.4.9 Elision |
| DONE | `trait.md` | Traits | 17.1 Traits |
| DONE | `trait/derive.md` | Derive | 17.1.1 Derive |
| DONE | `trait/dyn.md` | Returning Traits with `dyn` | 17.1.2 Returning Traits with dyn |
| DONE | `trait/ops.md` | Operator Overloading | 17.1.3 Operator Overloading |
| DONE | `trait/drop.md` | Drop | 17.1.4 Drop |
| DONE | `trait/iter.md` | Iterators | 17.1.5 Iterators |
| DONE | `trait/impl_trait.md` | `impl Trait` | 17.1.6 impl Trait |
| DONE | `trait/clone.md` | Clone | 17.1.7 Clone |
| DONE | `trait/supertraits.md` | Supertraits | 17.1.8 Supertraits |
| DONE | `trait/disambiguating.md` | Disambiguating overlapping traits | 17.1.9 Disambiguating overlapping traits |
| DONE | `macros.md` | macro_rules! | 18.1 macro_rules! |
| DONE | `macros/syntax.md` | Syntax | 18.1.1 Syntax |
| DONE | `macros/designators.md` | Designators | 18.1.1.1 Designators |
| DONE | `macros/overload.md` | Overload | 18.1.1.2 Overload |
| DONE | `macros/repeat.md` | Repeat | 18.1.1.3 Repeat |
| DONE | `macros/dry.md` | DRY (Don't Repeat Yourself) | 18.1.2 DRY (Don't Repeat Yourself) |
| DONE | `macros/dsl.md` | DSL (Domain Specific Languages) | 18.1.3 DSL (Domain Specific Languages) |
| DONE | `macros/variadics.md` | Variadics | 18.1.4 Variadics |
| DONE | `error.md` | Error handling | 19.1 Error handling |
| DONE | `error/panic.md` | `panic` | 19.1.1 panic |
| DONE | `error/abort_unwind.md` | `abort` & `unwind` | 19.1.2 abort & unwind |
| DONE | `error/option_unwrap.md` | `Option` & `unwrap` | 19.1.3 Option & unwrap |
| DONE | `error/option_unwrap/question_mark.md` | Unpacking options with `?` | 19.1.3.1 Unpacking options with ? |
| DONE | `error/option_unwrap/map.md` | Combinators: `map` | 19.1.3.2 Combinators: map |
| DONE | `error/option_unwrap/and_then.md` | Combinators: `and_then` | 19.1.3.3 Combinators: and_then |
| DONE | `error/option_unwrap/defaults.md` | Defaults: `or`, `or_else`, `get_or_insert`, `get_or_insert_with` | 19.1.3.4 Defaults: or, or_else, get_or_insert, get_or_insert_with |
| DONE | `error/result.md` | `Result` | 19.1.4 Result |
| DONE | `error/result/result_map.md` | `map` for `Result` | 19.1.4.1 map for Result |
| DONE | `error/result/result_alias.md` | aliases for `Result` | 19.1.4.2 aliases for Result |
| DONE | `error/result/early_returns.md` | Early returns | 19.1.4.3 Early returns |
| DONE | `error/result/enter_question_mark.md` | Introducing `?` | 19.1.4.4 Introducing ? |
| DONE | `error/multiple_error_types.md` | Multiple error types | 19.1.5 Multiple error types |
| DONE | `error/multiple_error_types/option_result.md` | Pulling `Result`s out of `Option`s | 19.1.5.1 Pulling Results out of Options |
| DONE | `error/multiple_error_types/define_error_type.md` | Defining an error type | 19.1.5.2 Defining an error type |
| DONE | `error/multiple_error_types/boxing_errors.md` | `Box`ing errors | 19.1.5.3 Boxing errors |
| DONE | `error/multiple_error_types/reenter_question_mark.md` | Other uses of `?` | 19.1.5.4 Other uses of ? |
| DONE | `error/multiple_error_types/wrap_error.md` | Wrapping errors | 19.1.5.5 Wrapping errors |
| DONE | `error/iter_result.md` | Iterating over `Result`s | 19.1.6 Iterating over Results |
| DONE | `std.md` | Std library types | 20.1 Std library types |
| DONE | `std/box.md` | Box, stack and heap | 20.1.1 Box, stack and heap |
| DONE | `std/vec.md` | Vectors | 20.1.2 Vectors |
| DONE | `std/str.md` | Strings | 20.1.3 Strings |
| DONE | `std/option.md` | `Option` | 20.1.4 Option |
| DONE | `std/result.md` | `Result` | 20.1.5 Result |
| DONE | `std/result/question_mark.md` | `?` | 20.1.5.1 ? |
| DONE | `std/panic.md` | `panic!` | 20.1.6 panic! |
| DONE | `std/hash.md` | HashMap | 20.1.7 HashMap |
| DONE | `std/hash/alt_key_types.md` | Alternate/custom key types | 20.1.7.1 Alternate/custom key types |
| DONE | `std/hash/hashset.md` | HashSet | 20.1.7.2 HashSet |
| DONE | `std/rc.md` | `Rc` | 20.1.8 Rc |
| DONE | `std/arc.md` | `Arc` | 20.1.9 Arc |
| DONE | `std_misc.md` | Std misc | 21.1 Std misc |
| DONE | `std_misc/threads.md` | Threads | 21.1.1 Threads |
| DONE | `std_misc/threads/testcase_mapreduce.md` | Testcase: map-reduce | 21.1.1.1 Testcase: map-reduce |
| DONE | `std_misc/channels.md` | Channels | 21.1.2 Channels |
| DONE | `std_misc/path.md` | Path | 21.1.3 Path |
| DONE | `std_misc/file.md` | File I/O | 21.1.4 File I/O |
| DONE | `std_misc/file/open.md` | `open` | 21.1.4.1 open |
| DONE | `std_misc/file/create.md` | `create` | 21.1.4.2 create |
| DONE | `std_misc/file/read_lines.md` | `read_lines` | 21.1.4.3 read_lines |
| DONE | `std_misc/process.md` | Child processes | 21.1.5 Child processes |
| DONE | `std_misc/process/pipe.md` | Pipes | 21.1.5.1 Pipes |
| DONE | `std_misc/process/wait.md` | Wait | 21.1.5.2 Wait |
| DONE | `std_misc/fs.md` | Filesystem Operations | 21.1.6 Filesystem Operations |
| DONE | `std_misc/arg.md` | Program arguments | 21.1.7 Program arguments |
| DONE | `std_misc/arg/matching.md` | Argument parsing | 21.1.7.1 Argument parsing |
| DONE | `std_misc/ffi.md` | Foreign Function Interface | 21.1.8 Foreign Function Interface |
| DONE | `testing.md` | Testing | 22.1 Testing |
| DONE | `testing/unit_testing.md` | Unit testing | 22.1.1 Unit testing |
| DONE | `testing/doc_testing.md` | Documentation testing | 22.1.2 Documentation testing |
| DONE | `testing/integration_testing.md` | Integration testing | 22.1.3 Integration testing |
| DONE | `testing/dev_dependencies.md` | Dev-dependencies | 22.1.4 Dev-dependencies |
| DONE | `unsafe.md` | Unsafe Operations | 23.1 Unsafe Operations |
| DONE | `unsafe/asm.md` | Inline assembly | 23.1.1 Inline assembly |
| DONE | `compatibility.md` | Compatibility | 24.1 Compatibility |
| DONE | `compatibility/raw_identifiers.md` | Raw identifiers | 24.1.1 Raw identifiers |
| DONE | `meta.md` | Meta | 24.1.2 Meta |
| DONE | `meta/doc.md` | Documentation | 24.1.2.1 Documentation |
| DONE | `meta/playground.md` | Playground | 24.1.2.2 Playground |

Generated for full nested coverage.
