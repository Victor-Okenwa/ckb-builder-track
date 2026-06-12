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

__The output wil be 50 and 90 because we defined a new variable with the same name which means that the varible `x` get new value and subsequently any time we call `x`, x will always be 90 intead of 50 as before__

We can also perform an action where we call the same variable name within the new initilization:

```rs
let x= 50;
println!("The value of x is {}", x);

let  x= x + 90;
println!("The value of x is {}", x);
```

__The output will be 140 (50 + 90)__