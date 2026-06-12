# Builder Track Weekly Report — Week 24

__Name:__ Victor Okenwa.
__Week Ending:__ Friday 12th June, 2026


## Rust Variables, Shadowing, Constants, Type Inference and Scopes.

During the week I had to go through one of the most basic parts of Rust which is ho variables work in rust.

To define a variable the `let` keyword is used for defining a variable.

```rs
fn main() {
    let x = 5;
    println!("The value of x is {}", x);
}
```

In Rust a `let` variable is not mutable by default. To make a `let` variable to be mutable the `mut` keyword is used. For instance:

```rs
fn main() {
    let x = 5;
    println!("The value of x is {}", x);

    x = 10;
    println!("The value of x is {}", x);
}
```

The code above will produce this error:

```bash
cargo run
   Compiling variables-shadowing-constants v0.1.0 (/home/morse-code/projects/rust-hello-world/variables-shadowing-constants)
error[E0384]: cannot assign twice to immutable variable `x`
 --> src/main.rs:5:5
  |
2 |     let x = 5;
  |         - first assignment to `x`
...
5 |     x = 10;
  |     ^^^^^^ cannot assign twice to immutable variable
  |
help: consider making this binding mutable
  |
2 |     let mut x = 5;
  |         +++

For more information about this error, try `rustc --explain E0384`.
error: could not compile `variables-shadowing-constants` (bin "variables-shadowing-constants") due to 1 previous error
```

to solve this issue

```rs
fn main() {
    let mut x = 5;
    println!("The value of x is {}", x);

    x = 10;
    println!("The value of x is {}", x);
}
```
This is the output.

```bash
morse-code@morse-code:~/projects/rust-hello-world/variables-shadowing-constants$ cargo run
   Compiling variables-shadowing-constants v0.1.0 (/home/morse-code/projects/rust-hello-world/variables-shadowing-constants)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.30s
     Running `target/debug/variables-shadowing-constants`
The value of x is 5
The value of x is 10
```

### Shadowing

The concept of shadowning in Rust is noe that I haven't seen so many languages posses. It is about having the same variable name within the same scope.

__For example__

```rs
let x= 50;
println!("The value of x is {}", x);

let  x= 90;
println!("The value of x is {}", x);
```

__The output wil be 50 and 90 because we defined a new variable with the same name which means that the varible `x` get new value and subsequently any time we call `x`, x will always be 90 instead of 50 as before__

We can also perform an action where we call the same variable name within the new initilization:

```rs
let x= 50;
println!("The value of x is {}", x);

let  x= x + 90;
println!("The value of x is {}", x);
```

__The output will be 140 (50 + 90)__

### Constants

This is a concept of `Rust` that interested me the most because I asked a question __Why do we need  constants in Rust when `let` variables are immutable by default?__

I notice some changes between them

| Feature              | `let`                                        | `const`                                            |
|----------------------|----------------------------------------------|----------------------------------------------------|
| Mutability           | Immutable by default (can be made mutable)   | Always immutable                                   |
| Value assignment     | Value can be computed at runtime             | Must be assigned a constant expression at compile time |
| Syntax               | `let x = 5;` / `let mut x = 5;`              | `const X: i32 = 5;`                                |
| Type annotation      | Optional (unless using `mut`)                | Mandatory                                          |
| Scope                | Block scope (like a variable)                | Static/global scope (lives for the entire program) |
| Shadowing            | Allowed (`let x = x + 1;`)                   | Not allowed                                        |
| Example modification | `x = 10; // if mutable`                      | Cannot be reassigned                               |
| Naming convention    | Any valid identifier (e.g., `x`, `foo`)      | Uppercase with underscores (e.g., `MAX_POINTS`)    |

__Summary__:  
- Use `let` for variables whose values may be computed at runtime or will change.
- Use `const` for compile-time constants whose values never change throughout the program.

### Type inference

Rust provides a powerful feature called **type inference**, which means the compiler can automatically figure out the type of a variable based on the value it is assigned. This allows you to write less verbose and more readable code, while still benefiting from Rust's strict static type checking.

**How Type Inference Works**

When you declare a variable using `let`, you often do **not** have to specify its type explicitly:

```rs
let x = 42;
```

The compiler looks at the value on the right-hand side and tries to deduce the most appropriate type for the variable. If you're using a value for which the type isn't obvious or could be multiple things (such as collections or function return values), you can still provide an explicit type:

```rs
let guess: i32 = 40;
```

### Summary

- Rust's type inference saves you from writing many explicit types while maintaining safety and clarity.
- If the compiler cannot infer the type, you must provide an explicit annotation.
- Type inference does not mean Rust is dynamically typed—types are always checked at compile-time.

This balance of flexibility and safety is a key reason why Rust is both productive and reliable for developers.


### Scopes

This is one of the features of `Rust` that interested me and I have found it perculier to `Rust` only where within a function we can add a scope by just using curly braces `{}`.

For example:

```rust 
fn main() {
    let x = 10;
    println!("Outer scope: x = {}", x);

    {
        let x = 20;
        println!("Inner scope: x = {}", x);
    }

    println!("Back to outer scope: x = {}", x);
}
```

__I also learnt abot how shadowing works with scopes__

Here is an example demonstrating **shadowing** with scopes in Rust:

```rust
fn main() {
    let value = 5;
    println!("Outer scope: value = {}", value);

    {
        // Shadowing 'value' in the inner scope
        let value = value + 10;
        println!("Inner scope: value = {}", value);
    }

    // Back to the outer scope, original 'value' is unchanged
    println!("Back to outer scope: value = {}", value);
}
```

**Output:**
```
Outer scope: value = 5
Inner scope: value = 15
Back to outer scope: value = 5
```

In this example:
- The variable `value` is first assigned `5` in the outer scope.
- Within the inner scope (curly braces), `let value = value + 10;` _shadows_ the outer `value`, resulting in `15`.
- After exiting the inner scope, the original `value` remains unchanged (`5`).