# Builder Track Weekly Report — Week 26

__Name:__ Victor Okenwa.
__Week Ending:__ Friday 26th June, 2026

## Overview: Rust function, types and return procedures


### Functions, Arguments vs Parameters — What's the Difference?

This week, I really dived into functions in Rust, and, honestly, I was a bit confused at first about the terms "arguments" and "parameters." Aren't they just the same thing? Turns out, they're related, but not exactly the same!

Here's what I figured out. The table helped clarify it for me:

| Term        | Where Defined / Used                | Example in Rust                   | Description                                                                            |
|-------------|------------------------------------|-----------------------------------|----------------------------------------------------------------------------------------|
| Parameter   | In function definition              | `fn double(x: i32)`              | The variable name used in the function declaration; it's like a placeholder.            |
| Argument    | When calling the function           | `double(5)`                      | The actual value you pass in when calling—that value fills the parameter's place.       |

#### Example:

```rust
fn double(number: i32) -> i32 { // 'number' is a parameter
    number * 2
}

fn main() {
    let result = double(5); // '5' is the argument
    println!("Result: {}", result);
}
```
**Output:**
```
Result: 10
```

---

### Statements vs Expressions

Rust makes a distinction between *statements* and *expressions.* I was curious—what's the difference? Here's what I found:

| Type        | Example                      | Returns a Value?   |
|-------------|-----------------------------|--------------------|
| Statement   | `let x = 6;`                | No                 |
| Expression  | `6 + 2`, `{ x + 1 }`        | Yes                |

- **Statement:** Does something, but *doesn't* return a value.
- **Expression:** *Evaluates* to a value.

#### Example — Expression In a Scope

I learnt you can assign the result of a block (an expression) to a variable! Whoa.

```rust
fn main() {
    let y = {
        let x = 50;
        x + 30 // this is an expression, so its value gets returned
    };
    println!("The value of y: {}", y);
}
```
**Output:**
```
The value of y: 80
```

---

### How Returns Work (& When To Use the `return` Keyword)

Rust functions *always* return the last expression (if `return` keyword is omitted and no semicolon is used). But if you use a semicolon, it's a statement, not an expression—and nothing is returned! Also, you can always use `return` to exit early or be explicit.

#### Example: Implicit Return (No `return` keyword, no semicolon)

```rust
fn add(a: i32, b: i32) -> i32 {
    a + b // expression, returned
}
fn main() {
    let sum = add(2, 3);
    println!("Sum: {}", sum);
}
```
**Output:**
```
Sum: 5
```

#### Example: Explicit Return

```rust
fn multiply(a: i32, b: i32) -> i32 {
    return a * b; // You *must* use semicolon with `return`
}
fn main() {
    let product = multiply(4, 6);
    println!("Product: {}", product);
}
```
**Output:**
```
Product: 24
```

#### Example: What if I use a Semicolon by Mistake?

```rust
fn minus_one(n: i32) -> i32 {
    n - 1; // Statement! Returns ()
}
fn main() {
    let value = minus_one(9);
    println!("Value: {:?}", value);
}
```
**Output:**
```
Value: ()
```
*That one shocked me! If you want to return a value automatically, DO NOT use a semicolon.*

---

### Returning Multiple Values — Tuple Returns!

If you want to return more than one value, you can use a tuple. This felt pretty neat.

```rust
fn split_sum(a: i32, b: i32) -> (i32, i32) {
    (a + b, a * b)
}

fn main() {
    let (sum, product) = split_sum(3, 7);
    println!("Sum: {}, Product: {}", sum, product);
}
```
**Output:**
```
Sum: 10, Product: 21
```

#### You can also print a tuple directly using `{:?}`:

```rust
fn main() {
    let values = split_sum(3, 7);
    println!("Tuple output: {:?}", values); // {:?} pretty prints the tuple
}
```
**Output:**
```
Tuple output: (10, 21)
```

---

### My Aha Moments / Extra Questions

- If I forget to return a value at the end, or just use a semicolon, Rust returns `()`, which is called the "unit" type. Kinda like `void` except... it's still a value!
- You only *need* the `return` keyword if you want to break out early, or be explicit. But idiomatic Rust often omits it for the last line!

---

Overall, I’m starting to see that functions and returns in Rust have a lot of tiny rules, but once you get the hang of where expressions vs statements are, it feels logical. Now, I keep asking: “Do I need a semicolon here?” "Will this line return the value?" It's fun to reason about!

