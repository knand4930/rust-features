# 🦀 Complete Rust Language Reference — Basic to Extremely Advanced

> Every keyword, feature, pattern, and concept in Rust — from `let` to zero-cost abstractions — with purpose, real-world usage, and code examples at every level. Covers ownership, borrowing, lifetimes, traits, generics, async, unsafe, macros, concurrency, FFI, type system internals, and more.

---

## Table of Contents

1. [Primitive Types & Literals](#1-primitive-types--literals)
2. [Variable Bindings & Shadowing](#2-variable-bindings--shadowing)
3. [Functions & Closures](#3-functions--closures)
4. [Control Flow](#4-control-flow)
5. [Pattern Matching (Deep Dive)](#5-pattern-matching-deep-dive)
6. [Ownership, Borrowing & References](#6-ownership-borrowing--references)
7. [Structs](#7-structs)
8. [Enums & Algebraic Data Types](#8-enums--algebraic-data-types)
9. [Traits — Basic to Advanced](#9-traits--basic-to-advanced)
10. [Generics & Monomorphisation](#10-generics--monomorphisation)
11. [Lifetimes — Basic to Advanced](#11-lifetimes--basic-to-advanced)
12. [Error Handling](#12-error-handling)
13. [Collections](#13-collections)
14. [Iterators (Deep Dive)](#14-iterators-deep-dive)
15. [Modules, Crates & Workspaces](#15-modules-crates--workspaces)
16. [Smart Pointers](#16-smart-pointers)
17. [Concurrency & Parallelism](#17-concurrency--parallelism)
18. [Async / Await (Deep Dive)](#18-async--await-deep-dive)
19. [Macros — Declarative & Procedural](#19-macros--declarative--procedural)
20. [Unsafe Rust](#20-unsafe-rust)
21. [FFI — Foreign Function Interface](#21-ffi--foreign-function-interface)
22. [Type System Advanced Features](#22-type-system-advanced-features)
23. [Operator Overloading](#23-operator-overloading)
24. [String Types](#24-string-types)
25. [Slices & Arrays](#25-slices--arrays)
26. [Closures (Advanced)](#26-closures-advanced)
27. [Trait Objects vs Generics](#27-trait-objects-vs-generics)
28. [Advanced Lifetime Patterns](#28-advanced-lifetime-patterns)
29. [Memory Layout & Repr](#29-memory-layout--repr)
30. [Build System & Cargo](#30-build-system--cargo)
31. [Testing](#31-testing)
32. [Common Standard Library Traits](#32-common-standard-library-traits)
33. [Interior Mutability Patterns](#33-interior-mutability-patterns)
34. [Zero-Cost Abstractions](#34-zero-cost-abstractions)
35. [Newtype Pattern & Type Safety](#35-newtype-pattern--type-safety)
36. [State Machines in Rust](#36-state-machines-in-rust)
37. [Builder Pattern](#37-builder-pattern)
38. [Compile-Time Programming (const fn, const generics)](#38-compile-time-programming)
39. [SIMD & Intrinsics](#39-simd--intrinsics)
40. [Quick Reference Cheat Sheet](#40-quick-reference-cheat-sheet)

---

## 1. Primitive Types & Literals

### Integer Types

| Type | Size | Range |
|------|------|-------|
| `i8` | 1 byte | -128 to 127 |
| `i16` | 2 bytes | -32,768 to 32,767 |
| `i32` | 4 bytes | -2.1B to 2.1B (default) |
| `i64` | 8 bytes | -9.2E18 to 9.2E18 |
| `i128` | 16 bytes | Very large |
| `isize` | arch | Pointer-sized signed |
| `u8` | 1 byte | 0 to 255 |
| `u16` | 2 bytes | 0 to 65,535 |
| `u32` | 4 bytes | 0 to 4.3B |
| `u64` | 8 bytes | 0 to 1.8E19 |
| `u128` | 16 bytes | Very large |
| `usize` | arch | Pointer-sized unsigned (array index) |

```rust
fn main() {
    // Integer literals
    let decimal    = 1_000_000;     // underscores for readability
    let hex        = 0xFF_AA_BB;
    let octal      = 0o755;
    let binary     = 0b1010_0011;
    let byte       = b'A';          // u8 only

    // Explicit type suffix
    let x: i32   = -42i32;
    let big: u64 = 18_446_744_073_709_551_615u64;

    // Overflow: in debug mode panics; in release wraps
    // Use checked/saturating/wrapping explicitly:
    let a: u8 = 250;
    println!("{:?}", a.checked_add(10));     // None
    println!("{}", a.saturating_add(10));    // 255
    println!("{}", a.wrapping_add(10));      // 4

    // Casting
    let f: f64 = 3.99;
    let i = f as i32;   // truncates: 3
    let u = 300i32 as u8; // truncates: 44

    println!("{decimal} {hex} {i} {u}");
}
```

---

### Floating Point

```rust
fn main() {
    let x: f32 = 3.14;
    let y: f64 = 2.718281828; // f64 is the default

    // Special values
    let inf  = f64::INFINITY;
    let nan  = f64::NAN;
    println!("{}", nan.is_nan());    // true
    println!("{}", inf.is_finite()); // false

    // Math operations
    let sqrt = 2.0f64.sqrt();
    let pow  = 2.0f64.powi(10);   // integer exponent
    let powf = 2.0f64.powf(0.5);  // float exponent
    println!("{sqrt:.4} {pow} {powf:.4}"); // 1.4142 1024 1.4142

    // Comparison gotcha — never use == on floats
    let a = 0.1 + 0.2;
    let b = 0.3;
    println!("{}", (a - b).abs() < f64::EPSILON); // true (correct way)
}
```

---

### Boolean, Character, Unit

```rust
fn main() {
    // bool
    let t: bool = true;
    let f: bool = false;
    println!("{}", t && f); // false
    println!("{}", t as u8); // 1

    // char — Unicode scalar value (4 bytes)
    let c: char = 'z';
    let emoji: char = '🦀';
    let cjk: char = '中';
    println!("{} {} {}", c, emoji, cjk);
    println!("{}", emoji.len_utf8()); // 4 bytes in UTF-8

    // unit type () — "nothing"
    let unit: () = ();
    fn returns_nothing() -> () { println!("hi"); }
}
```

---

### Tuples

```rust
fn main() {
    let t: (i32, f64, &str) = (42, 3.14, "hello");

    // Access by index
    println!("{} {} {}", t.0, t.1, t.2);

    // Destructuring
    let (x, y, z) = t;
    println!("{x} {y} {z}");

    // Nested tuples
    let nested = ((1, 2), (3, 4));
    println!("{}", (nested.0).1); // 2

    // Returning multiple values
    fn min_max(v: &[i32]) -> (i32, i32) {
        (*v.iter().min().unwrap(), *v.iter().max().unwrap())
    }
    let (lo, hi) = min_max(&[3, 1, 4, 1, 5, 9]);
    println!("{lo} {hi}"); // 1 9
}
```

---

## 2. Variable Bindings & Shadowing

### `let`, `mut`, and Shadowing

```rust
fn main() {
    // Immutable by default
    let x = 5;
    // x = 6; // compile error

    // Mutable
    let mut y = 10;
    y += 5;

    // Shadowing — same name, new binding (can change type!)
    let spaces = "   ";           // &str
    let spaces = spaces.len();    // usize — type changed!
    println!("{spaces}"); // 3

    // Shadowing in inner scope
    let z = 1;
    {
        let z = z * 2; // inner shadow
        println!("{z}"); // 2
    }
    println!("{z}"); // 1 — outer z unchanged

    // Pattern destructuring in let
    let (a, b, c) = (1, 2, 3);
    let [first, .., last] = [10, 20, 30, 40];
    println!("{a} {b} {c} | {first} {last}"); // 1 2 3 | 10 40
}
```

---

### `const` and `static`

```rust
// const — compile-time constant, always inlined, no fixed address
const MAX_POINTS: u32 = 100_000;
const PI: f64 = std::f64::consts::PI;

// static — fixed memory address, lives for program duration
static GREETING: &str = "Hello, World!";
static COUNTER: std::sync::atomic::AtomicUsize =
    std::sync::atomic::AtomicUsize::new(0);

// const fn — evaluated at compile time
const fn square(n: u32) -> u32 { n * n }
const NINE: u32 = square(3); // computed at compile time

fn main() {
    println!("{MAX_POINTS} {PI:.4}");
    println!("{GREETING}");
    println!("9 = {NINE}");

    // static mut — requires unsafe
    static mut COUNT: i32 = 0;
    unsafe {
        COUNT += 1;
        println!("count: {COUNT}");
    }
}
```

---

## 3. Functions & Closures

### Function Basics

```rust
// Basic function
fn add(a: i32, b: i32) -> i32 { a + b }

// Multiple return values via tuple
fn divmod(a: i32, b: i32) -> (i32, i32) { (a / b, a % b) }

// Variadic-style with slices
fn sum(nums: &[i32]) -> i32 { nums.iter().sum() }

// Function pointers
fn apply(f: fn(i32) -> i32, x: i32) -> i32 { f(x) }

fn double(x: i32) -> i32 { x * 2 }

// Diverging functions — never return (return type !)
fn panic_always(msg: &str) -> ! {
    panic!("{msg}");
}

fn main() {
    println!("{}", add(3, 4));
    println!("{:?}", divmod(10, 3)); // (3, 1)
    println!("{}", sum(&[1, 2, 3, 4, 5]));
    println!("{}", apply(double, 7)); // 14
}
```

---

### Closures

```rust
fn main() {
    // Closure — anonymous function capturing environment
    let x = 5;
    let add_x = |n| n + x;       // captures x by reference
    println!("{}", add_x(3));    // 8

    // Explicit types
    let multiply = |a: i32, b: i32| -> i32 { a * b };

    // move closure — takes ownership of captured vars
    let s = String::from("hello");
    let owns_s = move || println!("{s}");
    owns_s();
    // s is moved — can't use it here

    // Closures as arguments
    let nums = vec![1, 2, 3, 4, 5];
    let evens: Vec<_> = nums.iter().filter(|&&n| n % 2 == 0).collect();
    println!("{evens:?}"); // [2, 4]

    // Returning closures from functions
    fn make_multiplier(factor: i32) -> impl Fn(i32) -> i32 {
        move |x| x * factor
    }
    let triple = make_multiplier(3);
    println!("{}", triple(7)); // 21
}
```

---

### `Fn`, `FnMut`, `FnOnce` Traits

```rust
// Fn      — borrows environment immutably, can call many times
// FnMut   — borrows environment mutably, can call many times
// FnOnce  — consumes environment, can call only once

fn call_fn<F: Fn()>(f: F) { f(); f(); }           // called twice
fn call_fn_mut<F: FnMut()>(mut f: F) { f(); f(); } // called twice, mutates
fn call_once<F: FnOnce()>(f: F) { f(); }           // called once

fn main() {
    let s = "hello".to_string();

    // Fn — borrows
    call_fn(|| println!("{s}"));

    // FnMut — mutates captured variable
    let mut count = 0;
    call_fn_mut(|| { count += 1; println!("{count}"); });
    println!("final count: {count}"); // 2

    // FnOnce — consumes
    let owned = String::from("owned");
    call_once(move || { drop(owned); println!("dropped"); });

    // Boxed closures for dynamic dispatch
    let ops: Vec<Box<dyn Fn(i32) -> i32>> = vec![
        Box::new(|x| x + 1),
        Box::new(|x| x * 2),
        Box::new(|x| x - 3),
    ];
    let result = ops.iter().fold(10, |acc, f| f(acc));
    println!("{result}"); // ((10+1)*2)-3 = 19
}
```

---

## 4. Control Flow

### `if`, `else if`, `else`

```rust
fn classify(n: i32) -> &'static str {
    if n < 0       { "negative" }
    else if n == 0 { "zero" }
    else if n < 10 { "small" }
    else           { "large" }
}

fn main() {
    // if as expression (replaces ternary)
    let abs = if -5 < 0 { 5 } else { -5 };

    // if in let
    let description = if true { "yes" } else { "no" };

    // Inline conditional (arms must have same type)
    for i in -2..=12 {
        print!("{} ", classify(i));
    }
}
```

---

### `match` — All Pattern Types

```rust
fn main() {
    let n: i32 = 42;

    // Literal patterns
    let msg = match n {
        1        => "one",
        2 | 3    => "two or three",
        4..=10   => "four to ten",
        11..=99  => "teens/tens",
        _        => "other",
    };

    // Destructuring tuple
    let point = (0, -3);
    match point {
        (0, 0)       => println!("origin"),
        (x, 0) | (0, x) => println!("on axis at {x}"),
        (x, y)       => println!("({x}, {y})"),
    }

    // Match guard (if condition after pattern)
    let pair = (2, -2);
    match pair {
        (x, y) if x == y  => println!("equal"),
        (x, y) if x + y == 0 => println!("sum zero"),
        (x, y) => println!("{x} {y}"),
    }

    // Binding with @ (and-pattern)
    let age = 25u32;
    match age {
        n @ 0..=17  => println!("minor, age {n}"),
        n @ 18..=64 => println!("adult, age {n}"),
        n           => println!("senior, age {n}"),
    }

    // Nested enum destructuring
    #[derive(Debug)]
    enum Message {
        Quit,
        Move { x: i32, y: i32 },
        Write(String),
        Color(u8, u8, u8),
    }

    let msg = Message::Move { x: 10, y: 20 };
    if let Message::Move { x, y } = msg {
        println!("Move to {x},{y}");
    }

    // matches! macro
    let v = vec![1, 2, 3];
    let has_big = v.iter().any(|&x| matches!(x, 10..=100));
    println!("{has_big}"); // false
}
```

---

### Loops

```rust
fn main() {
    // loop — infinite, can return value
    let mut i = 0;
    let result = loop {
        i += 1;
        if i == 10 { break i * i; }
    };
    println!("{result}"); // 100

    // while
    let mut n = 256u32;
    while n > 1 { n /= 2; }

    // while let
    let mut stack = vec![1, 2, 3];
    while let Some(top) = stack.pop() {
        print!("{top} ");
    }
    println!();

    // for with ranges
    for i in 0..5 { print!("{i} "); }   // 0 1 2 3 4
    println!();
    for i in 0..=5 { print!("{i} "); }  // 0 1 2 3 4 5
    println!();

    // for with rev, step
    for i in (0..10).rev().step_by(2) {
        print!("{i} "); // 8 6 4 2 0
    }
    println!();

    // Nested loops with labels
    'outer: for x in 0..5 {
        for y in 0..5 {
            if x * y > 6 { break 'outer; }
            print!("({x},{y}) ");
        }
    }
    println!();

    // continue with label
    'outer2: for i in 0..3 {
        for j in 0..3 {
            if j == 1 { continue 'outer2; }
            print!("[{i},{j}] ");
        }
    }
}
```

---

## 5. Pattern Matching (Deep Dive)

### Patterns Everywhere

```rust
fn main() {
    // Destructuring in let
    let (a, b, c) = (1, 2, 3);
    let Point { x, y } = Point { x: 10, y: 20 };

    // Struct field shorthand in match
    struct Rectangle { width: u32, height: u32 }
    let rect = Rectangle { width: 30, height: 50 };
    let Rectangle { width, height } = rect;

    // Slice patterns
    let v = vec![1, 2, 3, 4, 5];
    match v.as_slice() {
        []              => println!("empty"),
        [x]             => println!("one: {x}"),
        [first, .., last] => println!("first {first}, last {last}"),
    }

    // Ignoring values with _
    let (_, second, _) = (1, 2, 3);
    struct Point3D { x: i32, y: i32, z: i32 }
    let p = Point3D { x: 1, y: 2, z: 3 };
    let Point3D { x, .. } = p; // ignore y and z

    // Ref patterns in match
    let values = vec![String::from("a"), String::from("b")];
    for val in &values {
        match val.as_str() {
            "a" => println!("found a"),
            s   => println!("other: {s}"),
        }
    }
    // values still valid here — not moved

    // Or-patterns
    let c = 'A';
    if let 'a'..='z' | 'A'..='Z' = c {
        println!("letter");
    }
}

struct Point { x: i32, y: i32 }
```

---

## 6. Ownership, Borrowing & References

### Ownership Rules

```
1. Every value has exactly ONE owner.
2. When the owner goes out of scope, the value is DROPPED (memory freed).
3. Ownership can be MOVED to a new variable (invalidating the old).
4. Types implementing Copy are duplicated instead of moved.
```

```rust
fn main() {
    // Move semantics
    let s1 = String::from("hello");
    let s2 = s1;               // s1 MOVED to s2
    // println!("{s1}");        // ERROR: s1 invalid after move

    // Clone — explicit deep copy
    let s3 = String::from("world");
    let s4 = s3.clone();       // deep copy — both valid
    println!("{s3} {s4}");

    // Copy — stack-only types are copied, not moved
    let x = 5;
    let y = x;                 // x COPIED (i32 is Copy)
    println!("{x} {y}");       // both valid

    // Ownership in functions
    fn takes_ownership(s: String) { println!("{s}"); } // s dropped here
    fn makes_copy(n: i32) { println!("{n}"); }         // copy, original valid

    let s = String::from("hello");
    takes_ownership(s);        // s moved into function
    // println!("{s}");         // ERROR

    let n = 5;
    makes_copy(n);
    println!("{n}");            // fine — copy

    // Return ownership
    fn give_ownership() -> String { String::from("new") }
    fn take_and_give(s: String) -> String { s }

    let s5 = give_ownership();        // ownership moved out
    let s6 = take_and_give(s5);       // s5 moved in, moved back out
    println!("{s6}");
}
```

---

### References & Borrowing

```rust
fn main() {
    let s = String::from("hello");

    // Immutable reference — borrow without taking ownership
    let len = calculate_len(&s);
    println!("{s} has {len} chars"); // s still valid

    // Multiple immutable references allowed simultaneously
    let r1 = &s;
    let r2 = &s;
    println!("{r1} {r2}"); // ok

    // Mutable reference — only ONE at a time
    let mut s2 = String::from("hello");
    let r3 = &mut s2;
    r3.push_str(", world");
    println!("{r3}");

    // Can't have mutable + immutable at same time
    let mut s3 = String::from("hi");
    let r4 = &s3;              // immutable borrow starts
    // let r5 = &mut s3;       // ERROR: can't borrow mutably
    println!("{r4}");          // r4 used here — borrow ends
    let r5 = &mut s3;          // now ok — r4 no longer in use
    r5.push('!');

    // Dangling references caught by compiler
    // fn dangle() -> &String {
    //     let s = String::from("hi");
    //     &s  // ERROR: s dropped at end of function
    // }
}

fn calculate_len(s: &String) -> usize { s.len() }
```

---

### Slices — A Special Kind of Reference

```rust
fn main() {
    let s = String::from("hello world");

    // String slice — reference to part of a String
    let hello = &s[0..5];
    let world = &s[6..11];
    println!("{hello} {world}");

    // Slice syntax sugar
    let whole = &s[..];
    let from_six = &s[6..];
    let to_five = &s[..5];

    // String literals are slices
    let literal: &str = "static string";

    // Array slices
    let arr = [1, 2, 3, 4, 5];
    let slice: &[i32] = &arr[1..3];
    println!("{slice:?}"); // [2, 3]

    // Mutable slices
    let mut v = vec![3, 1, 4, 1, 5];
    let ms: &mut [i32] = &mut v[..3];
    ms.sort();
    println!("{v:?}"); // [1, 3, 4, 1, 5]
}
```

---

## 7. Structs

### All Struct Forms

```rust
// Regular struct
#[derive(Debug, Clone, PartialEq)]
struct User {
    username: String,
    email: String,
    age: u32,
    active: bool,
}

// Tuple struct (newtype and named tuple)
#[derive(Debug, Clone, Copy)]
struct Point(f64, f64);

#[derive(Debug, Clone, Copy, PartialEq, PartialOrd)]
struct Metres(f64); // newtype for type safety

// Unit struct (no fields — useful for marker types)
struct AlwaysEqual;

impl User {
    // Associated function (constructor)
    fn new(username: &str, email: &str, age: u32) -> Self {
        User { username: username.into(), email: email.into(), age, active: true }
    }

    // Method
    fn is_adult(&self) -> bool { self.age >= 18 }

    // Mutable method
    fn deactivate(&mut self) { self.active = false; }

    // Consuming method
    fn into_email(self) -> String { self.email }
}

impl Point {
    fn distance(&self, other: &Point) -> f64 {
        ((self.0 - other.0).powi(2) + (self.1 - other.1).powi(2)).sqrt()
    }
}

fn main() {
    let mut u = User::new("alice", "alice@example.com", 30);
    println!("{u:?}");
    u.deactivate();

    // Struct update syntax
    let u2 = User {
        email: "bob@example.com".into(),
        username: "bob".into(),
        ..u  // remaining fields from u (age, active)
    };
    println!("{u2:?}");

    let p1 = Point(0.0, 0.0);
    let p2 = Point(3.0, 4.0);
    println!("{}", p1.distance(&p2)); // 5.0

    // Newtype prevents mixing units
    let d1 = Metres(100.0);
    let d2 = Metres(200.0);
    // let bad = d1 + d2; // must impl Add explicitly — no accidental mixing
}
```

---

## 8. Enums & Algebraic Data Types

### Enums as Sum Types

```rust
// Unit variants, tuple variants, struct variants
#[derive(Debug)]
enum IpAddress {
    V4(u8, u8, u8, u8),
    V6(String),
}

#[derive(Debug)]
enum Json {
    Null,
    Bool(bool),
    Int(i64),
    Float(f64),
    Str(String),
    Array(Vec<Json>),
    Object(std::collections::HashMap<String, Json>),
}

// Enum with methods
#[derive(Debug)]
enum Direction { North, South, East, West }

impl Direction {
    fn opposite(&self) -> Direction {
        match self {
            Direction::North => Direction::South,
            Direction::South => Direction::North,
            Direction::East  => Direction::West,
            Direction::West  => Direction::East,
        }
    }

    fn is_horizontal(&self) -> bool {
        matches!(self, Direction::East | Direction::West)
    }
}

// Option and Result are enums in std
fn divide(a: f64, b: f64) -> Option<f64> {
    if b == 0.0 { None } else { Some(a / b) }
}

fn main() {
    let ip = IpAddress::V4(192, 168, 1, 1);
    println!("{ip:?}");

    let d = Direction::North;
    println!("{:?}", d.opposite()); // South

    // Option methods
    let val = divide(10.0, 2.0);
    println!("{}", val.unwrap());              // 5
    println!("{}", val.unwrap_or(0.0));        // 5
    println!("{}", val.map(|v| v * 2.0).unwrap_or(0.0)); // 10

    let none: Option<f64> = None;
    println!("{}", none.unwrap_or_default());  // 0

    // Chaining Options
    let text = Some("42");
    let num: Option<i32> = text.and_then(|s| s.parse().ok());
    println!("{num:?}"); // Some(42)
}
```

---

## 9. Traits — Basic to Advanced

### Basic Traits

```rust
trait Summary {
    fn summarise(&self) -> String;

    // Default implementation
    fn preview(&self) -> String {
        format!("{}...", &self.summarise()[..20.min(self.summarise().len())])
    }
}

struct Article { title: String, content: String, author: String }
struct Tweet   { username: String, content: String }

impl Summary for Article {
    fn summarise(&self) -> String {
        format!("{} by {} — {}", self.title, self.author, self.content)
    }
}

impl Summary for Tweet {
    fn summarise(&self) -> String {
        format!("{}: {}", self.username, self.content)
    }
    // preview() uses default impl
}

// Trait bounds on functions
fn notify(item: &impl Summary) {                        // sugar for:
    println!("{}", item.summarise());
}
fn notify2<T: Summary>(item: &T) {                      // explicit bound
    println!("{}", item.summarise());
}
fn notify3<T: Summary + std::fmt::Debug>(item: &T) {    // multiple bounds
    println!("{item:?} — {}", item.summarise());
}
fn notify4<T>(item: &T) where T: Summary + std::fmt::Debug { // where clause
    println!("{item:?}");
}
```

---

### Trait Objects and Dynamic Dispatch

```rust
trait Draw { fn draw(&self) -> String; }

#[derive(Debug)] struct Circle  { radius: f64 }
#[derive(Debug)] struct Square  { side: f64 }
#[derive(Debug)] struct Triangle { base: f64, height: f64 }

impl Draw for Circle   { fn draw(&self) -> String { format!("○ r={}", self.radius) } }
impl Draw for Square   { fn draw(&self) -> String { format!("□ s={}", self.side) } }
impl Draw for Triangle { fn draw(&self) -> String { format!("△ b={} h={}", self.base, self.height) } }

struct Screen {
    components: Vec<Box<dyn Draw>>,
}

impl Screen {
    fn render(&self) {
        for comp in &self.components {
            println!("{}", comp.draw());
        }
    }
}

fn main() {
    let screen = Screen {
        components: vec![
            Box::new(Circle { radius: 3.0 }),
            Box::new(Square { side: 4.0 }),
            Box::new(Triangle { base: 5.0, height: 6.0 }),
        ],
    };
    screen.render();
}
```

---

### Trait Inheritance and Supertraits

```rust
use std::fmt;

// Supertrait: OutlinePrint requires Display
trait OutlinePrint: fmt::Display {
    fn outline_print(&self) {
        let output = self.to_string();
        let len = output.len();
        println!("{}", "*".repeat(len + 4));
        println!("* {output} *");
        println!("{}", "*".repeat(len + 4));
    }
}

struct Point { x: f64, y: f64 }

impl fmt::Display for Point {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        write!(f, "({}, {})", self.x, self.y)
    }
}

impl OutlinePrint for Point {}  // Display satisfied above

fn main() {
    Point { x: 1.0, y: 3.0 }.outline_print();
}
```

---

### Associated Types in Traits

```rust
// Associated types vs generics:
// Use associated types when a trait has ONE obvious output type per implementor.
// Use generics when a type can implement the trait for MANY types.

trait Transformer {
    type Input;
    type Output;
    fn transform(&self, input: Self::Input) -> Self::Output;
}

struct Doubler;
struct Stringifier;

impl Transformer for Doubler {
    type Input = i32;
    type Output = i32;
    fn transform(&self, input: i32) -> i32 { input * 2 }
}

impl Transformer for Stringifier {
    type Input = i32;
    type Output = String;
    fn transform(&self, input: i32) -> String { format!("value={input}") }
}

// Iterator uses associated type:
// trait Iterator { type Item; fn next(&mut self) -> Option<Self::Item>; }

fn main() {
    let d = Doubler;
    let s = Stringifier;
    println!("{}", d.transform(5));   // 10
    println!("{}", s.transform(5));   // value=5
}
```

---

### Blanket Implementations

```rust
use std::fmt;

// Implement a trait for ANY type satisfying some bound
trait Printable: fmt::Display {
    fn print(&self) { println!("{self}"); }
    fn print_n(&self, n: usize) {
        for _ in 0..n { self.print(); }
    }
}

// Blanket impl: every Display type gets Printable for free
impl<T: fmt::Display> Printable for T {}

fn main() {
    42.print();           // works — i32 is Display
    "hello".print();      // works — &str is Display
    3.14f64.print_n(3);   // works
}
```

---

## 10. Generics & Monomorphisation

### Generic Functions, Structs, Enums

```rust
// Generic function
fn largest<T: PartialOrd>(list: &[T]) -> &T {
    let mut largest = &list[0];
    for item in list {
        if item > largest { largest = item; }
    }
    largest
}

// Generic struct
#[derive(Debug)]
struct Stack<T> {
    items: Vec<T>,
}

impl<T> Stack<T> {
    fn new() -> Self { Stack { items: Vec::new() } }
    fn push(&mut self, item: T) { self.items.push(item); }
    fn pop(&mut self) -> Option<T> { self.items.pop() }
    fn peek(&self) -> Option<&T> { self.items.last() }
    fn is_empty(&self) -> bool { self.items.is_empty() }
}

// Only implement certain methods when T has required traits
impl<T: fmt::Display> Stack<T> {
    fn print_top(&self) {
        match self.peek() {
            Some(v) => println!("Top: {v}"),
            None    => println!("Empty stack"),
        }
    }
}

use std::fmt;

// Generic enum (like Option and Result in std)
#[derive(Debug)]
enum Either<L, R> {
    Left(L),
    Right(R),
}

fn main() {
    println!("{}", largest(&[3, 1, 4, 1, 5, 9])); // 9
    println!("{}", largest(&["apple", "banana", "cherry"])); // cherry

    let mut stack: Stack<i32> = Stack::new();
    stack.push(1); stack.push(2); stack.push(3);
    stack.print_top(); // Top: 3
    println!("{:?}", stack.pop()); // Some(3)

    let e: Either<i32, &str> = Either::Left(42);
    println!("{e:?}");
}
```

---

### Const Generics

```rust
// Generic over array SIZE at compile time
#[derive(Debug)]
struct Matrix<T, const ROWS: usize, const COLS: usize> {
    data: [[T; COLS]; ROWS],
}

impl<T: Default + Copy, const R: usize, const C: usize> Matrix<T, R, C> {
    fn new() -> Self {
        Matrix { data: [[T::default(); C]; R] }
    }
}

impl<const R: usize, const C: usize> Matrix<f64, R, C> {
    fn set(&mut self, row: usize, col: usize, val: f64) {
        self.data[row][col] = val;
    }
    fn get(&self, row: usize, col: usize) -> f64 {
        self.data[row][col]
    }
}

fn main() {
    let mut m: Matrix<f64, 2, 3> = Matrix::new();
    m.set(0, 0, 1.0);
    m.set(1, 2, 9.0);
    println!("{}", m.get(1, 2)); // 9
    println!("{:?}", m);
}
```

---

## 11. Lifetimes — Basic to Advanced

### Lifetime Basics

```rust
// Problem: which input does the output reference?
// Compiler needs to know to ensure no dangling refs.

fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() { x } else { y }
}

// Struct holding a reference
#[derive(Debug)]
struct Important<'a> {
    content: &'a str,
}

impl<'a> Important<'a> {
    fn announce(&self, announcement: &str) -> &str {
        println!("Attention: {announcement}");
        self.content
    }
}

fn main() {
    let string1 = String::from("long string");
    let result;
    {
        let string2 = String::from("xyz");
        result = longest(string1.as_str(), string2.as_str());
        println!("{result}"); // ok — string2 lives long enough here
    }

    let novel = String::from("Call me Ishmael...");
    let imp = Important { content: &novel[..4] };
    println!("{imp:?}");
}
```

---

### Lifetime Elision Rules

```rust
// The compiler applies 3 rules to infer lifetimes:
// Rule 1: Each reference parameter gets its own lifetime.
// Rule 2: If there is exactly one input lifetime, it goes to all outputs.
// Rule 3: If one parameter is &self/&mut self, its lifetime goes to all outputs.

// These are identical after elision:
fn first_word_explicit<'a>(s: &'a str) -> &'a str { &s[..5] }
fn first_word(s: &str) -> &str { &s[..5] }  // elision applies rule 2

// This REQUIRES explicit: two inputs, no &self
fn longest_explicit<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() { x } else { y }
}

// Method — rule 3 applies
struct Parser { source: String }
impl Parser {
    fn current_line(&self) -> &str {  // returns &self's lifetime
        &self.source[..]
    }
}
```

---

### Advanced Lifetime Patterns

```rust
// Multiple lifetime parameters
fn complex<'a, 'b>(x: &'a str, y: &'b str) -> &'a str {
    println!("{y}");
    x  // returns x's lifetime, not y's
}

// Lifetime bounds on generics
use std::fmt::Display;
fn longest_with_announcement<'a, T>(
    x: &'a str,
    y: &'a str,
    ann: T,
) -> &'a str
where
    T: Display,
{
    println!("Announcement: {ann}");
    if x.len() > y.len() { x } else { y }
}

// 'static lifetime — data lives forever
fn static_example() -> &'static str {
    "I live forever in the binary"
}

// Lifetime subtyping: 'long: 'short means 'long lives >= 'short
fn subtype<'long: 'short, 'short>(x: &'long str, _: &'short str) -> &'short str {
    x  // coerces 'long to 'short
}

// Higher-ranked trait bounds (HRTB) — for<'a>
// Used when a closure must work for ALL lifetimes
fn apply_to_str<F>(f: F, s: &str) -> usize
where
    F: for<'a> Fn(&'a str) -> usize,
{
    f(s)
}

fn main() {
    let result = apply_to_str(|s| s.len(), "hello");
    println!("{result}"); // 5
}
```

---

## 12. Error Handling

### The `?` Operator

```rust
use std::fs;
use std::io;
use std::num::ParseIntError;

// Custom error type
#[derive(Debug)]
enum AppError {
    Io(io::Error),
    Parse(ParseIntError),
    Custom(String),
}

impl std::fmt::Display for AppError {
    fn fmt(&self, f: &mut std::fmt::Formatter<'_>) -> std::fmt::Result {
        match self {
            AppError::Io(e)     => write!(f, "IO error: {e}"),
            AppError::Parse(e)  => write!(f, "Parse error: {e}"),
            AppError::Custom(s) => write!(f, "App error: {s}"),
        }
    }
}

impl std::error::Error for AppError {}

impl From<io::Error> for AppError {
    fn from(e: io::Error) -> Self { AppError::Io(e) }
}

impl From<ParseIntError> for AppError {
    fn from(e: ParseIntError) -> Self { AppError::Parse(e) }
}

fn read_and_double(path: &str) -> Result<i32, AppError> {
    let content = fs::read_to_string(path)?;  // io::Error → AppError via From
    let n = content.trim().parse::<i32>()?;   // ParseIntError → AppError via From
    if n < 0 {
        return Err(AppError::Custom(format!("{n} is negative")));
    }
    Ok(n * 2)
}
```

---

### `Option` Methods (Complete)

```rust
fn main() {
    let some: Option<i32> = Some(42);
    let none: Option<i32> = None;

    // Unwrapping
    some.unwrap();                          // panics if None
    some.unwrap_or(0);                      // default if None
    some.unwrap_or_else(|| expensive_fn()); // lazy default
    some.unwrap_or_default();               // Default::default()
    some.expect("should have value");       // custom panic message

    // Transforming
    some.map(|n| n * 2);                    // Some(84) or None
    some.map_or(0, |n| n * 2);             // 84 or 0
    some.map_or_else(|| 0, |n| n * 2);     // lazy version

    // Chaining
    some.and_then(|n| if n > 0 { Some(n) } else { None }); // flat_map
    none.or(Some(0));                       // Some(0)
    none.or_else(|| Some(99));              // lazy or

    // Checking
    some.is_some();   // true
    none.is_none();   // true
    some.filter(|&n| n > 100); // None — fails filter

    // Converting to Result
    some.ok_or("missing");                  // Ok(42)
    none.ok_or("missing");                  // Err("missing")
    none.ok_or_else(|| expensive_err_fn()); // lazy

    // Iteration
    for val in some { println!("{val}"); }  // iterates 0 or 1 times
}

fn expensive_fn() -> i32 { 99 }
fn expensive_err_fn() -> String { "expensive error".into() }
```

---

### `Result` Methods (Complete)

```rust
fn main() {
    let ok: Result<i32, &str>  = Ok(42);
    let err: Result<i32, &str> = Err("oops");

    // Unwrapping
    ok.unwrap();
    ok.unwrap_or(0);
    ok.unwrap_or_else(|e| { eprintln!("{e}"); 0 });
    ok.expect("expected Ok");
    err.unwrap_or_default();               // Default value

    // Transforming
    ok.map(|n| n * 2);                    // Ok(84)
    err.map_err(|e| format!("Error: {e}")); // changes error type
    ok.and_then(|n| if n > 0 { Ok(n) } else { Err("negative") });

    // Converting
    ok.ok();                               // Some(42)
    err.err();                             // Some("oops")
    ok.is_ok();
    err.is_err();

    // Collecting Results
    let strings = vec!["1", "2", "3", "bad", "5"];
    let nums: Result<Vec<i32>, _> = strings.iter()
        .map(|s| s.parse::<i32>())
        .collect();
    println!("{nums:?}"); // Err(...)

    let strings = vec!["1", "2", "3"];
    let nums: Result<Vec<i32>, _> = strings.iter()
        .map(|s| s.parse::<i32>())
        .collect();
    println!("{nums:?}"); // Ok([1, 2, 3])
}
```

---

### `thiserror` and `anyhow` Patterns

```rust
// thiserror — library for deriving error types (crate: thiserror)
// Shown as manual impl here (same effect):

#[derive(Debug)]
enum DatabaseError {
    ConnectionFailed { host: String },
    QueryFailed { sql: String, cause: String },
    NotFound { id: u64 },
}

impl std::fmt::Display for DatabaseError {
    fn fmt(&self, f: &mut std::fmt::Formatter<'_>) -> std::fmt::Result {
        match self {
            DatabaseError::ConnectionFailed { host } =>
                write!(f, "Failed to connect to {host}"),
            DatabaseError::QueryFailed { sql, cause } =>
                write!(f, "Query failed: {sql} — {cause}"),
            DatabaseError::NotFound { id } =>
                write!(f, "Record {id} not found"),
        }
    }
}

impl std::error::Error for DatabaseError {}

// Box<dyn Error> — erase the error type (like anyhow::Error)
fn flexible_error() -> Result<String, Box<dyn std::error::Error>> {
    let n = "42".parse::<i32>()?;         // ParseIntError
    let s = std::fs::read_to_string("x")?; // io::Error
    Ok(format!("{n}"))                    // both auto-boxed
}
```

---

## 13. Collections

### `Vec<T>` — Complete

```rust
fn main() {
    // Creating
    let mut v: Vec<i32> = Vec::new();
    let v2 = vec![1, 2, 3];
    let v3: Vec<i32> = (1..=5).collect();
    let v4 = Vec::from([1, 2, 3]);
    let v5: Vec<i32> = std::iter::repeat(0).take(5).collect();

    // Adding
    v.push(1); v.push(2); v.push(3);
    v.insert(1, 99);          // insert at index 1
    v.extend([4, 5, 6]);
    v.extend_from_slice(&v2);

    // Removing
    v.pop();                  // removes last
    v.remove(0);              // removes at index (shifts)
    v.swap_remove(0);         // fast remove (swaps with last)
    v.retain(|&x| x > 1);    // keep elements matching predicate
    v.dedup();                // remove consecutive duplicates
    v.clear();

    // Accessing
    let mut v = vec![10, 20, 30, 40, 50];
    println!("{}", v[2]);              // 30 (panics if out of bounds)
    println!("{:?}", v.get(2));        // Some(30) (safe)
    println!("{:?}", v.get(99));       // None
    println!("{:?}", v.first());       // Some(10)
    println!("{:?}", v.last());        // Some(50)

    // Searching
    println!("{:?}", v.iter().position(|&x| x == 30)); // Some(2)
    println!("{}", v.contains(&20));

    // Sorting
    v.sort();
    v.sort_unstable();
    v.sort_by(|a, b| b.cmp(a)); // reverse
    v.sort_by_key(|&x| -(x as i32));

    // Splitting / joining
    let (left, right) = v.split_at(2);
    let chunks: Vec<&[i32]> = v.chunks(2).collect();
    let windows: Vec<&[i32]> = v.windows(3).collect();

    // Deduplication after sort
    let mut dups = vec![1, 1, 2, 3, 3, 3, 4];
    dups.sort();
    dups.dedup();
    println!("{dups:?}"); // [1, 2, 3, 4]
}
```

---

### `HashMap<K, V>` — Complete

```rust
use std::collections::HashMap;

fn main() {
    let mut map: HashMap<String, i32> = HashMap::new();

    // Inserting
    map.insert("alice".to_string(), 100);
    map.insert("bob".to_string(), 85);

    // Entry API — most idiomatic for conditional insert/update
    map.entry("alice".to_string()).or_insert(0);      // no-op: already exists
    map.entry("carol".to_string()).or_insert(75);     // inserts carol=75
    *map.entry("alice".to_string()).or_insert(0) += 5; // alice += 5

    // or_insert_with — lazy (closure only called if needed)
    map.entry("dave".to_string()).or_insert_with(|| 60 + 5);

    // Accessing
    println!("{:?}", map.get("alice"));           // Some(105)
    println!("{}", map["alice"]);                 // 105 (panics if missing)
    println!("{}", map.contains_key("bob"));

    // Iterating
    for (k, v) in &map { println!("{k}: {v}"); }
    let keys: Vec<&String> = map.keys().collect();
    let vals: Vec<&i32>    = map.values().collect();

    // Removing
    map.remove("dave");

    // From iterator
    let pairs = vec![("x", 1), ("y", 2)];
    let m2: HashMap<_, _> = pairs.into_iter().collect();

    // Word frequency (common pattern)
    let text = "hello world hello rust world hello";
    let mut freq: HashMap<&str, u32> = HashMap::new();
    for word in text.split_whitespace() {
        *freq.entry(word).or_insert(0) += 1;
    }
    println!("{freq:?}");
}
```

---

### Other Collections

```rust
use std::collections::{HashSet, BTreeMap, BTreeSet, BinaryHeap, VecDeque, LinkedList};

fn main() {
    // HashSet — unique elements, O(1) ops
    let mut s: HashSet<i32> = HashSet::new();
    s.insert(1); s.insert(2); s.insert(1); // second 1 ignored
    println!("{}", s.len()); // 2
    let a: HashSet<_> = [1,2,3].iter().collect();
    let b: HashSet<_> = [2,3,4].iter().collect();
    let inter: HashSet<_> = a.intersection(&b).collect();
    let union: HashSet<_> = a.union(&b).collect();
    let diff: HashSet<_> = a.difference(&b).collect();

    // BTreeMap — sorted keys, O(log n) ops
    let mut bt: BTreeMap<&str, i32> = BTreeMap::new();
    bt.insert("zebra", 3); bt.insert("apple", 1); bt.insert("mango", 2);
    for (k, v) in &bt { print!("{k}:{v} "); } // sorted: apple mango zebra
    println!();

    // BinaryHeap — max-heap, priority queue
    let mut heap = BinaryHeap::new();
    heap.push(5); heap.push(2); heap.push(8); heap.push(1);
    while let Some(top) = heap.pop() {
        print!("{top} "); // 8 5 2 1
    }
    println!();

    // VecDeque — double-ended queue
    let mut dq: VecDeque<i32> = VecDeque::new();
    dq.push_back(1); dq.push_back(2);
    dq.push_front(0);
    println!("{dq:?}"); // [0, 1, 2]
    dq.pop_front();     // 0
    dq.pop_back();      // 2
}
```

---

## 14. Iterators (Deep Dive)

### Iterator Adapters

```rust
fn main() {
    let v = vec![1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

    // map — transform each element
    let doubled: Vec<_> = v.iter().map(|&x| x * 2).collect();

    // filter — keep matching elements
    let evens: Vec<_> = v.iter().filter(|&&x| x % 2 == 0).collect();

    // filter_map — filter + map in one (discard None)
    let parsed: Vec<i32> = vec!["1", "bad", "3", "4"]
        .iter()
        .filter_map(|s| s.parse().ok())
        .collect();
    println!("{parsed:?}"); // [1, 3, 4]

    // flat_map — map then flatten
    let words = vec!["hello world", "foo bar"];
    let chars: Vec<&str> = words.iter().flat_map(|s| s.split(' ')).collect();
    println!("{chars:?}"); // ["hello", "world", "foo", "bar"]

    // flatten
    let nested = vec![vec![1,2], vec![3,4], vec![5]];
    let flat: Vec<i32> = nested.into_iter().flatten().collect();
    println!("{flat:?}"); // [1,2,3,4,5]

    // fold (reduce with initial value)
    let sum = v.iter().fold(0, |acc, &x| acc + x);
    println!("{sum}"); // 55

    // reduce (fold without initial — returns Option)
    let product = v.iter().copied().reduce(|acc, x| acc * x);
    println!("{product:?}"); // Some(3628800)

    // take / skip / step_by / rev
    let first3: Vec<_> = v.iter().take(3).collect();
    let skip3: Vec<_>  = v.iter().skip(3).collect();
    let every2: Vec<_> = v.iter().step_by(2).collect();
    let rev: Vec<_>    = v.iter().rev().collect();

    // take_while / skip_while
    let head: Vec<_> = v.iter().take_while(|&&x| x < 5).collect();
    let tail: Vec<_> = v.iter().skip_while(|&&x| x < 5).collect();

    // zip — combine two iterators
    let a = vec![1, 2, 3];
    let b = vec!["one", "two", "three"];
    let zipped: Vec<_> = a.iter().zip(b.iter()).collect();
    println!("{zipped:?}"); // [(1,"one"), (2,"two"), (3,"three")]

    // unzip
    let (nums, strs): (Vec<_>, Vec<_>) = zipped.into_iter().unzip();

    // chain — concatenate iterators
    let c1 = vec![1, 2];
    let c2 = vec![3, 4];
    let chained: Vec<_> = c1.iter().chain(c2.iter()).collect();

    // enumerate
    for (i, val) in v.iter().enumerate() {
        if i == 0 { println!("first: {val}"); }
    }

    // peekable — look at next without consuming
    let mut iter = v.iter().peekable();
    println!("{:?}", iter.peek()); // Some(1)
    println!("{:?}", iter.next()); // Some(1) — consumed

    // any / all
    println!("{}", v.iter().any(|&x| x > 9));   // true
    println!("{}", v.iter().all(|&x| x > 0));   // true

    // find / find_map / position
    println!("{:?}", v.iter().find(|&&x| x > 7));        // Some(8)
    println!("{:?}", v.iter().position(|&x| x == 5));     // Some(4)

    // min / max / min_by / max_by / min_by_key
    println!("{:?}", v.iter().max());
    println!("{:?}", v.iter().min_by_key(|&&x| (x - 5).abs())); // closest to 5

    // sum / product / count
    let total: i32 = v.iter().sum();
    let count = v.iter().filter(|&&x| x % 2 == 0).count();

    // partition — split into two collections
    let (evens2, odds): (Vec<_>, Vec<_>) = v.iter().partition(|&&x| x % 2 == 0);
    println!("evens: {evens2:?}");

    // scan — stateful map (like fold but yields intermediate values)
    let running_sum: Vec<i32> = v.iter()
        .scan(0, |acc, &x| { *acc += x; Some(*acc) })
        .collect();
    println!("{running_sum:?}"); // [1, 3, 6, 10, 15, 21, 28, 36, 45, 55]

    // cycle — repeat iterator infinitely
    let cycling: Vec<_> = [1,2,3].iter().cycle().take(8).collect();
    println!("{cycling:?}"); // [1,2,3,1,2,3,1,2]
}
```

---

### Custom Iterator

```rust
struct Counter { value: u32, max: u32 }

impl Counter {
    fn new(max: u32) -> Self { Counter { value: 0, max } }
}

impl Iterator for Counter {
    type Item = u32;
    fn next(&mut self) -> Option<u32> {
        if self.value < self.max {
            self.value += 1;
            Some(self.value)
        } else {
            None
        }
    }
}

// Once you implement next(), you get ALL of these for free:
fn main() {
    let c = Counter::new(5);
    let sum: u32 = c.sum();         // 15
    println!("{sum}");

    // Pair with itself, zip, filter, map
    let pairs: Vec<_> = Counter::new(5)
        .zip(Counter::new(5).skip(1))
        .map(|(a, b)| a * b)
        .filter(|x| x % 3 == 0)
        .collect();
    println!("{pairs:?}"); // [6, 12]
}
```

---

## 15. Modules, Crates & Workspaces

### Module System

```rust
// src/main.rs or src/lib.rs

mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() { println!("added"); }
        pub fn seat_at_table() {}
    }
    mod serving {
        fn take_order() {}
        fn serve_order() {}
    }
}

// Use absolute path
use crate::front_of_house::hosting;

// Use relative path
// use self::front_of_house::hosting;
// use super::... (from child module to parent)

// Glob import
use std::collections::*;

// Renaming
use std::fmt::Result as FmtResult;
use std::io::Result as IoResult;

// Re-exporting (pub use)
pub use crate::front_of_house::hosting as PublicHosting;

fn main() {
    hosting::add_to_waitlist();
    // Or fully qualified:
    crate::front_of_house::hosting::add_to_waitlist();
}
```

---

### Cargo.toml & Feature Flags

```toml
[package]
name = "my_app"
version = "0.1.0"
edition = "2021"

[dependencies]
serde = { version = "1.0", features = ["derive"] }
tokio = { version = "1", features = ["full"] }
reqwest = { version = "0.11", optional = true }

[features]
default = []
http = ["reqwest"]
full = ["http"]

[dev-dependencies]
criterion = "0.5"

[build-dependencies]
cc = "1.0"

[profile.release]
opt-level = 3
lto = true
codegen-units = 1
```

```rust
// Conditional compilation with features
#[cfg(feature = "http")]
pub mod http_client {
    pub fn get(url: &str) -> String {
        format!("GET {url}")
    }
}

// cfg attributes
#[cfg(target_os = "linux")]
fn linux_only() { println!("Linux!"); }

#[cfg(debug_assertions)]
fn debug_check() { println!("debug mode"); }

#[cfg(test)]
mod tests {
    use super::*;
    #[test]
    fn it_works() { assert_eq!(2 + 2, 4); }
}
```

---

## 16. Smart Pointers

### `Box<T>` — Heap Allocation

```rust
fn main() {
    // Heap allocation
    let b = Box::new(5);
    println!("{}", *b);     // dereference

    // Recursive types (must use Box)
    #[derive(Debug)]
    enum Tree {
        Leaf(i32),
        Node(Box<Tree>, Box<Tree>),
    }
    let tree = Tree::Node(
        Box::new(Tree::Leaf(1)),
        Box::new(Tree::Node(
            Box::new(Tree::Leaf(2)),
            Box::new(Tree::Leaf(3)),
        )),
    );
    println!("{tree:?}");

    // Large data — avoid copying on stack
    let large: Box<[u8; 10_000]> = Box::new([0u8; 10_000]);
    println!("{}", large[0]);

    // Trait objects
    trait Speak { fn speak(&self) -> &str; }
    struct Dog; struct Cat;
    impl Speak for Dog { fn speak(&self) -> &str { "woof" } }
    impl Speak for Cat { fn speak(&self) -> &str { "meow" } }

    let animals: Vec<Box<dyn Speak>> = vec![Box::new(Dog), Box::new(Cat)];
    for a in &animals { println!("{}", a.speak()); }
}
```

---

### `Rc<T>` and `Arc<T>`

```rust
use std::rc::Rc;
use std::sync::Arc;
use std::cell::RefCell;
use std::sync::Mutex;

fn main() {
    // Rc — single-threaded shared ownership
    let a = Rc::new(5);
    let b = Rc::clone(&a);
    let c = Rc::clone(&a);
    println!("refs: {}", Rc::strong_count(&a)); // 3
    drop(b);
    println!("refs: {}", Rc::strong_count(&a)); // 2

    // Rc<RefCell<T>> — shared ownership + mutation (single thread)
    let shared = Rc::new(RefCell::new(vec![1, 2, 3]));
    let shared2 = Rc::clone(&shared);
    shared2.borrow_mut().push(4);
    println!("{:?}", shared.borrow()); // [1, 2, 3, 4]

    // Arc — atomic ref count (multi-threaded)
    let arc_data = Arc::new(Mutex::new(0));
    let mut handles = vec![];
    for _ in 0..5 {
        let d = Arc::clone(&arc_data);
        handles.push(std::thread::spawn(move || {
            *d.lock().unwrap() += 1;
        }));
    }
    for h in handles { h.join().unwrap(); }
    println!("{}", *arc_data.lock().unwrap()); // 5

    // Weak references — break reference cycles
    use std::rc::Weak;
    let strong = Rc::new(5);
    let weak: Weak<i32> = Rc::downgrade(&strong);
    println!("{:?}", weak.upgrade()); // Some(5)
    drop(strong);
    println!("{:?}", weak.upgrade()); // None — was dropped
}
```

---

### `Cell<T>` and `RefCell<T>`

```rust
use std::cell::{Cell, RefCell};

// Interior mutability — mutate through &self (shared reference)
struct Config {
    name: String,
    call_count: Cell<u32>,          // cheaply copyable interior mutation
    cache: RefCell<Option<String>>, // heap data interior mutation
}

impl Config {
    fn new(name: &str) -> Self {
        Config {
            name: name.to_string(),
            call_count: Cell::new(0),
            cache: RefCell::new(None),
        }
    }

    // &self — NOT &mut self
    fn get_value(&self) -> String {
        self.call_count.set(self.call_count.get() + 1);
        let mut cache = self.cache.borrow_mut();
        if cache.is_none() {
            *cache = Some(format!("computed value for {}", self.name));
        }
        cache.clone().unwrap()
    }

    fn calls(&self) -> u32 { self.call_count.get() }
}

fn main() {
    let cfg = Config::new("my_service");
    println!("{}", cfg.get_value()); // computed once
    println!("{}", cfg.get_value()); // cached
    println!("Called {} times", cfg.calls()); // 2

    // RefCell runtime borrow checking
    let rc = RefCell::new(vec![1, 2, 3]);
    let borrow1 = rc.borrow();     // immutable borrow
    let borrow2 = rc.borrow();     // second immutable — ok
    // rc.borrow_mut();            // PANIC: already borrowed immutably
    drop(borrow1); drop(borrow2);
    let mut mb = rc.borrow_mut(); // now ok
    mb.push(4);
}
```

---

## 17. Concurrency & Parallelism

### Threads

```rust
use std::thread;
use std::sync::{Arc, Mutex, RwLock};
use std::time::Duration;

fn main() {
    // Spawn a thread
    let handle = thread::spawn(|| {
        println!("Hello from thread!");
        thread::sleep(Duration::from_millis(100));
    });
    handle.join().unwrap(); // wait for thread

    // Move data into thread
    let data = vec![1, 2, 3];
    let h = thread::spawn(move || {
        println!("{data:?}");
    });
    h.join().unwrap();

    // Shared mutable state with Arc<Mutex<T>>
    let counter = Arc::new(Mutex::new(0i32));
    let mut handles = vec![];

    for i in 0..10 {
        let c = Arc::clone(&counter);
        handles.push(thread::spawn(move || {
            let mut lock = c.lock().unwrap();
            *lock += i;
        }));
    }
    for h in handles { h.join().unwrap(); }
    println!("Total: {}", *counter.lock().unwrap()); // 0+1+...+9 = 45

    // RwLock — multiple readers OR one writer
    let rw = Arc::new(RwLock::new(vec![1, 2, 3]));
    let rw2 = Arc::clone(&rw);

    let reader = thread::spawn(move || {
        let data = rw2.read().unwrap(); // multiple readers ok
        println!("{data:?}");
    });

    {
        let mut data = rw.write().unwrap(); // exclusive write
        data.push(4);
    }
    reader.join().unwrap();
}
```

---

### Channels

```rust
use std::sync::mpsc; // multi-producer, single-consumer
use std::thread;

fn main() {
    // Simple channel
    let (tx, rx) = mpsc::channel();

    thread::spawn(move || {
        tx.send("hello from thread").unwrap();
    });

    let received = rx.recv().unwrap(); // blocks until message
    println!("{received}");

    // Multiple producers
    let (tx, rx) = mpsc::channel::<i32>();
    let tx2 = tx.clone();

    thread::spawn(move || { for i in 0..5  { tx.send(i).unwrap(); } });
    thread::spawn(move || { for i in 10..15 { tx2.send(i).unwrap(); } });

    for _ in 0..10 {
        match rx.recv() {
            Ok(val) => print!("{val} "),
            Err(_)  => break,
        }
    }
    println!();

    // try_recv — non-blocking
    let (tx3, rx3) = mpsc::channel::<String>();
    match rx3.try_recv() {
        Ok(msg) => println!("{msg}"),
        Err(mpsc::TryRecvError::Empty) => println!("no message yet"),
        Err(mpsc::TryRecvError::Disconnected) => println!("sender dropped"),
    }
}
```

---

### Atomic Operations

```rust
use std::sync::atomic::{AtomicUsize, AtomicBool, Ordering};
use std::sync::Arc;
use std::thread;

fn main() {
    // Atomic counter — faster than Mutex for simple counters
    let counter = Arc::new(AtomicUsize::new(0));
    let mut handles = vec![];

    for _ in 0..100 {
        let c = Arc::clone(&counter);
        handles.push(thread::spawn(move || {
            c.fetch_add(1, Ordering::Relaxed);
        }));
    }
    for h in handles { h.join().unwrap(); }
    println!("{}", counter.load(Ordering::Relaxed)); // 100

    // AtomicBool — stop flag
    let running = Arc::new(AtomicBool::new(true));
    let r = Arc::clone(&running);
    let worker = thread::spawn(move || {
        while r.load(Ordering::Relaxed) {
            thread::yield_now();
        }
        println!("Worker stopped");
    });
    running.store(false, Ordering::Relaxed);
    worker.join().unwrap();

    // Ordering semantics:
    // Relaxed  — no synchronisation, just atomicity
    // Acquire  — pairs with Release: see all writes before the Release
    // Release  — pairs with Acquire: all writes visible to Acquire-reader
    // AcqRel   — both Acquire and Release
    // SeqCst   — total order across all threads (most expensive)
}
```

---

### `std::sync::Barrier` and `std::sync::Condvar`

```rust
use std::sync::{Arc, Barrier, Condvar, Mutex};
use std::thread;

fn main() {
    // Barrier — synchronise N threads at a checkpoint
    let barrier = Arc::new(Barrier::new(3));
    let mut handles = vec![];

    for i in 0..3 {
        let b = Arc::clone(&barrier);
        handles.push(thread::spawn(move || {
            println!("Thread {i} doing work...");
            thread::sleep(std::time::Duration::from_millis(i as u64 * 50));
            b.wait(); // all 3 threads block here until all arrive
            println!("Thread {i} past barrier");
        }));
    }
    for h in handles { h.join().unwrap(); }

    // Condvar — conditional variable for thread signalling
    let pair = Arc::new((Mutex::new(false), Condvar::new()));
    let pair2 = Arc::clone(&pair);

    thread::spawn(move || {
        let (lock, cvar) = &*pair2;
        let mut ready = lock.lock().unwrap();
        *ready = true;
        cvar.notify_one(); // wake one waiting thread
    });

    let (lock, cvar) = &*pair;
    let mut ready = lock.lock().unwrap();
    while !*ready {
        ready = cvar.wait(ready).unwrap(); // releases lock, waits for signal
    }
    println!("Got signal, ready={ready}");
}
```

---

## 18. Async / Await (Deep Dive)

### Futures and the Executor Model

```rust
// A Future is a value representing a computation not yet completed.
// async fn foo() -> T is sugar for fn foo() -> impl Future<Output = T>
// .await polls the future until it's ready, yielding if not.
// An executor (Tokio, async-std) drives futures.

use std::future::Future;
use std::pin::Pin;
use std::task::{Context, Poll};

// Manual Future implementation
struct MyFuture { value: i32 }

impl Future for MyFuture {
    type Output = i32;
    fn poll(self: Pin<&mut Self>, _cx: &mut Context<'_>) -> Poll<i32> {
        Poll::Ready(self.value * 2) // immediately ready
    }
}

// Async/await (compiled to state machine by rustc)
async fn fetch(id: u32) -> String {
    tokio::time::sleep(std::time::Duration::from_millis(10)).await;
    format!("item_{id}")
}

#[tokio::main]
async fn main() {
    // Sequential
    let a = fetch(1).await;
    let b = fetch(2).await;

    // Concurrent (both run simultaneously)
    let (c, d) = tokio::join!(fetch(3), fetch(4));

    // Racing — first to finish wins
    tokio::select! {
        val = fetch(5) => println!("fetch(5) won: {val}"),
        val = fetch(6) => println!("fetch(6) won: {val}"),
    }

    // Spawn background task
    let handle = tokio::spawn(async {
        fetch(99).await
    });
    let result = handle.await.unwrap();
    println!("{result}");

    // Timeout
    use tokio::time::timeout;
    let res = timeout(
        std::time::Duration::from_millis(5),
        fetch(7),
    ).await;
    match res {
        Ok(val) => println!("{val}"),
        Err(_)  => println!("timed out"),
    }
}
```

---

### Async Streams

```rust
use tokio_stream::{Stream, StreamExt};
use std::pin::Pin;

// Stream is the async equivalent of Iterator
async fn process_stream<S: Stream<Item = i32> + Unpin>(mut stream: S) {
    while let Some(val) = stream.next().await {
        println!("Got: {val}");
    }
}

// Creating a stream with async-stream crate (shown as concept):
// let s = stream! {
//     for i in 0..5 {
//         yield i;
//     }
// };
```

---

### Async Traits (Workarounds)

```rust
// Rust doesn't (yet in stable) support async fn in traits directly.
// Common workaround: return BoxFuture

use std::future::Future;
use std::pin::Pin;

type BoxFuture<'a, T> = Pin<Box<dyn Future<Output = T> + Send + 'a>>;

trait DataSource: Send + Sync {
    fn fetch(&self, id: u32) -> BoxFuture<'_, String>;
}

struct MockSource;

impl DataSource for MockSource {
    fn fetch(&self, id: u32) -> BoxFuture<'_, String> {
        Box::pin(async move {
            format!("data_{id}")
        })
    }
}

#[tokio::main]
async fn main() {
    let src: Box<dyn DataSource> = Box::new(MockSource);
    let data = src.fetch(42).await;
    println!("{data}");
}
```

---

## 19. Macros — Declarative & Procedural

### `macro_rules!` (Declarative Macros)

```rust
// Declarative macros match patterns and expand to code at compile time.

// Variable number of arguments
macro_rules! print_all {
    () => {};
    ($first:expr $(, $rest:expr)*) => {
        print!("{} ", $first);
        print_all!($($rest),*);
    };
}

// Create a HashMap easily
macro_rules! hashmap {
    ($($key:expr => $val:expr),* $(,)?) => {{
        let mut m = std::collections::HashMap::new();
        $(m.insert($key, $val);)*
        m
    }};
}

// Assert with custom message
macro_rules! ensure {
    ($cond:expr, $msg:literal) => {
        if !$cond { panic!("Assertion failed: {}", $msg); }
    };
    ($cond:expr, $fmt:literal, $($args:expr),+) => {
        if !$cond { panic!($fmt, $($args),+); }
    };
}

fn main() {
    print_all!(1, "hello", 3.14, true);
    println!();

    let m = hashmap! {
        "a" => 1,
        "b" => 2,
        "c" => 3,
    };
    println!("{m:?}");

    ensure!(2 + 2 == 4, "basic math broken");
    // ensure!(1 == 2, "expected {} == {}", 1, 2); // panics
}
```

---

### Procedural Macros

```rust
// Procedural macros are Rust code that generates Rust code.
// Three types:
// 1. Custom derive:     #[derive(MyMacro)]
// 2. Attribute macros: #[my_attribute]
// 3. Function-like:    my_macro!(...)

// Example of what derive macros look like (from the serde crate):
use serde::{Serialize, Deserialize};

#[derive(Debug, Clone, Serialize, Deserialize, PartialEq)]
struct Config {
    host: String,
    port: u16,
    debug: bool,
    tags: Vec<String>,
}

fn main() {
    let cfg = Config {
        host: "localhost".to_string(),
        port: 8080,
        debug: true,
        tags: vec!["web".to_string(), "api".to_string()],
    };

    // Serialize to JSON
    let json = serde_json::to_string_pretty(&cfg).unwrap();
    println!("{json}");

    // Deserialize back
    let cfg2: Config = serde_json::from_str(&json).unwrap();
    assert_eq!(cfg, cfg2);
}
```

---

### Useful Built-in Macros

```rust
fn main() {
    // Formatting
    println!("{:?}", (1, 2));              // debug
    println!("{:#?}", vec![1,2,3]);        // pretty debug
    println!("{:>10}", "right");           // right-align
    println!("{:<10}", "left");            // left-align
    println!("{:^10}", "center");          // center
    println!("{:0>5}", 42);               // zero-pad: 00042
    println!("{:b}", 255);                 // binary: 11111111
    println!("{:x}", 255);                 // hex: ff
    println!("{:e}", 1_000_000.0f64);      // scientific: 1e6

    // Assertions
    assert!(1 + 1 == 2);
    assert_eq!(2 + 2, 4, "math is broken");
    assert_ne!(1, 2);

    // Debug
    let v = vec![1, 2, 3];
    dbg!(&v);              // prints "[src/main.rs:37] &v = [1, 2, 3]"
    let doubled = dbg!(v.iter().map(|&x| x * 2).collect::<Vec<_>>());

    // Compile-time info
    println!("{}", file!());       // "src/main.rs"
    println!("{}", line!());       // 43
    println!("{}", module_path!()); // "crate::module"
    println!("{}", env!("CARGO_PKG_VERSION")); // "0.1.0"

    // Panicking
    // panic!("something went wrong");
    // todo!("implement this later");
    // unimplemented!("this variant is not handled");
    // unreachable!("should never reach here");

    // Type info
    println!("{}", std::mem::size_of::<i32>());    // 4
    println!("{}", std::mem::size_of::<String>()); // 24 (on 64-bit)
    println!("{}", std::mem::align_of::<f64>());   // 8
}
```

---

## 20. Unsafe Rust

### What Unsafe Allows

```rust
fn main() {
    // 1. Dereference raw pointers
    let mut val = 42;
    let raw_const: *const i32 = &val;
    let raw_mut:  *mut i32   = &mut val;

    unsafe {
        println!("{}", *raw_const);  // dereference
        *raw_mut += 8;
        println!("{}", *raw_mut);    // 50
    }

    // 2. Call unsafe functions
    unsafe fn dangerous(x: i32) -> i32 { x * 2 }
    let result = unsafe { dangerous(21) };
    println!("{result}"); // 42

    // 3. Access/modify mutable statics
    static mut COUNTER: i32 = 0;
    unsafe {
        COUNTER += 1;
        println!("{COUNTER}");
    }

    // 4. Implement unsafe traits (Send, Sync)
    // struct MyWrapper(*mut u8);
    // unsafe impl Send for MyWrapper {}

    // 5. Access union fields
    union IntOrFloat { i: i32, f: f32 }
    let u = IntOrFloat { i: 0x3f800000 };
    unsafe { println!("{}", u.f); } // 1.0
}
```

---

### Safe Abstractions Over Unsafe Code

```rust
use std::slice;

// Building safe API over unsafe internals
fn split_at_mid<T>(slice: &[T], mid: usize) -> (&[T], &[T]) {
    let len = slice.len();
    let ptr = slice.as_ptr();
    assert!(mid <= len, "mid > len");

    unsafe {
        (
            slice::from_raw_parts(ptr, mid),
            slice::from_raw_parts(ptr.add(mid), len - mid),
        )
    }
}

// Interior mutability using unsafe (like Cell/RefCell)
struct NaiveCell<T>(std::cell::UnsafeCell<T>);

impl<T: Copy> NaiveCell<T> {
    fn new(val: T) -> Self { NaiveCell(std::cell::UnsafeCell::new(val)) }
    fn get(&self) -> T { unsafe { *self.0.get() } }
    fn set(&self, val: T) { unsafe { *self.0.get() = val; } }
}

fn main() {
    let v = vec![1, 2, 3, 4, 5];
    let (left, right) = split_at_mid(&v, 3);
    println!("{left:?} {right:?}");

    let cell = NaiveCell::new(42);
    cell.set(100);
    println!("{}", cell.get());
}
```

---

## 21. FFI — Foreign Function Interface

### Calling C from Rust

```rust
// Link to the C standard library
extern "C" {
    fn abs(n: i32) -> i32;
    fn sqrt(x: f64) -> f64;
    fn printf(fmt: *const u8, ...) -> i32;
    fn malloc(size: usize) -> *mut std::ffi::c_void;
    fn free(ptr: *mut std::ffi::c_void);
}

fn main() {
    let x = unsafe { abs(-42) };
    let s = unsafe { sqrt(2.0) };
    println!("{x} {s:.4}"); // 42 1.4142
}

// Calling Rust from C — expose Rust functions
#[no_mangle]              // prevents name mangling
pub extern "C" fn add(a: i32, b: i32) -> i32 { a + b }

#[no_mangle]
pub extern "C" fn greet(name: *const std::ffi::c_char) {
    use std::ffi::CStr;
    let name = unsafe { CStr::from_ptr(name).to_str().unwrap_or("?") };
    println!("Hello, {name}!");
}
```

---

### CStr, CString, OsStr

```rust
use std::ffi::{CStr, CString, OsStr, OsString};
use std::os::unix::ffi::OsStrExt;

fn main() {
    // CString — owned, null-terminated, for passing TO C
    let c_string = CString::new("hello world").unwrap();
    let ptr = c_string.as_ptr(); // *const c_char — safe as long as c_string lives

    // CStr — borrowed, null-terminated, for receiving FROM C
    let c_str: &CStr = unsafe { CStr::from_ptr(ptr) };
    let rust_str: &str = c_str.to_str().unwrap();
    println!("{rust_str}");

    // OsString / OsStr — platform-native strings (may not be valid UTF-8 on all OS)
    let os_str: &OsStr = OsStr::new("path/to/file.txt");
    let os_string: OsString = os_str.to_os_string();
    if let Some(s) = os_str.to_str() {
        println!("{s}");
    }
}
```

---

## 22. Type System Advanced Features

### Newtype Pattern

```rust
// Wrap a primitive in a struct to get type safety
#[derive(Debug, Clone, Copy, PartialEq, PartialOrd)]
struct Kilometers(f64);

#[derive(Debug, Clone, Copy, PartialEq, PartialOrd)]
struct Miles(f64);

impl From<Miles> for Kilometers {
    fn from(m: Miles) -> Kilometers { Kilometers(m.0 * 1.60934) }
}

// Newtypes for validated data
#[derive(Debug, Clone)]
struct Email(String);

impl Email {
    fn new(s: &str) -> Result<Self, String> {
        if s.contains('@') { Ok(Email(s.to_string())) }
        else { Err(format!("'{s}' is not a valid email")) }
    }
    fn as_str(&self) -> &str { &self.0 }
}

fn send_email(_to: &Email, message: &str) {
    println!("Sending '{}' to {}", message, _to.as_str());
}

fn main() {
    let km = Kilometers(100.0);
    let mi = Miles(62.14);
    let converted: Kilometers = mi.into();
    println!("{km:?} ≈ {converted:.2?}");

    let email = Email::new("alice@example.com").unwrap();
    send_email(&email, "Hello!");
    // send_email("raw string", "hello"); // compile error — type mismatch
}
```

---

### Type Aliases vs Newtypes

```rust
// Type alias — same type, just a shorter name. No extra safety.
type Meters = f64;
type Km = f64;
// let m: Meters = 5.0;
// let k: Km = m;  // FINE — same type!

// Newtype — truly different type. Compiler prevents mixing.
struct Meters2(f64);
struct Km2(f64);
// let m = Meters2(5.0);
// let k: Km2 = m;  // ERROR — different types!

// type for complex generics
type Thunk = Box<dyn Fn() -> String>;
type Result<T> = std::result::Result<T, std::io::Error>;

fn returns_thunk() -> Thunk {
    Box::new(|| String::from("hello"))
}
```

---

### The `!` (Never) Type

```rust
// ! means "this expression never produces a value"
// Used for functions that diverge (panic, loop forever, exit)

fn exit_program() -> ! {
    std::process::exit(1);
}

fn infinite_loop() -> ! {
    loop { println!("forever"); }
}

fn main() {
    // ! coerces to any type — that's why match arms can have different diverging arms:
    let x: i32 = 5;
    let value: i32 = match x {
        0 => 0,
        _ => {
            // panic! has type !, which coerces to i32
            if x < 0 { panic!("negative!") }
            x * 2
        }
    };
    println!("{value}");

    // continue, break, return also have type !
    let arr = [1, 2, 3];
    let val = loop {
        let n = arr[0];
        if n > 0 { break n; } // break value has type of the loop
    };
}
```

---

### Phantom Data

```rust
use std::marker::PhantomData;

// PhantomData<T> — tells compiler "I logically use T" without storing it
// Common uses: type-state patterns, variance, dropck

#[derive(Debug)]
struct TypedId<T> {
    id: u64,
    _marker: PhantomData<T>,
}

struct User;
struct Product;

impl<T> TypedId<T> {
    fn new(id: u64) -> Self {
        TypedId { id, _marker: PhantomData }
    }
    fn value(&self) -> u64 { self.id }
}

fn get_user(_id: TypedId<User>) -> String { format!("user") }
fn get_product(_id: TypedId<Product>) -> String { format!("product") }

fn main() {
    let user_id = TypedId::<User>::new(1);
    let product_id = TypedId::<Product>::new(42);

    get_user(user_id);
    get_product(product_id);
    // get_user(product_id); // COMPILE ERROR — wrong type!
}
```

---

## 23. Operator Overloading

```rust
use std::ops::{Add, Sub, Mul, Neg, Index, IndexMut};
use std::fmt;

#[derive(Debug, Clone, Copy, PartialEq)]
struct Vec2 { x: f64, y: f64 }

impl Vec2 {
    fn new(x: f64, y: f64) -> Self { Vec2 { x, y } }
    fn dot(&self, other: &Vec2) -> f64 { self.x * other.x + self.y * other.y }
    fn len(&self) -> f64 { (self.x * self.x + self.y * self.y).sqrt() }
}

impl Add for Vec2 {
    type Output = Vec2;
    fn add(self, rhs: Vec2) -> Vec2 { Vec2::new(self.x + rhs.x, self.y + rhs.y) }
}

impl Sub for Vec2 {
    type Output = Vec2;
    fn sub(self, rhs: Vec2) -> Vec2 { Vec2::new(self.x - rhs.x, self.y - rhs.y) }
}

impl Mul<f64> for Vec2 {
    type Output = Vec2;
    fn mul(self, s: f64) -> Vec2 { Vec2::new(self.x * s, self.y * s) }
}

impl Neg for Vec2 {
    type Output = Vec2;
    fn neg(self) -> Vec2 { Vec2::new(-self.x, -self.y) }
}

impl fmt::Display for Vec2 {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        write!(f, "({:.2}, {:.2})", self.x, self.y)
    }
}

// Index operator
struct Matrix2x2([[f64; 2]; 2]);

impl Index<(usize, usize)> for Matrix2x2 {
    type Output = f64;
    fn index(&self, (r, c): (usize, usize)) -> &f64 { &self.0[r][c] }
}

fn main() {
    let a = Vec2::new(1.0, 2.0);
    let b = Vec2::new(3.0, 4.0);

    println!("{}", a + b);       // (4.00, 6.00)
    println!("{}", a - b);       // (-2.00, -2.00)
    println!("{}", a * 2.0);     // (2.00, 4.00)
    println!("{}", -a);          // (-1.00, -2.00)
    println!("{:.2}", a.dot(&b)); // 11.00
    println!("{:.4}", a.len());   // 2.2361

    let m = Matrix2x2([[1.0, 2.0], [3.0, 4.0]]);
    println!("{}", m[(1, 0)]); // 3.0
}
```

---

## 24. String Types

### `str`, `String`, and Conversions

```rust
fn main() {
    // &str — string slice (borrowed reference, usually to static or String data)
    let s1: &str = "hello, world"; // 'static
    let s2: &'static str = "also static";

    // String — owned, heap-allocated, growable
    let mut s3 = String::new();
    let s4 = String::from("hello");
    let s5 = "hello".to_string();
    let s6 = "hello".to_owned();

    // Growing
    s3.push_str("hello");
    s3.push(' ');
    s3.push_str("world");

    // Concatenation
    let s7 = String::from("hello, ");
    let s8 = String::from("world!");
    let s9 = s7 + &s8; // s7 moved, s8 borrowed

    // format! — doesn't consume
    let combined = format!("{} {}", s4, s5);

    // Slicing (byte indices — must be on char boundaries!)
    let hello = &s4[0..5]; // &str

    // Iteration
    for c in "hello".chars() { print!("{c} "); }   // chars (Unicode)
    for b in "hello".bytes() { print!("{b} "); }   // bytes (u8)
    println!();

    // Common methods
    let text = "  Hello, World!  ";
    println!("{}", text.trim());                   // "Hello, World!"
    println!("{}", text.to_uppercase());
    println!("{}", text.to_lowercase());
    println!("{}", text.contains("World"));        // true
    println!("{}", text.replace("World", "Rust")); // "  Hello, Rust!  "
    println!("{:?}", text.split(", ").collect::<Vec<_>>());
    println!("{}", text.starts_with("  Hello"));
    println!("{}", "42".parse::<i32>().unwrap());  // parse

    // String capacity / length
    let s = String::with_capacity(50);
    println!("{}", s.capacity()); // 50
    println!("{}", "hello".len()); // 5 bytes
    println!("{}", "hello".chars().count()); // 5 chars

    // Unicode
    let s = "Здравствуйте"; // Russian
    println!("{} bytes, {} chars", s.len(), s.chars().count());
}
```

---

## 25. Slices & Arrays

```rust
fn main() {
    // Fixed-size arrays — stored entirely on stack
    let arr: [i32; 5] = [1, 2, 3, 4, 5];
    let zeros = [0u8; 256]; // 256 zeros

    println!("{}", arr.len());      // 5
    println!("{}", arr[2]);         // 3
    println!("{arr:?}");

    // Array methods
    let sorted = { let mut a = arr; a.sort(); a };
    println!("{:?}", arr.iter().sum::<i32>()); // 15
    println!("{:?}", arr.contains(&3));         // true
    println!("{:?}", arr.iter().max());          // Some(5)

    // Slices — dynamically sized views
    let slice: &[i32] = &arr[1..4]; // [2, 3, 4]
    println!("{slice:?}");

    // Mutable slices
    let mut v = vec![3, 1, 4, 1, 5, 9, 2, 6];
    let mid = v.len() / 2;
    let (left, right) = v.split_at_mut(mid);
    left.sort();
    right.sort();
    println!("{v:?}");

    // Slice methods
    println!("{:?}", v.windows(3).collect::<Vec<_>>()); // overlapping windows
    println!("{:?}", v.chunks(3).collect::<Vec<_>>());  // non-overlapping chunks

    // Multidimensional
    let grid: [[i32; 3]; 3] = [[1,2,3],[4,5,6],[7,8,9]];
    for row in &grid {
        println!("{row:?}");
    }
}
```

---

## 26. Closures (Advanced)

### Capturing and Escape

```rust
fn main() {
    // By reference (default when possible)
    let x = vec![1, 2, 3];
    let equal_to_x = |z: &Vec<i32>| z == &x;
    println!("{}", equal_to_x(&vec![1, 2, 3])); // true
    println!("{x:?}"); // x still valid

    // By value (move keyword)
    let name = String::from("Rust");
    let greeting = move || format!("Hello, {name}!");
    println!("{}", greeting()); // Hello, Rust!
    // name moved into closure

    // Closures as struct fields
    struct Cacher<T: Fn(i32) -> i32> {
        calculation: T,
        value: Option<i32>,
    }

    impl<T: Fn(i32) -> i32> Cacher<T> {
        fn new(calc: T) -> Self {
            Cacher { calculation: calc, value: None }
        }
        fn value(&mut self, arg: i32) -> i32 {
            match self.value {
                Some(v) => v,
                None => {
                    let v = (self.calculation)(arg);
                    self.value = Some(v);
                    v
                }
            }
        }
    }

    let mut expensive = Cacher::new(|n| { println!("computing..."); n * 2 });
    println!("{}", expensive.value(5));   // computes: 10
    println!("{}", expensive.value(99));  // cached: 10

    // Returning closure from function
    fn apply_twice(f: impl Fn(i32) -> i32) -> impl Fn(i32) -> i32 {
        move |x| f(f(x))
    }

    let quad = apply_twice(|x| x * 2);
    println!("{}", quad(3)); // 12
}
```

---

## 27. Trait Objects vs Generics

```rust
use std::fmt;

trait Drawable: fmt::Debug {
    fn draw(&self);
    fn area(&self) -> f64;
}

#[derive(Debug)] struct Circle(f64);
#[derive(Debug)] struct Square(f64);
impl Drawable for Circle { fn draw(&self) { print!("○"); } fn area(&self) -> f64 { std::f64::consts::PI * self.0 * self.0 } }
impl Drawable for Square { fn draw(&self) { print!("□"); } fn area(&self) -> f64 { self.0 * self.0 } }

// Static dispatch — generic, zero overhead, but homogeneous
fn draw_static<T: Drawable>(shape: &T) {
    shape.draw();
    println!(" area={:.2}", shape.area());
}

// Dynamic dispatch — runtime vtable, heterogeneous
fn draw_dynamic(shape: &dyn Drawable) {
    shape.draw();
    println!(" area={:.2}", shape.area());
}

// When to use which:
// - Generics (static): performance-critical, all same type, no runtime polymorphism needed
// - dyn Trait (dynamic): heterogeneous collections, plugin systems, when type unknown at compile time

fn main() {
    let c = Circle(3.0);
    let s = Square(4.0);

    draw_static(&c);    // monomorphised to draw_static::<Circle>
    draw_static(&s);    // monomorphised to draw_static::<Square>

    // Heterogeneous collection — must use dyn
    let shapes: Vec<Box<dyn Drawable>> = vec![
        Box::new(Circle(1.0)),
        Box::new(Square(2.0)),
        Box::new(Circle(3.0)),
    ];
    let total_area: f64 = shapes.iter().map(|s| s.area()).sum();
    println!("Total area: {total_area:.2}");

    // Object safety rules: a trait is object-safe if:
    // 1. No generic methods
    // 2. No methods returning Self
    // 3. No associated functions without self parameter
    // Clone is NOT object-safe (returns Self)
    // Iterator IS object-safe (type Item is associated, not generic method)
}
```

---

## 28. Advanced Lifetime Patterns

### Non-Lexical Lifetimes (NLL)

```rust
fn main() {
    let mut v = vec![1, 2, 3, 4, 5];

    // Without NLL, this would fail. NLL ends borrows at last use, not scope end.
    let first = &v[0];    // borrow starts
    println!("{first}");  // last use of borrow
                          // borrow ends HERE (NLL)
    v.push(6);            // now allowed — previous borrow is over
    println!("{v:?}");
}
```

---

### Lifetime Variance

```rust
// Covariant: if 'long: 'short, then T<'long> can be used as T<'short>
// e.g. &'long T is covariant in 'long — you can use a longer-lived ref where shorter expected

// Contravariant: fn(T<'long>) is contravariant in 'long
// — a function accepting shorter-lived refs is more general

// Invariant: &'a mut T is invariant in T
// — you can't substitute &mut String for &mut &str

fn covariant_example<'long: 'short, 'short>(r: &'long str) -> &'short str {
    r // covarcion allowed: &'long → &'short
}

// Invariant in practice — this demonstrates why &mut T is invariant:
fn invariant_example() {
    let mut s: &str = "hello";
    {
        let owned = String::from("world");
        // &mut &str is invariant — can't assign shorter lifetime through mutable ref
        // change_str(&mut s, &owned); // would be unsound if allowed
    }
    // println!("{s}"); // s might point to dropped owned here!
}
```

---

## 29. Memory Layout & Repr

### `repr` Attributes

```rust
// Default repr: compiler chooses layout for optimal performance
#[derive(Debug)]
struct Default {
    a: u8,
    b: u32,
    c: u8,
    // compiler may reorder: b, a, c with padding
}

// repr(C): C-compatible layout, no reordering
#[repr(C)]
#[derive(Debug)]
struct CLayout {
    a: u8,
    _pad: [u8; 3],
    b: u32,
    c: u8,
    _pad2: [u8; 3],
}

// repr(packed): no padding (may cause alignment issues)
#[repr(packed)]
struct Packed { a: u8, b: u32, c: u8 }

// repr(transparent): single-field struct with same layout as inner type
#[repr(transparent)]
struct Wrapper(i32);

// repr(u8/u16/...): specify discriminant size for enums
#[repr(u8)]
enum Status { Active = 1, Inactive = 2, Pending = 3 }

fn main() {
    use std::mem::{size_of, align_of};
    println!("Default  size={} align={}", size_of::<Default>(),  align_of::<Default>());
    println!("CLayout  size={} align={}", size_of::<CLayout>(),  align_of::<CLayout>());
    println!("Packed   size={} align={}", size_of::<Packed>(),   align_of::<Packed>());
    println!("Wrapper  size={} align={}", size_of::<Wrapper>(),  align_of::<Wrapper>());
    println!("Status   size={} align={}", size_of::<Status>(),   align_of::<Status>());

    // size_of_val — size of a dynamically sized type
    let s = "hello world";
    println!("str bytes: {}", std::mem::size_of_val(s)); // 11
}
```

---

### `mem` Module

```rust
use std::mem;

fn main() {
    // swap — exchange two values
    let mut a = 1;
    let mut b = 2;
    mem::swap(&mut a, &mut b);
    println!("{a} {b}"); // 2 1

    // replace — replace value, return old
    let mut s = String::from("hello");
    let old = mem::replace(&mut s, String::from("world"));
    println!("{old} {s}"); // hello world

    // take — take value, leave Default in its place
    let mut opt = Some(42i32);
    let val = mem::take(&mut opt);
    println!("{val:?} {opt:?}"); // Some(42) None

    // forget — prevent Drop from running
    let v = vec![1, 2, 3];
    mem::forget(v); // v's memory leaked (no Drop called)
    // Useful in FFI when C takes ownership of memory

    // transmute — reinterpret bytes (VERY unsafe)
    let bits: u32 = 0x3f800000;
    let float: f32 = unsafe { mem::transmute(bits) };
    println!("{float}"); // 1.0

    // ManuallyDrop — prevent automatic Drop
    use mem::ManuallyDrop;
    let v = ManuallyDrop::new(vec![1, 2, 3]);
    let ptr = ManuallyDrop::as_ptr(&v); // raw pointer to inner vec
}
```

---

## 30. Build System & Cargo

### Advanced Cargo

```toml
# Cargo.toml
[package]
name = "my_crate"
version = "1.2.3"
edition = "2021"
authors = ["Alice <alice@example.com>"]
description = "An example crate"
license = "MIT OR Apache-2.0"
repository = "https://github.com/example/my_crate"
keywords = ["parsing", "utility"]
categories = ["parsing"]
readme = "README.md"
exclude = ["tests/fixtures/*"]

[[bin]]
name = "my_tool"
path = "src/bin/my_tool.rs"

[[example]]
name = "basic"
path = "examples/basic.rs"

[[bench]]
name = "performance"
harness = false

[lib]
name = "my_crate"
crate-type = ["cdylib", "rlib"]  # cdylib for C FFI, rlib for Rust

[workspace]
members = ["crate_a", "crate_b", "shared"]
```

```toml
# .cargo/config.toml — project-wide build settings
[build]
target = "x86_64-unknown-linux-musl"
rustflags = ["-C", "target-cpu=native"]

[profile.release]
opt-level = 3
lto = "fat"
codegen-units = 1
panic = "abort"
strip = true

[profile.dev]
opt-level = 0
debug = true

[profile.bench]
inherits = "release"
debug = true
```

---

## 31. Testing

### Unit Tests, Integration Tests, Benchmarks

```rust
// Unit tests — in the same file, inside #[cfg(test)]
pub fn add(a: i32, b: i32) -> i32 { a + b }
pub fn divide(a: f64, b: f64) -> Option<f64> {
    if b == 0.0 { None } else { Some(a / b) }
}

#[cfg(test)]
mod tests {
    use super::*;

    // Basic test
    #[test]
    fn test_add() {
        assert_eq!(add(2, 3), 5);
        assert_ne!(add(2, 3), 6);
    }

    // Test that should panic
    #[test]
    #[should_panic(expected = "divide by zero")]
    fn test_panic() {
        panic!("divide by zero");
    }

    // Test returning Result
    #[test]
    fn test_result() -> Result<(), String> {
        let result = divide(10.0, 2.0).ok_or("division failed")?;
        assert!((result - 5.0).abs() < f64::EPSILON);
        Ok(())
    }

    // Ignore a test
    #[test]
    #[ignore = "too slow for CI"]
    fn expensive_test() {
        // takes minutes...
    }

    // Test with custom failure message
    #[test]
    fn test_with_message() {
        let x = add(2, 2);
        assert_eq!(x, 4, "Expected 4, got {x}");
    }

    // Parameterised-style test (manual)
    #[test]
    fn test_multiple_cases() {
        let cases = vec![(1, 2, 3), (0, 0, 0), (-1, 1, 0), (100, -50, 50)];
        for (a, b, expected) in cases {
            assert_eq!(add(a, b), expected, "add({a}, {b})");
        }
    }
}

// Integration tests go in tests/ directory (separate files)
// tests/integration_test.rs:
// use my_crate::add;
// #[test]
// fn test_add_integration() { assert_eq!(add(5, 5), 10); }
```

---

### Benchmarking with Criterion

```rust
// benches/my_bench.rs
use criterion::{black_box, criterion_group, criterion_main, Criterion};

fn fibonacci(n: u64) -> u64 {
    match n { 0 => 0, 1 => 1, _ => fibonacci(n-1) + fibonacci(n-2) }
}

fn bench_fibonacci(c: &mut Criterion) {
    c.bench_function("fib 20", |b| b.iter(|| fibonacci(black_box(20))));

    let mut group = c.benchmark_group("fibonacci");
    for i in [10u64, 20, 30].iter() {
        group.bench_with_input(format!("fib {i}"), i, |b, i| {
            b.iter(|| fibonacci(black_box(*i)))
        });
    }
    group.finish();
}

criterion_group!(benches, bench_fibonacci);
criterion_main!(benches);
```

---

## 32. Common Standard Library Traits

### Full Trait Catalogue

```rust
use std::fmt;
use std::ops::*;
use std::cmp::Ordering;

// Display — user-facing formatting ({})
// Debug   — developer formatting ({:?})
// Both shown in Section 9.

// PartialEq / Eq — equality
// PartialOrd / Ord — comparison/ordering

#[derive(Debug)]
struct Temperature(f64);

impl PartialEq for Temperature {
    fn eq(&self, other: &Self) -> bool {
        (self.0 - other.0).abs() < 0.001
    }
}

impl PartialOrd for Temperature {
    fn partial_cmp(&self, other: &Self) -> Option<Ordering> {
        self.0.partial_cmp(&other.0)
    }
}

// Hash — for use as HashMap key
use std::hash::{Hash, Hasher};

#[derive(Debug, Eq, PartialEq)]
struct Point { x: i32, y: i32 }

impl Hash for Point {
    fn hash<H: Hasher>(&self, state: &mut H) {
        self.x.hash(state);
        self.y.hash(state);
    }
}

// Default — produce a zero-value
#[derive(Debug)]
struct Config { host: String, port: u16, debug: bool }

impl Default for Config {
    fn default() -> Self {
        Config { host: "localhost".into(), port: 8080, debug: false }
    }
}

// AsRef / AsMut — cheap reference conversion
struct Buffer(Vec<u8>);
impl AsRef<[u8]> for Buffer {
    fn as_ref(&self) -> &[u8] { &self.0 }
}

fn process_bytes<T: AsRef<[u8]>>(data: T) {
    let bytes = data.as_ref();
    println!("{} bytes", bytes.len());
}

fn main() {
    let t1 = Temperature(36.6);
    let t2 = Temperature(36.6001);
    println!("{}", t1 == t2); // true (within tolerance)
    println!("{}", t1 < Temperature(37.0)); // true

    let mut map = std::collections::HashMap::new();
    map.insert(Point { x: 1, y: 2 }, "origin");

    let cfg = Config::default();
    println!("{cfg:?}");

    let buf = Buffer(vec![1, 2, 3]);
    process_bytes(&buf);
    process_bytes(vec![1, 2]);  // Vec<u8> also impl AsRef<[u8]>
    process_bytes("hello");     // &str too
}
```

---

## 33. Interior Mutability Patterns

### `UnsafeCell`, `Cell`, `RefCell`, `Mutex`, `RwLock`, `Atomic`

```rust
// The interior mutability spectrum:
//
// UnsafeCell  — raw primitive, requires unsafe, zero overhead
// Cell<T>     — safe, Copy types only, no references into data
// RefCell<T>  — safe, runtime borrow checking, panics on violation
// Mutex<T>    — safe, blocking, multi-threaded
// RwLock<T>   — safe, readers/writers, multi-threaded
// Atomic*     — safe, lock-free, multi-threaded, primitives only

use std::cell::{Cell, RefCell};
use std::sync::{Mutex, RwLock};
use std::sync::atomic::{AtomicI32, Ordering};

struct Stats {
    hits: Cell<u32>,         // simple counter — no overhead
    log: RefCell<Vec<String>>, // structured logging — heap
    count: AtomicI32,          // thread-safe counter
}

impl Stats {
    fn new() -> Self {
        Stats {
            hits: Cell::new(0),
            log: RefCell::new(vec![]),
            count: AtomicI32::new(0),
        }
    }

    fn record(&self, msg: &str) {
        self.hits.set(self.hits.get() + 1);
        self.log.borrow_mut().push(msg.to_string());
        self.count.fetch_add(1, Ordering::Relaxed);
    }
}

fn main() {
    let stats = Stats::new();
    stats.record("request_1");
    stats.record("request_2");
    println!("hits: {}", stats.hits.get());
    println!("log: {:?}", stats.log.borrow());
    println!("atomic: {}", stats.count.load(Ordering::Relaxed));
}
```

---

## 34. Zero-Cost Abstractions

### How Rust Achieves Zero Cost

```rust
// Rust's iterators compile to the SAME code as hand-written loops.
// No runtime overhead for:
// - Generics (monomorphised at compile time)
// - Traits (static dispatch inlined)
// - Iterators (fusion-optimised to a single loop)
// - Closures (inlined when possible)

fn sum_hand_written(v: &[i32]) -> i32 {
    let mut sum = 0;
    let mut i = 0;
    while i < v.len() {
        sum += v[i];
        i += 1;
    }
    sum
}

fn sum_iterator(v: &[i32]) -> i32 {
    // Compiles to identical machine code as above
    v.iter().sum()
}

fn sum_complex_chain(v: &[i32]) -> i32 {
    // This entire chain is fused into ONE loop by the optimizer
    v.iter()
        .filter(|&&x| x > 0)
        .map(|&x| x * 2)
        .sum()
}

// Generic functions are monomorphised — one compiled version per concrete type
fn print_debug<T: std::fmt::Debug>(val: &T) {
    println!("{val:?}");
    // Becomes: println!("{:?}", val) with T hardcoded — no vtable
}

fn main() {
    let v = vec![1, -2, 3, -4, 5];
    println!("{}", sum_hand_written(&v));
    println!("{}", sum_iterator(&v));
    println!("{}", sum_complex_chain(&v)); // 18 (1+3+5)*2

    print_debug(&42i32);          // monomorphised for i32
    print_debug(&"hello");        // monomorphised for &str
    print_debug(&vec![1,2,3]);    // monomorphised for Vec<i32>
}
```

---

## 35. Newtype Pattern & Type Safety

### Enforcing Invariants at Compile Time

```rust
// The type system as a documentation and safety tool

// Prevent mixing different units
#[derive(Debug, Clone, Copy, PartialEq, PartialOrd)]
struct Seconds(f64);
#[derive(Debug, Clone, Copy, PartialEq, PartialOrd)]
struct Meters(f64);
#[derive(Debug, Clone, Copy, PartialEq, PartialOrd)]
struct MetersPerSecond(f64);

impl std::ops::Div<Seconds> for Meters {
    type Output = MetersPerSecond;
    fn div(self, rhs: Seconds) -> MetersPerSecond {
        MetersPerSecond(self.0 / rhs.0)
    }
}

// Type-state pattern — encode state in the type system
struct Connection<State> {
    addr: String,
    _state: std::marker::PhantomData<State>,
}
struct Disconnected;
struct Connected;
struct Authenticated;

impl Connection<Disconnected> {
    fn new(addr: &str) -> Self {
        Connection { addr: addr.to_string(), _state: std::marker::PhantomData }
    }
    fn connect(self) -> Connection<Connected> {
        println!("Connecting to {}...", self.addr);
        Connection { addr: self.addr, _state: std::marker::PhantomData }
    }
}

impl Connection<Connected> {
    fn authenticate(self, token: &str) -> Connection<Authenticated> {
        println!("Authenticating with token {token}...");
        Connection { addr: self.addr, _state: std::marker::PhantomData }
    }
}

impl Connection<Authenticated> {
    fn send(&self, msg: &str) {
        println!("Sending '{}' to {}", msg, self.addr);
    }
}

fn main() {
    let speed = Meters(100.0) / Seconds(9.58);
    println!("{speed:.2?}"); // MetersPerSecond(10.44)

    // Type state — can't call send() without connecting first!
    let conn = Connection::<Disconnected>::new("server:8080");
    let conn = conn.connect();
    let conn = conn.authenticate("secret_token");
    conn.send("hello");
    // conn.connect(); // ERROR — already Connected or Authenticated
}
```

---

## 36. State Machines in Rust

```rust
// Enum-based state machine
#[derive(Debug, PartialEq, Clone)]
enum TrafficLight {
    Red,
    Yellow,
    Green,
}

impl TrafficLight {
    fn next(&self) -> TrafficLight {
        match self {
            TrafficLight::Red    => TrafficLight::Green,
            TrafficLight::Green  => TrafficLight::Yellow,
            TrafficLight::Yellow => TrafficLight::Red,
        }
    }

    fn duration_seconds(&self) -> u32 {
        match self {
            TrafficLight::Red    => 30,
            TrafficLight::Green  => 25,
            TrafficLight::Yellow => 5,
        }
    }
}

// Richer state machine with data
#[derive(Debug)]
enum Order {
    Draft { items: Vec<String> },
    Submitted { items: Vec<String>, submitted_at: u64 },
    Shipped { tracking: String },
    Delivered,
    Cancelled { reason: String },
}

impl Order {
    fn submit(self, now: u64) -> Result<Order, String> {
        match self {
            Order::Draft { items } if items.is_empty() =>
                Err("Cannot submit empty order".into()),
            Order::Draft { items } =>
                Ok(Order::Submitted { items, submitted_at: now }),
            _ => Err("Order is not in Draft state".into()),
        }
    }

    fn ship(self, tracking: &str) -> Result<Order, String> {
        match self {
            Order::Submitted { .. } =>
                Ok(Order::Shipped { tracking: tracking.to_string() }),
            _ => Err("Can only ship Submitted orders".into()),
        }
    }
}

fn main() {
    let mut light = TrafficLight::Red;
    for _ in 0..6 {
        println!("{light:?} ({} sec)", light.duration_seconds());
        light = light.next();
    }

    let order = Order::Draft { items: vec!["book".into(), "pen".into()] };
    let order = order.submit(1_000_000).unwrap();
    let order = order.ship("TRACK123").unwrap();
    println!("{order:?}");
}
```

---

## 37. Builder Pattern

```rust
#[derive(Debug, Clone)]
struct HttpRequest {
    url: String,
    method: String,
    headers: std::collections::HashMap<String, String>,
    body: Option<Vec<u8>>,
    timeout_ms: u64,
}

struct HttpRequestBuilder {
    url: String,
    method: String,
    headers: std::collections::HashMap<String, String>,
    body: Option<Vec<u8>>,
    timeout_ms: u64,
}

impl HttpRequestBuilder {
    fn new(url: &str) -> Self {
        HttpRequestBuilder {
            url: url.to_string(),
            method: "GET".to_string(),
            headers: std::collections::HashMap::new(),
            body: None,
            timeout_ms: 30_000,
        }
    }

    fn method(mut self, m: &str) -> Self { self.method = m.to_string(); self }
    fn header(mut self, key: &str, val: &str) -> Self {
        self.headers.insert(key.to_string(), val.to_string()); self
    }
    fn body(mut self, data: Vec<u8>) -> Self { self.body = Some(data); self }
    fn timeout_ms(mut self, ms: u64) -> Self { self.timeout_ms = ms; self }
    fn json_body(mut self, json: &str) -> Self {
        self.headers.insert("Content-Type".into(), "application/json".into());
        self.body = Some(json.as_bytes().to_vec());
        self
    }

    fn build(self) -> Result<HttpRequest, String> {
        if self.url.is_empty() { return Err("URL is required".into()); }
        Ok(HttpRequest {
            url: self.url,
            method: self.method,
            headers: self.headers,
            body: self.body,
            timeout_ms: self.timeout_ms,
        })
    }
}

fn main() {
    let req = HttpRequestBuilder::new("https://api.example.com/users")
        .method("POST")
        .header("Authorization", "Bearer token123")
        .header("Accept", "application/json")
        .json_body(r#"{"name": "Alice", "age": 30}"#)
        .timeout_ms(5_000)
        .build()
        .unwrap();

    println!("{req:#?}");
}
```

---

## 38. Compile-Time Programming

### `const fn`, `const generics`, `const` evaluation

```rust
// const fn — evaluated at compile time when called in const context
const fn fibonacci(n: u64) -> u64 {
    match n {
        0 => 0,
        1 => 1,
        n => fibonacci(n - 1) + fibonacci(n - 2),
    }
}

const FIB_20: u64 = fibonacci(20); // computed at compile time

// Const arrays
const PRIMES: [u32; 10] = [2, 3, 5, 7, 11, 13, 17, 19, 23, 29];

// Const generics — generic over constant values
#[derive(Debug, Clone, Copy)]
struct ArrayStack<T, const N: usize> {
    data: [Option<T>; N],
    top: usize,
}

impl<T: Copy + Default, const N: usize> ArrayStack<T, N> {
    const fn new() -> Self {
        ArrayStack { data: [None; N], top: 0 }
    }

    fn push(&mut self, val: T) -> bool {
        if self.top == N { return false; } // full
        self.data[self.top] = Some(val);
        self.top += 1;
        true
    }

    fn pop(&mut self) -> Option<T> {
        if self.top == 0 { return None; }
        self.top -= 1;
        self.data[self.top].take()
    }
}

// compile-time assertions
const _: () = assert!(FIB_20 == 6765, "fibonacci is wrong");
const _: () = assert!(std::mem::size_of::<u32>() == 4);

fn main() {
    println!("fib(20) = {FIB_20}");

    let mut stack: ArrayStack<i32, 4> = ArrayStack::new(); // fixed size on stack!
    stack.push(1);
    stack.push(2);
    stack.push(3);
    println!("{:?}", stack.pop()); // Some(3)

    // include_str! — read file at compile time
    // const SCHEMA: &str = include_str!("schema.sql");

    // include_bytes! — read binary at compile time
    // const ICON: &[u8] = include_bytes!("icon.png");
}
```

---

## 39. SIMD & Intrinsics

### Platform-Specific Vectorisation

```rust
// std::simd is stabilising. For now, target_feature + unsafe intrinsics.

#[cfg(target_arch = "x86_64")]
use std::arch::x86_64::*;

// Safe abstraction over SIMD addition
#[cfg(target_arch = "x86_64")]
#[target_feature(enable = "avx2")]
unsafe fn simd_add_f32(a: &[f32], b: &[f32], out: &mut [f32]) {
    assert_eq!(a.len(), b.len());
    assert_eq!(a.len(), out.len());
    let len = a.len();
    let mut i = 0;

    // Process 8 floats at a time with AVX2
    while i + 8 <= len {
        let va = _mm256_loadu_ps(a.as_ptr().add(i));
        let vb = _mm256_loadu_ps(b.as_ptr().add(i));
        let vc = _mm256_add_ps(va, vb);
        _mm256_storeu_ps(out.as_mut_ptr().add(i), vc);
        i += 8;
    }
    // Handle remainder scalar
    while i < len {
        out[i] = a[i] + b[i];
        i += 1;
    }
}

// Auto-vectorisation hint (no unsafe needed)
fn sum_slice(v: &[f32]) -> f32 {
    // LLVM will often vectorise this automatically:
    v.iter().sum()
}

fn main() {
    let a = vec![1.0f32; 16];
    let b = vec![2.0f32; 16];
    let mut out = vec![0.0f32; 16];

    #[cfg(target_arch = "x86_64")]
    if is_x86_feature_detected!("avx2") {
        unsafe { simd_add_f32(&a, &b, &mut out); }
        println!("{:?}", &out[..4]); // [3.0, 3.0, 3.0, 3.0]
    }

    println!("{}", sum_slice(&a)); // 16.0
}
```

---

## 40. Quick Reference Cheat Sheet

### All Rust Keywords

| Keyword | Category | Purpose |
|---------|----------|---------|
| `as` | Casting/Imports | Type cast or rename import |
| `async` | Async | Mark async function/block |
| `await` | Async | Suspend until Future resolves |
| `break` | Control | Exit loop (optionally with value) |
| `const` | Types | Compile-time constant |
| `continue` | Control | Skip to next iteration |
| `crate` | Modules | Crate-root path anchor |
| `dyn` | Traits | Dynamic dispatch trait object |
| `else` | Control | Fallback branch |
| `enum` | Types | Algebraic data type |
| `extern` | FFI | C ABI linkage |
| `false` | Literals | Boolean false |
| `fn` | Functions | Define function |
| `for` | Control | Iterate over IntoIterator |
| `if` | Control | Conditional expression |
| `impl` | Types | Implement methods/traits |
| `in` | Control | Part of `for x in` syntax |
| `let` | Bindings | Bind a value to a name |
| `loop` | Control | Infinite loop (can return value) |
| `match` | Control | Exhaustive pattern matching |
| `mod` | Modules | Declare or define a module |
| `move` | Closures | Capture variables by value |
| `mut` | Ownership | Enable mutation |
| `pub` | Modules | Make item publicly accessible |
| `ref` | Patterns | Bind by reference in pattern |
| `return` | Functions | Early return from function |
| `Self` | Types | Current implementing type |
| `self` | Methods | Current instance |
| `static` | Memory | Static lifetime binding |
| `struct` | Types | Named-field data structure |
| `super` | Modules | Parent module |
| `trait` | Traits | Define shared interface |
| `true` | Literals | Boolean true |
| `type` | Types | Type alias |
| `union` | Unsafe | C-style union |
| `unsafe` | Safety | Opt into unsafe operations |
| `use` | Modules | Bring name into scope |
| `where` | Generics | Trait bound clause |
| `while` | Control | Condition-based loop |

### Reserved Keywords (Not Yet Stable)
`abstract`, `become`, `box`, `do`, `final`, `macro`, `override`, `priv`, `try`, `typeof`, `unsized`, `virtual`, `yield`

---

### Ownership Mental Model

```
T               — owned value, single owner
&T              — shared borrow (immutable, many allowed)
&mut T          — exclusive borrow (one at a time)
Box<T>          — owned heap value
Rc<T>           — shared ownership (single thread)
Arc<T>          — shared ownership (multi-thread)
Rc<RefCell<T>>  — shared + mutable (single thread, runtime checked)
Arc<Mutex<T>>   — shared + mutable (multi-thread, blocking)
Arc<RwLock<T>>  — shared + mutable (multi-thread, readers/writers)
Pin<T>          — cannot be moved (async state machines)
```

---

### Trait Quick Reference

| Trait | Purpose | Derive? |
|-------|---------|---------|
| `Debug` | `{:?}` formatting | ✅ |
| `Display` | `{}` formatting | ❌ impl |
| `Clone` | Explicit deep copy | ✅ |
| `Copy` | Implicit bitwise copy | ✅ |
| `PartialEq` / `Eq` | Equality (`==`) | ✅ |
| `PartialOrd` / `Ord` | Ordering (`<`, `>`) | ✅ |
| `Hash` | Hash map key | ✅ |
| `Default` | Zero/empty value | ✅ |
| `From` / `Into` | Infallible conversion | ❌ impl |
| `TryFrom` / `TryInto` | Fallible conversion | ❌ impl |
| `AsRef` / `AsMut` | Cheap reference conversion | ❌ impl |
| `Iterator` | Lazy sequence | ❌ impl `next()` |
| `IntoIterator` | Convert to iterator | ❌ impl |
| `Index` / `IndexMut` | `[]` operator | ❌ impl |
| `Add/Sub/Mul/Div` | Arithmetic operators | ❌ impl |
| `Deref` / `DerefMut` | `*` operator / coercion | ❌ impl |
| `Drop` | Custom destructor | ❌ impl |
| `Send` | Safe to transfer between threads | auto |
| `Sync` | Safe to share ref between threads | auto |
| `Fn` / `FnMut` / `FnOnce` | Callable | auto (closures) |
| `Future` | Async computation | ❌ impl / `async fn` |
| `Error` | Error type | ❌ impl |

---

### Error Handling Decision Tree

```
Function can fail?
├── No  → return T
└── Yes → return Result<T, E>
    ├── Propagate? → use ?  (requires From<SourceError> for E)
    ├── Handle all cases → use match
    ├── Provide default → .unwrap_or(val)
    ├── Lazy default → .unwrap_or_else(|| compute())
    ├── Convert to Option → .ok()
    └── Panic on None → .unwrap() / .expect("msg")
```

---

### Async Mental Model

```
async fn foo() -> T     == fn foo() -> impl Future<Output=T>
foo().await             == poll future until Poll::Ready(T)
tokio::join!(a, b)      == run a and b concurrently, wait for both
tokio::select!(a, b)    == run a and b concurrently, return first
tokio::spawn(fut)       == background task, returns JoinHandle
Pin<Box<dyn Future>>    == boxed/erasure future (trait object)
Stream                  == async Iterator (futures::Stream)
```

---

### Performance Tips

| Situation | Tip |
|-----------|-----|
| Repeated string building | Use `String::with_capacity(n)` |
| Frequent small allocations | Use `Vec::with_capacity(n)` |
| HashMap lookups | Use `.entry()` API to avoid double lookup |
| Cloning large structs | Pass `&T` instead |
| Many trait objects | Prefer generics (static dispatch) for hot paths |
| Large futures | Use `Box::pin` to move to heap |
| Thread-safe counter | `AtomicUsize` instead of `Mutex<usize>` |
| Sorting | Prefer `sort_unstable` — faster when order of equal elements doesn't matter |
| Collecting into Vec | Use `.collect::<Vec<_>>()` with size hint |
| Allocation-free error handling | Use `?` with no-alloc error types |

---

## Resources

| Resource | URL |
|----------|-----|
| The Book | https://doc.rust-lang.org/book/ |
| Rust by Example | https://doc.rust-lang.org/rust-by-example/ |
| Standard Library Docs | https://doc.rust-lang.org/std/ |
| Rustonomicon (Unsafe) | https://doc.rust-lang.org/nomicon/ |
| Async Book | https://rust-lang.github.io/async-book/ |
| Rust Reference | https://doc.rust-lang.org/reference/ |
| Rust API Guidelines | https://rust-lang.github.io/api-guidelines/ |
| The Cargo Book | https://doc.rust-lang.org/cargo/ |
| Rust Cookbook | https://rust-lang-nursery.github.io/rust-cookbook/ |
| This Week in Rust | https://this-week-in-rust.org/ |
| Crates.io | https://crates.io/ |
| Docs.rs | https://docs.rs/ |

---

*Complete Rust Language Reference — Basic to Extremely Advanced. Every keyword, feature, and pattern with real-world code.*
