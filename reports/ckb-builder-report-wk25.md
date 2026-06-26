# Builder Track Weekly Report — Week 25

__Name:__ Victor Okenwa.
__Week Ending:__ Friday 19th June, 2026

## Overview: Rust Data Structures

This week, I learned that Rust has three main types of data structures:

1. **Scalar Types**
2. **Compound Types**
3. **Custom Types**

Below are examples and outputs for each:

---

### 1. Scalar Types

Scalar types represent a single value. The main scalar types in Rust are integers, floating-point numbers, Booleans, and characters.

**Example:**
```rust
fn main() {
    let int_val: i32 = 10;
    let float_val: f64 = 3.14;
    let is_rust_fun: bool = true;
    let character: char = 'R';

    println!("int_val: {}", int_val);
    println!("float_val: {}", float_val);
    println!("is_rust_fun: {}", is_rust_fun);
    println!("character: {}", character);
}
```
**Output:**
```
int_val: 10
float_val: 3.14
is_rust_fun: true
character: R
```

---

### 2. Compound Types

Compound types can group multiple values into one type. The two most common are tuples and arrays.

**Example (Tuple and Array):**
```rust
fn main() {
    let tup: (i32, f64, u8) = (500, 6.4, 1);
    let arr: [i32; 3] = [1, 2, 3];

    println!("Tuple first value: {}", tup.0);
    println!("Array second value: {}", arr[1]);
}
```
**Output:**
```
Tuple first value: 500
Array second value: 2
```

---

### 3. Custom Types

Custom types allow us to create our own data structures using `struct`, `enum`, or `union`.

**Example (Struct):**
```rust
struct Person {
    name: String,
    age: u8,
}

fn main() {
    let person = Person {
        name: String::from("Alice"),
        age: 30,
    };

    println!("Name: {}, Age: {}", person.name, person.age);
}
```
**Output:**
```
Name: Alice, Age: 30
```

---

These examples helped me understand the foundation of data structures in Rust and how to use them in practical ways.

