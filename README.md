# 🦀 Complete Rust Keywords Reference

> A comprehensive guide to every Rust keyword — covering ownership, borrowing, memory safety, concurrency, modules, traits, generics, async programming, error handling, and more. Each keyword includes purpose, real-world usage, and a beginner-friendly code example.

---

## Table of Contents

1. [Variable Bindings & Mutability](#1-variable-bindings--mutability)
2. [Functions & Methods](#2-functions--methods)
3. [Control Flow](#3-control-flow)
4. [Data Types](#4-data-types)
5. [Traits & Generics](#5-traits--generics)
6. [Ownership & Borrowing](#6-ownership--borrowing)
7. [Modules & Visibility](#7-modules--visibility)
8. [Error Handling](#8-error-handling)
9. [Async Programming](#9-async-programming)
10. [Unsafe & FFI](#10-unsafe--ffi)
11. [Standard Library Types](#11-standard-library-types)
12. [Concurrency Primitives](#12-concurrency-primitives)
13. [Smart Pointers](#13-smart-pointers)
14. [Macros & Attributes](#14-macros--attributes)
15. [Marker Traits](#15-marker-traits)
16. [Common Traits to Implement](#16-common-traits-to-implement)
17. [Lifetimes](#17-lifetimes)
18. [Type Conversions](#18-type-conversions)

---

## 1. Variable Bindings & Mutability

### `let`

| | |
|---|---|
| **Purpose** | Binds a value to a variable name |
| **Category** | Variable declaration |

**Explanation:**
`let` is how you create variables in Rust. By default, all variables are **immutable** — you cannot change them after assignment. This is a deliberate design choice: immutability prevents accidental state changes and makes code easier to reason about in large codebases. If you need to change a value, you must explicitly opt in with `mut`.

**Real-world usage:**
Used everywhere — declaring local variables in functions, loop bodies, match arms, and conditional blocks.

```rust
fn main() {
    let x = 5;          // immutable — cannot be changed
    let mut y = 10;     // mutable — can be changed
    y += 1;
    println!("{x} {y}"); // Output: 5 11

    // let bindings can shadow each other
    let z = "hello";
    let z = z.len();    // shadowing — z is now a usize
    println!("{z}");    // Output: 5
}
```

---

### `mut`

| | |
|---|---|
| **Purpose** | Marks a variable or reference as mutable |
| **Category** | Mutability |

**Explanation:**
`mut` is an explicit opt-in to mutation. Rust requires you to declare upfront when a variable or reference will be modified. This design makes data-flow easy to audit — if you see no `mut`, nothing changes. The compiler enforces this, so forgetting `mut` is a compile error, not a silent bug.

**Real-world usage:**
Used whenever a variable needs to change — loop counters, accumulators, struct fields being updated, and mutable function parameters.

```rust
fn increment(n: &mut i32) {
    *n += 1;
}

fn main() {
    let mut val = 42;
    increment(&mut val);
    println!("{val}"); // Output: 43

    let mut v = vec![1, 2, 3];
    v.push(4); // requires v to be mut
}
```

---

## 2. Functions & Methods

### `fn`

| | |
|---|---|
| **Purpose** | Defines a function |
| **Category** | Functions |

**Explanation:**
`fn` is the basic unit of code organisation in Rust. Functions take typed parameters and return a typed value. The last expression in a function body is implicitly returned — no `return` keyword needed unless you want an early exit. The mandatory entry point of every Rust program is `fn main()`.

**Real-world usage:**
Used for utility helpers, library APIs, struct methods, free functions, closures-as-arguments, and the program entry point.

```rust
fn add(a: i32, b: i32) -> i32 {
    a + b   // implicit return — no semicolon
}

fn greet(name: &str) -> String {
    format!("Hello, {}!", name)
}

fn main() {
    println!("{}", add(3, 4));        // Output: 7
    println!("{}", greet("Rustacean")); // Output: Hello, Rustacean!
}
```

---

### `return`

| | |
|---|---|
| **Purpose** | Returns a value from a function early |
| **Category** | Control flow |

**Explanation:**
While Rust functions implicitly return their last expression, `return` lets you exit early — before reaching the last line. This is useful for guard clauses and early bailout conditions that would otherwise require deeply nested `if` blocks.

**Real-world usage:**
Guard clauses at the start of a function, early exit on validation failure, returning from nested loops.

```rust
fn first_even(nums: &[i32]) -> Option<i32> {
    for &n in nums {
        if n % 2 == 0 {
            return Some(n); // exit immediately when found
        }
    }
    None // implicit return if no even number found
}

fn main() {
    println!("{:?}", first_even(&[1, 3, 4, 6])); // Output: Some(4)
    println!("{:?}", first_even(&[1, 3, 5]));     // Output: None
}
```

---

## 3. Control Flow

### `if` / `else`

| | |
|---|---|
| **Purpose** | Conditional branching |
| **Category** | Control flow |

**Explanation:**
`if` evaluates a boolean condition and executes a block if true. `else` provides the fallback. Crucially, in Rust **`if` is an expression** — it produces a value. This means you can use it directly in assignments, replacing the need for a ternary operator (`? :`) found in other languages.

**Real-world usage:**
Universal — used everywhere for input validation, business logic, feature flags, and conditional assignments.

```rust
fn grade(score: u32) -> &'static str {
    if score >= 90 { "A" }
    else if score >= 70 { "B" }
    else { "C" }
}

fn main() {
    // if as an expression — assign its result
    let max = if 5 > 3 { 5 } else { 3 };
    println!("{}", grade(85)); // Output: B
    println!("{max}");         // Output: 5
}
```

---

### `match`

| | |
|---|---|
| **Purpose** | Pattern-matching — a supercharged switch statement |
| **Category** | Control flow |

**Explanation:**
`match` is Rust's most powerful control-flow construct. It compares a value against a series of patterns and runs the first match. The compiler **enforces exhaustiveness** — every possible case must be handled. Match arms can destructure enums, bind inner values, use range patterns, and use guard conditions with `if`.

**Real-world usage:**
Handling `Option<T>` and `Result<T, E>`, processing enum variants, state machines, command dispatch, and AST traversal in compilers.

```rust
enum Shape {
    Circle(f64),
    Rectangle(f64, f64),
    Triangle(f64, f64, f64),
}

fn describe_area(shape: Shape) -> String {
    match shape {
        Shape::Circle(r) => format!("Circle area: {:.2}", std::f64::consts::PI * r * r),
        Shape::Rectangle(w, h) => format!("Rectangle area: {:.2}", w * h),
        Shape::Triangle(a, b, c) => {
            let s = (a + b + c) / 2.0;
            format!("Triangle area: {:.2}", (s*(s-a)*(s-b)*(s-c)).sqrt())
        }
    }
}

fn main() {
    println!("{}", describe_area(Shape::Circle(3.0)));
    println!("{}", describe_area(Shape::Rectangle(4.0, 5.0)));

    // match with ranges and guards
    let n = 42;
    let msg = match n {
        0        => "zero",
        1..=9    => "single digit",
        10..=99  => "double digit",
        _        => "large",
    };
    println!("{msg}"); // Output: double digit
}
```

---

### `loop`

| | |
|---|---|
| **Purpose** | Creates an infinite loop that runs until an explicit `break` |
| **Category** | Control flow |

**Explanation:**
`loop` runs its body forever until you `break` out. Unlike `while true`, a `loop` can **return a value** via `break value` — making it useful for "repeat until success" patterns. The Rust compiler also knows a `loop` always runs at least once, which helps with initialization.

**Real-world usage:**
Event loops, REPL implementations, retry logic with exponential backoff, and reading from a channel until closed.

```rust
fn find_answer() -> i32 {
    let mut attempts = 0;
    let result = loop {
        attempts += 1;
        if attempts == 5 {
            break attempts * 10; // return a value from loop
        }
    };
    result
}

fn main() {
    println!("{}", find_answer()); // Output: 50
}
```

---

### `while`

| | |
|---|---|
| **Purpose** | Loops while a boolean condition is true |
| **Category** | Control flow |

**Explanation:**
`while` checks its condition before each iteration. If the condition is false at the start, the body never runs. It's the classic "check first, run if true" loop.

**Real-world usage:**
Draining a queue, polling a sensor until a condition is met, reading until EOF, game loops with a running flag.

```rust
fn main() {
    let mut n = 1;
    while n < 100 {
        n *= 2;
    }
    println!("{n}"); // Output: 128

    // while let — loop until pattern stops matching
    let mut stack = vec![1, 2, 3];
    while let Some(top) = stack.pop() {
        println!("{top}"); // Prints 3, then 2, then 1
    }
}
```

---

### `for`

| | |
|---|---|
| **Purpose** | Iterates over any type implementing `IntoIterator` |
| **Category** | Control flow |

**Explanation:**
`for` is the idiomatic way to iterate in Rust. It works with ranges, `Vec`, `HashMap`, arrays, and any custom type implementing the `Iterator` trait. Rust's `for` loop is bounds-safe by design — no off-by-one index mistakes.

**Real-world usage:**
Processing collections, iterating over lines in a file, traversing tree nodes, batch-processing records from a database.

```rust
fn main() {
    // Range iteration
    for i in 1..=5 {
        print!("{i} "); // Output: 1 2 3 4 5
    }
    println!();

    // Vector iteration with index
    let fruits = vec!["apple", "banana", "cherry"];
    for (i, fruit) in fruits.iter().enumerate() {
        println!("{i}: {fruit}");
    }

    // HashMap iteration
    use std::collections::HashMap;
    let mut scores = HashMap::new();
    scores.insert("Alice", 95);
    scores.insert("Bob", 87);
    for (name, score) in &scores {
        println!("{name}: {score}");
    }
}
```

---

### `break` / `continue`

| | |
|---|---|
| **Purpose** | `break` exits a loop; `continue` skips to the next iteration |
| **Category** | Control flow |

**Explanation:**
`break` immediately exits the nearest enclosing loop. `continue` skips the rest of the current iteration and moves to the next. Both support **loop labels** (`'label:`) to target an outer loop in nested structures.

**Real-world usage:**
Skipping invalid records in data processing, exiting nested grid traversal, filtering items during iteration.

```rust
fn main() {
    // continue — skip even numbers
    for n in 0..10 {
        if n % 2 == 0 { continue; }
        print!("{n} "); // Output: 1 3 5 7 9
    }
    println!();

    // break with label — exit outer loop
    'outer: for i in 0..5 {
        for j in 0..5 {
            if i + j > 4 {
                break 'outer; // exits both loops
            }
            print!("({i},{j}) ");
        }
    }
    println!();
}
```

---

### `if let` / `while let`

| | |
|---|---|
| **Purpose** | Combines pattern matching with `if` or `while` |
| **Category** | Control flow / Pattern matching |

**Explanation:**
`if let` is syntactic sugar for a `match` with one arm — it runs a block only if the pattern matches. `while let` loops as long as the pattern keeps matching. Both are common idioms for working with `Option` and `Result` without verbose `match` blocks.

**Real-world usage:**
Unwrapping optional config values, consuming iterators, processing queues, parsing tokens.

```rust
fn main() {
    // if let — only run if Some
    let config: Option<&str> = Some("debug");
    if let Some(level) = config {
        println!("Log level: {level}"); // Output: Log level: debug
    }

    // while let — drain a stack
    let mut stack = vec![1, 2, 3];
    while let Some(top) = stack.pop() {
        println!("Popped: {top}"); // 3, 2, 1
    }
}
```

---

### `let else`

| | |
|---|---|
| **Purpose** | Destructures a pattern or diverges (return/break/panic) |
| **Category** | Control flow / Error handling |

**Explanation:**
Introduced in Rust 1.65, `let else` is a clean alternative to `if let` + early return. If the pattern doesn't match, the `else` block must **diverge** (return, break, continue, or panic). The bound variable is available after the statement — no nesting required.

**Real-world usage:**
Input validation, parsing, extracting values from API responses, and guard clauses at function entry.

```rust
fn parse_port(s: &str) -> u16 {
    let Ok(n) = s.parse::<u16>() else {
        panic!("Invalid port: {s}");
    };
    n // n is in scope here — no nesting needed
}

fn process(input: Option<String>) {
    let Some(value) = input else {
        return; // diverge — function exits
    };
    println!("Processing: {value}");
}

fn main() {
    println!("{}", parse_port("8080")); // Output: 8080
    process(Some("hello".into()));      // Output: Processing: hello
    process(None);                      // returns silently
}
```

---

## 4. Data Types

### `struct`

| | |
|---|---|
| **Purpose** | Defines a data structure with named fields |
| **Category** | Types |

**Explanation:**
`struct` groups related data together under a named type. Unlike classes in OOP languages, Rust structs have no inheritance. Behaviour is added separately via `impl` blocks and traits. There are three kinds: regular structs (named fields), tuple structs, and unit structs.

**Real-world usage:**
Domain models (User, Order, Config), HTTP request/response types, game entities, mathematical vectors, newtype wrappers for type safety.

```rust
#[derive(Debug, Clone)]
struct User {
    username: String,
    email: String,
    age: u32,
    active: bool,
}

impl User {
    fn new(username: &str, email: &str, age: u32) -> Self {
        User {
            username: username.to_string(),
            email: email.to_string(),
            age,
            active: true,
        }
    }

    fn is_adult(&self) -> bool {
        self.age >= 18
    }
}

fn main() {
    let user = User::new("alice", "alice@example.com", 30);
    println!("{:?}", user);
    println!("Adult: {}", user.is_adult()); // Output: Adult: true
}
```

---

### `enum`

| | |
|---|---|
| **Purpose** | Defines a type that can be one of several variants |
| **Category** | Types |

**Explanation:**
Rust enums are **algebraic data types** — each variant can carry different data. This makes them far more powerful than enums in C or Java. The standard library's `Option<T>` (Some/None) and `Result<T, E>` (Ok/Err) are enums. The compiler ensures every variant is handled in `match`.

**Real-world usage:**
`Option` for nullable values, `Result` for errors, state machines, command enums in CLI tools, AST nodes in compilers, message types in actor systems.

```rust
#[derive(Debug)]
enum Command {
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(u8, u8, u8),
    Quit,
}

fn execute(cmd: Command) {
    match cmd {
        Command::Move { x, y } => println!("Move to ({x}, {y})"),
        Command::Write(text)   => println!("Write: {text}"),
        Command::ChangeColor(r, g, b) => println!("Color: #{r:02X}{g:02X}{b:02X}"),
        Command::Quit          => println!("Quit"),
    }
}

fn main() {
    execute(Command::Move { x: 10, y: 20 });
    execute(Command::Write("hello".to_string()));
    execute(Command::ChangeColor(255, 128, 0));
    execute(Command::Quit);
}
```

---

### `impl`

| | |
|---|---|
| **Purpose** | Implements methods or traits for a type |
| **Category** | Types / OOP |

**Explanation:**
`impl` attaches behaviour to structs and enums. You can have multiple `impl` blocks for one type. Methods take `self`, `&self`, or `&mut self` as the first parameter — consuming, borrowing, or mutably borrowing the value respectively. Associated functions (like `new`) don't take `self` and act like static methods.

**Real-world usage:**
Every non-trivial Rust type uses `impl`. Constructors, getters/setters, business logic methods, trait implementations.

```rust
struct Rectangle {
    width: f64,
    height: f64,
}

impl Rectangle {
    // Associated function (constructor)
    fn new(width: f64, height: f64) -> Self {
        Rectangle { width, height }
    }

    // Method — borrows self
    fn area(&self) -> f64 {
        self.width * self.height
    }

    fn perimeter(&self) -> f64 {
        2.0 * (self.width + self.height)
    }

    // Method — mutably borrows self
    fn scale(&mut self, factor: f64) {
        self.width *= factor;
        self.height *= factor;
    }
}

fn main() {
    let mut r = Rectangle::new(4.0, 6.0);
    println!("Area: {}", r.area());       // 24
    r.scale(2.0);
    println!("New area: {}", r.area());   // 96
}
```

---

### `trait`

| | |
|---|---|
| **Purpose** | Defines a shared interface (similar to interfaces or type classes) |
| **Category** | Traits |

**Explanation:**
Traits define a set of methods that a type must implement to "satisfy" the trait. They are Rust's primary polymorphism mechanism. Unlike OOP inheritance, traits are opt-in and composable. A type can implement many traits. Traits can have **default implementations** and **associated types**.

**Real-world usage:**
`Display` for formatting, `Iterator` for custom iteration, `From`/`Into` for conversions, `Serialize`/`Deserialize` from Serde, `Read`/`Write` for I/O.

```rust
trait Animal {
    fn name(&self) -> &str;
    fn sound(&self) -> &str;

    // Default implementation
    fn introduce(&self) -> String {
        format!("I am {} and I say {}!", self.name(), self.sound())
    }
}

struct Dog { name: String }
struct Cat { name: String }

impl Animal for Dog {
    fn name(&self) -> &str { &self.name }
    fn sound(&self) -> &str { "woof" }
}

impl Animal for Cat {
    fn name(&self) -> &str { &self.name }
    fn sound(&self) -> &str { "meow" }
}

fn print_animal(a: &dyn Animal) {
    println!("{}", a.introduce());
}

fn main() {
    let dog = Dog { name: "Rex".to_string() };
    let cat = Cat { name: "Whiskers".to_string() };
    print_animal(&dog);  // I am Rex and I say woof!
    print_animal(&cat);  // I am Whiskers and I say meow!
}
```

---

### `type`

| | |
|---|---|
| **Purpose** | Creates a type alias |
| **Category** | Types |

**Explanation:**
`type` gives an existing type a new name. It doesn't create a new type — just an alias. The most common use in real Rust projects is defining a crate-wide `Result` type that has the error type pre-filled, so you don't have to repeat it everywhere.

**Real-world usage:**
Every Rust library defines `type Result<T> = std::result::Result<T, Error>`. Also used for shortening complex generic types in async code.

```rust
// Crate-wide Result alias — standard practice in libraries
type Result<T> = std::result::Result<T, String>;

// Shortening complex types
type Callback = Box<dyn Fn(i32) -> i32>;
type Grid = Vec<Vec<u8>>;

fn double(x: i32) -> i32 { x * 2 }

fn apply(f: Callback, val: i32) -> i32 {
    f(val)
}

fn divide(a: f64, b: f64) -> Result<f64> {
    if b == 0.0 { Err("Division by zero".into()) }
    else { Ok(a / b) }
}

fn main() {
    let cb: Callback = Box::new(double);
    println!("{}", apply(cb, 5));           // 10
    println!("{:?}", divide(10.0, 2.0));    // Ok(5.0)
}
```

---

### `where`

| | |
|---|---|
| **Purpose** | Adds trait bounds in a separate clause for readability |
| **Category** | Generics |

**Explanation:**
When a function has complex generic constraints, putting them in a `where` clause keeps the function signature clean. It's equivalent to inline bounds but much more readable for multiple constraints.

**Real-world usage:**
Library code with complex generics, trait implementations with multiple bounds, async functions.

```rust
use std::fmt::{Debug, Display};

// Without where — hard to read
fn print_v1<T: Display + Debug + Clone>(val: T) {
    println!("{val}");
}

// With where — clean and readable
fn print_v2<T>(val: T)
where
    T: Display + Debug + Clone,
{
    println!("{:?} = {}", val.clone(), val);
}

fn largest<T>(list: &[T]) -> &T
where
    T: PartialOrd,
{
    let mut l = &list[0];
    for item in list {
        if item > l { l = item; }
    }
    l
}

fn main() {
    print_v2(42);
    println!("Max: {}", largest(&[3, 1, 4, 1, 5, 9])); // 9
}
```

---

### `dyn`

| | |
|---|---|
| **Purpose** | Marks a trait object for dynamic dispatch |
| **Category** | Types / Polymorphism |

**Explanation:**
`dyn Trait` creates a **trait object** — a pointer to any type implementing `Trait`, resolved at runtime. This enables heterogeneous collections and plugin-style systems. The trade-off versus static dispatch (`impl Trait`) is a small vtable lookup overhead, similar to virtual methods in C++.

**Real-world usage:**
Collections of mixed types (`Vec<Box<dyn Widget>>`), plugin systems, callback registries, error types (`Box<dyn Error>`).

```rust
trait Drawable {
    fn draw(&self);
    fn area(&self) -> f64;
}

struct Circle(f64);
struct Square(f64);

impl Drawable for Circle {
    fn draw(&self) { println!("Drawing circle r={}", self.0); }
    fn area(&self) -> f64 { std::f64::consts::PI * self.0 * self.0 }
}

impl Drawable for Square {
    fn draw(&self) { println!("Drawing square s={}", self.0); }
    fn area(&self) -> f64 { self.0 * self.0 }
}

fn main() {
    // Heterogeneous collection — different types, same trait
    let shapes: Vec<Box<dyn Drawable>> = vec![
        Box::new(Circle(3.0)),
        Box::new(Square(4.0)),
        Box::new(Circle(1.5)),
    ];

    for shape in &shapes {
        shape.draw();
        println!("  area = {:.2}", shape.area());
    }
}
```

---

### `Self`

| | |
|---|---|
| **Purpose** | Refers to the current type inside `impl` or `trait` blocks |
| **Category** | Types |

**Explanation:**
`Self` (capital S) is a type alias for the type being implemented. Using `Self` in constructors and trait methods makes the code work for any implementing type without hardcoding the type name.

**Real-world usage:**
Builder patterns, constructors in traits, method chaining, and `Clone` implementations.

```rust
#[derive(Debug, Clone)]
struct Config {
    host: String,
    port: u16,
    debug: bool,
}

impl Config {
    fn new() -> Self {  // Self = Config
        Self {
            host: "localhost".to_string(),
            port: 8080,
            debug: false,
        }
    }

    fn host(mut self, h: &str) -> Self {
        self.host = h.to_string();
        self // return Self for method chaining
    }

    fn port(mut self, p: u16) -> Self {
        self.port = p;
        self
    }
}

fn main() {
    let cfg = Config::new().host("0.0.0.0").port(3000);
    println!("{cfg:?}");
}
```

---

## 5. Traits & Generics

### Generics `<T>`

| | |
|---|---|
| **Purpose** | Write code that works for many types |
| **Category** | Generics |

**Explanation:**
Generics allow you to write functions and types parameterised by a type. At compile time, Rust **monomorphises** generics — generating a concrete version for each type used. This means generics are completely zero-cost at runtime.

**Real-world usage:**
Standard library containers (`Vec<T>`, `HashMap<K, V>`), `Option<T>`, `Result<T, E>`, and any reusable data structure or algorithm.

```rust
// Generic function
fn first<T>(list: &[T]) -> Option<&T> {
    list.first()
}

// Generic struct
#[derive(Debug)]
struct Pair<T> {
    first: T,
    second: T,
}

impl<T: std::fmt::Display> Pair<T> {
    fn new(first: T, second: T) -> Self {
        Pair { first, second }
    }

    fn print(&self) {
        println!("({}, {})", self.first, self.second);
    }
}

fn main() {
    let p = Pair::new(10, 20);
    p.print(); // Output: (10, 20)

    let words = vec!["apple", "banana"];
    println!("{:?}", first(&words)); // Some("apple")
}
```

---

### `impl Trait` (return position)

| | |
|---|---|
| **Purpose** | Returns an opaque type that implements a trait |
| **Category** | Generics / Traits |

**Explanation:**
`-> impl Trait` lets functions return a concrete type without naming it. The compiler knows the type at compile time (zero overhead), but callers only see the trait interface. Especially useful for returning complex iterator chains or closures.

**Real-world usage:**
Returning iterator adapters, async functions (implicitly), closures from factory functions.

```rust
fn evens_up_to(n: u32) -> impl Iterator<Item = u32> {
    (0..n).filter(|x| x % 2 == 0)
}

fn make_adder(x: i32) -> impl Fn(i32) -> i32 {
    move |y| x + y
}

fn main() {
    let evens: Vec<u32> = evens_up_to(10).collect();
    println!("{evens:?}"); // [0, 2, 4, 6, 8]

    let add5 = make_adder(5);
    println!("{}", add5(3)); // 8
}
```

---

## 6. Ownership & Borrowing

### `ref`

| | |
|---|---|
| **Purpose** | Binds by reference in a pattern |
| **Category** | Ownership |

**Explanation:**
In a `match` or `let` destructuring, `ref` prevents the matched value from being **moved** into the binding — instead creating a reference. This is useful when you want to inspect parts of a value without consuming it.

**Real-world usage:**
Matching on `String` fields inside structs when you only need to read them, avoiding moves in complex destructuring.

```rust
fn main() {
    let s = String::from("hello");
    let ref r = s;        // r is &String; s is still valid
    println!("{r}");      // hello
    println!("{s}");      // s is still usable

    // In match
    let tuple = (String::from("world"), 42);
    match &tuple {
        (ref text, num) => println!("{text} {num}"),
    }
}
```

---

### `move`

| | |
|---|---|
| **Purpose** | Forces a closure to take ownership of captured variables |
| **Category** | Ownership / Closures |

**Explanation:**
By default, closures borrow variables from their environment. `move` forces the closure to **take ownership** of all captured variables. This is required when the closure will outlive the current scope — as happens when spawning threads or returning closures from functions.

**Real-world usage:**
`std::thread::spawn`, async task spawning with Tokio, returning closures from functions, event callbacks.

```rust
use std::thread;

fn main() {
    let message = String::from("Hello from thread!");

    let handle = thread::spawn(move || {
        // message moved into closure — owned by this thread
        println!("{message}");
    });

    // println!("{message}"); // ERROR: message was moved

    handle.join().unwrap();
}
```

---

### `box` / `Box<T>`

| | |
|---|---|
| **Purpose** | Allocates a value on the heap |
| **Category** | Smart pointers / Ownership |

**Explanation:**
`Box<T>` stores a value on the heap and gives you a pointer to it. This is needed when: (1) the size of a type is unknown at compile time (recursive types), (2) you want a large value on the heap to avoid stack overflow, or (3) you need a trait object (`Box<dyn Trait>`).

**Real-world usage:**
Recursive data structures (linked lists, trees), trait objects, large data structures, and FFI.

```rust
// Recursive type — impossible without Box (infinite size)
#[derive(Debug)]
enum List {
    Cons(i32, Box<List>),
    Nil,
}

use List::{Cons, Nil};

fn main() {
    let list = Cons(1, Box::new(Cons(2, Box::new(Cons(3, Box::new(Nil))))));
    println!("{list:?}");

    // Trait object
    trait Speak { fn speak(&self); }
    struct Dog;
    impl Speak for Dog { fn speak(&self) { println!("Woof!"); } }

    let animal: Box<dyn Speak> = Box::new(Dog);
    animal.speak();
}
```

---

## 7. Modules & Visibility

### `mod`

| | |
|---|---|
| **Purpose** | Declares or defines a module |
| **Category** | Modules |

**Explanation:**
`mod` organises code into namespaces. `mod foo { }` defines an inline module. `mod foo;` (no braces) loads `foo.rs` or `foo/mod.rs` from the filesystem. Modules are Rust's compilation unit and privacy boundary.

**Real-world usage:**
Every project larger than one file uses modules. Standard structure: `mod models`, `mod handlers`, `mod utils`.

```rust
mod math {
    pub fn square(x: i32) -> i32 { x * x }
    pub fn cube(x: i32) -> i32   { x * x * x }

    pub mod trig {
        pub fn sin_degrees(deg: f64) -> f64 {
            deg.to_radians().sin()
        }
    }
}

fn main() {
    println!("{}", math::square(5));          // 25
    println!("{}", math::cube(3));            // 27
    println!("{:.3}", math::trig::sin_degrees(90.0)); // 1.000
}
```

---

### `use`

| | |
|---|---|
| **Purpose** | Brings a path into scope |
| **Category** | Modules |

**Explanation:**
`use` imports a name so you don't have to write its full path every time. It supports nested braces for multiple imports from the same path, `self` for the module itself, and `as` for renaming.

**Real-world usage:**
Used in every non-trivial Rust file. `use std::collections::HashMap` and `use std::io::{self, Read, Write}` are common.

```rust
use std::collections::{HashMap, HashSet};
use std::io::{self, Write};
use std::fmt::Display as Disp;

fn show<T: Disp>(val: T) {
    println!("{val}");
}

fn main() {
    let mut map: HashMap<&str, i32> = HashMap::new();
    map.insert("apples", 5);
    map.insert("bananas", 3);

    let mut set: HashSet<i32> = HashSet::new();
    set.insert(1);

    show("hello world");
    print!("Enter value: ");
    io::stdout().flush().unwrap();
}
```

---

### `pub`

| | |
|---|---|
| **Purpose** | Makes an item publicly accessible outside its module |
| **Category** | Modules / Visibility |

**Explanation:**
Rust is **private by default**. Everything — structs, fields, functions, modules — is private unless marked `pub`. This enforces API hygiene: you decide exactly what forms your public interface. Fine-grained visibility modifiers:
- `pub` — public to everyone
- `pub(crate)` — public within the current crate only
- `pub(super)` — public to the parent module
- `pub(in path)` — public to a specific path

**Real-world usage:**
Library APIs, module interfaces, and field access control.

```rust
mod auth {
    pub struct User {
        pub username: String,
        password_hash: String, // private — callers can't access directly
    }

    impl User {
        pub fn new(username: &str, password: &str) -> Self {
            User {
                username: username.to_string(),
                password_hash: format!("hash_{password}"), // simplified
            }
        }

        pub fn verify(&self, password: &str) -> bool {
            self.password_hash == format!("hash_{password}")
        }

        pub(crate) fn admin_reset(&mut self) {
            self.password_hash = "hash_reset".to_string();
        }
    }
}

fn main() {
    let user = auth::User::new("alice", "secret");
    println!("{}", user.username);        // alice
    println!("{}", user.verify("secret")); // true
    // user.password_hash  // ERROR: private field
}
```

---

### `super` / `crate`

| | |
|---|---|
| **Purpose** | Path anchors for navigating the module tree |
| **Category** | Modules |

**Explanation:**
- `super` refers to the **parent module** — useful in submodule tests that need parent code.
- `crate` refers to the **crate root** — an absolute path anchor that avoids path ambiguity in deep module trees. `pub(crate)` makes something visible within the crate only.

```rust
mod network {
    pub fn connect() { println!("Connected"); }

    pub mod handlers {
        pub fn handle() {
            super::connect(); // parent module function
        }

        pub fn handle_absolute() {
            crate::network::connect(); // absolute path from crate root
        }
    }
}

fn main() {
    network::handlers::handle();
    network::handlers::handle_absolute();
}
```

---

### `as`

| | |
|---|---|
| **Purpose** | Casts a value to another type, or renames an import |
| **Category** | Misc / Modules |

**Explanation:**
`as` has two uses: (1) **type casting** between numeric types (may truncate or extend), and (2) **renaming imports** to avoid name collisions.

**Real-world usage:**
Numeric casting in graphics/systems code, resolving name conflicts between two crates exporting the same name.

```rust
use std::fmt::Display as Disp;
use std::collections::HashMap as Map;

fn show(v: &dyn Disp) { println!("{v}"); }

fn main() {
    let x: i64 = 1000;
    let y = x as i16;       // 1000 — fits
    let z = 300i32 as u8;   // 44 — truncated (300 % 256)
    let f = 3.99f64 as i32; // 3 — truncated toward zero

    println!("{y} {z} {f}"); // 1000 44 3

    let mut m: Map<&str, i32> = Map::new();
    m.insert("score", 100);
    show(&m["score"]);
}
```

---

## 8. Error Handling

### `?` operator

| | |
|---|---|
| **Purpose** | Propagates errors early (the try operator) |
| **Category** | Error handling |

**Explanation:**
`?` is the most-used error handling tool in Rust. When placed after an expression returning `Result<T, E>` or `Option<T>`, it either unwraps the success value or **immediately returns** the error from the current function. It also calls `.into()` on the error, enabling automatic type conversion. This makes fallible code read almost like happy-path code.

**Real-world usage:**
File I/O, network requests, database queries, JSON parsing — any chain of operations that can fail.

```rust
use std::fs;
use std::io;
use std::num::ParseIntError;

#[derive(Debug)]
enum AppError {
    Io(io::Error),
    Parse(ParseIntError),
}

impl From<io::Error> for AppError {
    fn from(e: io::Error) -> Self { AppError::Io(e) }
}

impl From<ParseIntError> for AppError {
    fn from(e: ParseIntError) -> Self { AppError::Parse(e) }
}

fn read_number_from_file(path: &str) -> Result<i32, AppError> {
    let content = fs::read_to_string(path)?;  // io::Error auto-converted
    let num = content.trim().parse::<i32>()?; // ParseIntError auto-converted
    Ok(num * 2)
}

fn main() {
    match read_number_from_file("number.txt") {
        Ok(n)  => println!("Got: {n}"),
        Err(e) => println!("Error: {e:?}"),
    }
}
```

---

### `Option<T>`

| | |
|---|---|
| **Purpose** | Represents a value that may or may not exist |
| **Category** | Error handling / Types |

**Explanation:**
`Option<T>` is Rust's solution to the billion-dollar mistake — null pointers. There is no `null` in safe Rust. Instead, you use `Some(value)` when a value exists and `None` when it doesn't. The compiler forces you to handle both cases, eliminating null pointer exceptions.

**Real-world usage:**
Optional struct fields, search/find operations, optional configuration values, nullable database columns.

```rust
fn find_user(id: u32) -> Option<String> {
    match id {
        1 => Some("Alice".to_string()),
        2 => Some("Bob".to_string()),
        _ => None,
    }
}

fn main() {
    // Explicit match
    match find_user(1) {
        Some(name) => println!("Found: {name}"),
        None       => println!("Not found"),
    }

    // Concise methods
    let name = find_user(2).unwrap_or("Guest".to_string());
    println!("{name}"); // Bob

    let length = find_user(3).map(|n| n.len());
    println!("{length:?}"); // None

    // ? operator works with Option in functions returning Option
    let upper = find_user(1).map(|n| n.to_uppercase());
    println!("{upper:?}"); // Some("ALICE")
}
```

---

### `Result<T, E>`

| | |
|---|---|
| **Purpose** | Represents success (`Ok`) or failure (`Err`) |
| **Category** | Error handling / Types |

**Explanation:**
`Result<T, E>` is Rust's primary error handling type. Functions that can fail return `Result` instead of throwing exceptions. This makes error handling explicit — callers must acknowledge that a function can fail. Combined with `?`, error propagation is concise. Combined with `match`, error handling is exhaustive.

**Real-world usage:**
Literally every I/O operation, every network call, every parse operation in Rust returns `Result`.

```rust
#[derive(Debug)]
struct Config {
    host: String,
    port: u16,
}

impl Config {
    fn from_args(args: &[String]) -> Result<Self, String> {
        if args.len() < 3 {
            return Err("Usage: program <host> <port>".to_string());
        }
        let port = args[2].parse::<u16>()
            .map_err(|e| format!("Invalid port: {e}"))?;

        Ok(Config {
            host: args[1].clone(),
            port,
        })
    }
}

fn main() {
    let args = vec!["app".to_string(), "localhost".to_string(), "8080".to_string()];
    match Config::from_args(&args) {
        Ok(cfg) => println!("Config: {:?}", cfg),
        Err(e)  => eprintln!("Error: {e}"),
    }
}
```

---

### `TryFrom` / `TryInto`

| | |
|---|---|
| **Purpose** | Fallible type conversions that return `Result` |
| **Category** | Error handling / Conversions |

**Explanation:**
When a conversion can fail (e.g., fitting a large number into a smaller type), use `TryFrom`/`TryInto`. Implementing `TryFrom<T>` automatically provides `TryInto` for free.

```rust
use std::convert::TryFrom;

#[derive(Debug)]
struct PositiveInt(u32);

impl TryFrom<i32> for PositiveInt {
    type Error = String;

    fn try_from(value: i32) -> Result<Self, Self::Error> {
        if value < 0 {
            Err(format!("{value} is negative"))
        } else {
            Ok(PositiveInt(value as u32))
        }
    }
}

fn main() {
    println!("{:?}", PositiveInt::try_from(42));   // Ok(PositiveInt(42))
    println!("{:?}", PositiveInt::try_from(-5));   // Err("-5 is negative")

    // TryInto (auto-implemented)
    use std::convert::TryInto;
    let big: i64 = 500;
    let small: Result<i8, _> = big.try_into();
    println!("{small:?}"); // Err(...)
}
```

---

## 9. Async Programming

### `async` / `await`

| | |
|---|---|
| **Purpose** | Write asynchronous code that looks synchronous |
| **Category** | Async |

**Explanation:**
`async fn` returns a `Future` — a value representing a computation that hasn't happened yet. `.await` suspends execution until the Future completes. An async **executor** (like Tokio or async-std) drives the futures. Rust's async is **zero-cost** — no heap allocation unless you explicitly box the future.

**Real-world usage:**
Web servers (Axum, Actix), HTTP clients (reqwest), database drivers (sqlx), file I/O, WebSocket handlers.

```rust
use tokio::time::{sleep, Duration};

async fn fetch_data(url: &str) -> String {
    // Simulate network delay
    sleep(Duration::from_millis(100)).await;
    format!("Data from: {url}")
}

async fn run_parallel() {
    // Run two futures concurrently
    let (a, b) = tokio::join!(
        fetch_data("https://api.example.com/users"),
        fetch_data("https://api.example.com/posts"),
    );
    println!("{a}");
    println!("{b}");
}

#[tokio::main]
async fn main() {
    let data = fetch_data("https://example.com").await;
    println!("{data}");
    run_parallel().await;
}
```

---

### `Pin<T>`

| | |
|---|---|
| **Purpose** | Prevents a value from being moved in memory |
| **Category** | Async / Advanced types |

**Explanation:**
`Pin` guarantees that a value will not be moved after being pinned. This is required for **self-referential structs** — which async state machines often are. When you `.await` a future, the compiler generates a struct that holds references to its own fields; if it moved, those references would be invalid.

**Real-world usage:**
Implementing custom `Future` types, `Pin<Box<dyn Future>>` as a boxed future type, and low-level async machinery.

```rust
use std::pin::Pin;
use std::future::Future;

// Standard boxed future type used in async trait workarounds
type BoxFuture<'a, T> = Pin<Box<dyn Future<Output = T> + Send + 'a>>;

fn wrap_future<T: Send + 'static>(
    fut: impl Future<Output = T> + Send + 'static
) -> BoxFuture<'static, T> {
    Box::pin(fut)
}

async fn compute() -> i32 { 42 }

#[tokio::main]
async fn main() {
    let boxed = wrap_future(compute());
    let result = boxed.await;
    println!("{result}"); // 42
}
```

---

## 10. Unsafe & FFI

### `unsafe`

| | |
|---|---|
| **Purpose** | Opts into operations the compiler cannot verify as safe |
| **Category** | Unsafe |

**Explanation:**
`unsafe` blocks allow five operations not permitted in safe Rust: dereferencing raw pointers, calling unsafe functions, accessing mutable statics, implementing unsafe traits, and accessing union fields. `unsafe` doesn't turn off the borrow checker — it only unlocks these specific operations. The programmer takes responsibility for upholding safety invariants.

**Real-world usage:**
OS kernels, embedded systems, JNI (Java interop), SIMD intrinsics, and wrapping C libraries. The standard library itself uses `unsafe` internally to build safe abstractions.

```rust
fn main() {
    // Raw pointer manipulation
    let mut val = 42;
    let ptr = &mut val as *mut i32;

    unsafe {
        *ptr += 8;
        println!("{}", *ptr); // 50
    }

    // Calling an unsafe function
    unsafe fn dangerous() -> i32 { 99 }
    let result = unsafe { dangerous() };
    println!("{result}");
}
```

---

### `extern`

| | |
|---|---|
| **Purpose** | Links to external code (C FFI) or specifies calling convention |
| **Category** | FFI |

**Explanation:**
`extern "C"` declares foreign functions using C's calling convention. Calling them requires `unsafe` because Rust can't verify foreign code's safety guarantees. `extern "Rust"` can expose Rust functions to C.

**Real-world usage:**
Wrapping C libraries (OpenSSL, SQLite, system APIs), game engine plugins, embedding Rust in Python/Ruby/C projects.

```rust
extern "C" {
    fn abs(n: i32) -> i32;
    fn strlen(s: *const u8) -> usize;
}

// Expose a Rust function to C
#[no_mangle]
pub extern "C" fn rust_add(a: i32, b: i32) -> i32 {
    a + b
}

fn main() {
    let x = unsafe { abs(-42) };
    println!("{x}"); // 42
}
```

---

## 11. Standard Library Types

### `Vec<T>`

| | |
|---|---|
| **Purpose** | A growable heap-allocated array |
| **Category** | Collections |

**Explanation:**
`Vec<T>` is the most common collection in Rust. It stores elements contiguously in heap memory and grows automatically. Provides O(1) push/pop (amortised), O(1) random access, and O(n) insert/remove at arbitrary positions.

```rust
fn main() {
    let mut v: Vec<i32> = Vec::new();
    v.push(10);
    v.push(20);
    v.push(30);

    // Iterate
    for x in &v { print!("{x} "); }
    println!();

    // Functional style
    let doubled: Vec<i32> = v.iter().map(|&x| x * 2).collect();
    println!("{doubled:?}"); // [20, 40, 60]

    // Slices
    println!("{:?}", &v[1..]); // [20, 30]
}
```

---

### `HashMap<K, V>`

| | |
|---|---|
| **Purpose** | Hash-based key-value map |
| **Category** | Collections |

**Explanation:**
`HashMap` stores key-value pairs with O(1) average lookup, insert, and delete. The default hasher is SipHash (DoS-resistant). For maximum speed in non-security-sensitive contexts, use `AHashMap` from the `ahash` crate.

```rust
use std::collections::HashMap;

fn word_count(text: &str) -> HashMap<&str, usize> {
    let mut counts = HashMap::new();
    for word in text.split_whitespace() {
        *counts.entry(word).or_insert(0) += 1;
    }
    counts
}

fn main() {
    let mut scores: HashMap<String, Vec<i32>> = HashMap::new();
    scores.entry("Alice".to_string()).or_insert_with(Vec::new).push(95);
    scores.entry("Alice".to_string()).or_insert_with(Vec::new).push(87);

    println!("{scores:?}");

    let counts = word_count("hello world hello rust world hello");
    println!("{counts:?}");
}
```

---

## 12. Concurrency Primitives

### `Arc<T>`

| | |
|---|---|
| **Purpose** | Atomically reference-counted smart pointer for shared ownership across threads |
| **Category** | Concurrency |

**Explanation:**
`Arc` (Atomic Reference Count) allows multiple owners of the same data across threads. Unlike `Rc`, its reference count operations are atomic — safe for multi-threaded use. Combine with `Mutex` for shared mutable state.

```rust
use std::sync::{Arc, Mutex};
use std::thread;

fn main() {
    let counter = Arc::new(Mutex::new(0));
    let mut handles = vec![];

    for _ in 0..10 {
        let c = Arc::clone(&counter);
        let h = thread::spawn(move || {
            let mut num = c.lock().unwrap();
            *num += 1;
        });
        handles.push(h);
    }

    for h in handles { h.join().unwrap(); }
    println!("Counter: {}", *counter.lock().unwrap()); // 10
}
```

---

### `Mutex<T>`

| | |
|---|---|
| **Purpose** | Mutual exclusion lock for safe shared mutable state |
| **Category** | Concurrency |

**Explanation:**
`Mutex` ensures only one thread accesses the protected data at a time. `lock()` blocks until the lock is acquired and returns a `MutexGuard` that automatically releases the lock when dropped. **Poisoning**: if a thread panics while holding the lock, the mutex is "poisoned" — subsequent `lock()` calls return `Err`.

```rust
use std::sync::Mutex;

fn main() {
    let m = Mutex::new(vec![1, 2, 3]);

    {
        let mut data = m.lock().unwrap();
        data.push(4);
    } // Lock released here (MutexGuard dropped)

    println!("{:?}", m.lock().unwrap()); // [1, 2, 3, 4]
}
```

---

### `Send` / `Sync`

| | |
|---|---|
| **Purpose** | Marker traits that prove thread safety at compile time |
| **Category** | Concurrency / Marker traits |

**Explanation:**
- `Send`: a type can be **transferred** to another thread. Most types are `Send`. `Rc<T>` is `!Send` (not thread-safe reference counting).
- `Sync`: a type can be **shared by reference** between threads. `Arc<T>` requires `T: Sync`. `Mutex<T>` is `Sync` even if `T` isn't.

The compiler automatically implements these for types that qualify. This provides **data-race freedom at compile time** — a unique Rust guarantee.

```rust
use std::sync::Arc;
use std::thread;

fn require_send<T: Send>(_: T) {}
fn require_sync<T: Sync>(_: &T) {}

fn main() {
    // Arc<Vec<i32>> is both Send and Sync
    let data = Arc::new(vec![1, 2, 3]);
    require_sync(&data);

    let handles: Vec<_> = (0..3).map(|_| {
        let d = Arc::clone(&data);
        thread::spawn(move || println!("{d:?}"))
    }).collect();

    for h in handles { h.join().unwrap(); }
}
```

---

## 13. Smart Pointers

### `Rc<T>`

| | |
|---|---|
| **Purpose** | Single-threaded reference-counted smart pointer |
| **Category** | Smart pointers |

**Explanation:**
`Rc` enables **shared ownership** in single-threaded contexts. Multiple `Rc` pointers can point to the same data. The data is freed when the last `Rc` is dropped. Use `RefCell<T>` inside `Rc` for interior mutability.

```rust
use std::rc::Rc;
use std::cell::RefCell;

fn main() {
    // Shared ownership
    let a = Rc::new(5);
    let b = Rc::clone(&a);
    let c = Rc::clone(&a);

    println!("Count: {}", Rc::strong_count(&a)); // 3
    println!("{a} {b} {c}");

    // Rc<RefCell<T>> — shared mutable data
    let shared = Rc::new(RefCell::new(vec![1, 2]));
    let clone1 = Rc::clone(&shared);
    let clone2 = Rc::clone(&shared);

    clone1.borrow_mut().push(3);
    clone2.borrow_mut().push(4);
    println!("{:?}", shared.borrow()); // [1, 2, 3, 4]
}
```

---

### `Cell<T>` / `RefCell<T>`

| | |
|---|---|
| **Purpose** | Interior mutability — mutate through a shared reference |
| **Category** | Smart pointers |

**Explanation:**
Normally you can't mutate through a `&T` (shared reference). `Cell` and `RefCell` allow **interior mutability** — mutation through a shared reference. `Cell` works by copying values. `RefCell` enforces borrow rules at **runtime** instead of compile time — panics if you violate them.

```rust
use std::cell::{Cell, RefCell};

struct Config {
    debug: Cell<bool>,        // cheap, copyable
    log_messages: RefCell<Vec<String>>, // heap data
}

impl Config {
    fn new() -> Self {
        Config {
            debug: Cell::new(false),
            log_messages: RefCell::new(vec![]),
        }
    }

    fn enable_debug(&self) {    // &self — not &mut self!
        self.debug.set(true);
    }

    fn log(&self, msg: &str) {
        self.log_messages.borrow_mut().push(msg.to_string());
    }
}

fn main() {
    let cfg = Config::new();
    cfg.enable_debug();
    cfg.log("System started");
    println!("{}", cfg.debug.get()); // true
    println!("{:?}", cfg.log_messages.borrow());
}
```

---

### `Deref` / `DerefMut`

| | |
|---|---|
| **Purpose** | Enables auto-dereferencing via the `*` operator |
| **Category** | Traits / Smart pointers |

**Explanation:**
Implementing `Deref` allows a type to behave like a reference to its inner type. This powers **deref coercion** — `&Box<String>` automatically coerces to `&String`, which coerces to `&str`. This is why you can pass `&String` where `&str` is expected.

```rust
use std::ops::{Deref, DerefMut};

struct MyBox<T>(T);

impl<T> MyBox<T> {
    fn new(x: T) -> Self { MyBox(x) }
}

impl<T> Deref for MyBox<T> {
    type Target = T;
    fn deref(&self) -> &T { &self.0 }
}

fn hello(name: &str) {
    println!("Hello, {name}!");
}

fn main() {
    let s = MyBox::new(String::from("world"));
    hello(&s);          // &MyBox<String> → &String → &str (auto deref)
    println!("{}", *s); // world
}
```

---

## 14. Macros & Attributes

### `macro_rules!`

| | |
|---|---|
| **Purpose** | Defines a declarative (pattern-based) macro |
| **Category** | Macros |

**Explanation:**
`macro_rules!` creates macros that expand at compile time based on pattern matching. Macros can accept variable numbers of arguments, generate repetitive code, and create DSLs. The standard `vec!`, `println!`, `assert!`, and `dbg!` macros are all `macro_rules!` macros.

```rust
macro_rules! create_function {
    ($func_name:ident) => {
        fn $func_name() {
            println!("Function {:?} called", stringify!($func_name));
        }
    };
}

macro_rules! max {
    ($x:expr) => { $x };
    ($x:expr, $($y:expr),+) => {
        std::cmp::max($x, max!($($y),+))
    };
}

create_function!(foo);
create_function!(bar);

fn main() {
    foo(); // Function "foo" called
    bar(); // Function "bar" called
    println!("{}", max!(3, 7, 2, 9, 1)); // 9
}
```

---

### `#[derive(...)]`

| | |
|---|---|
| **Purpose** | Auto-implements standard traits via a proc-macro attribute |
| **Category** | Attributes / Macros |

**Explanation:**
`#[derive]` generates trait implementations automatically. This saves massive amounts of boilerplate. The standard library provides: `Debug`, `Clone`, `Copy`, `PartialEq`, `Eq`, `PartialOrd`, `Ord`, `Hash`, `Default`. Third-party crates like Serde add `Serialize` and `Deserialize`.

```rust
use std::collections::HashMap;

#[derive(Debug, Clone, PartialEq, Eq, Hash)]
struct Point {
    x: i32,
    y: i32,
}

#[derive(Debug, Default)]
struct Stats {
    count: u32,
    total: f64,
    labels: Vec<String>,
}

fn main() {
    let p1 = Point { x: 1, y: 2 };
    let p2 = p1.clone();
    println!("{p1:?}");          // Point { x: 1, y: 2 }
    println!("Equal: {}", p1 == p2); // true

    // Using as a HashMap key (requires Hash + Eq)
    let mut map: HashMap<Point, &str> = HashMap::new();
    map.insert(p1, "origin-ish");

    let stats = Stats::default(); // requires Default
    println!("{stats:?}");
}
```

---

## 15. Marker Traits

### `Copy`

| | |
|---|---|
| **Purpose** | Values are copied (not moved) on assignment |
| **Category** | Marker traits |

**Explanation:**
Types implementing `Copy` are **duplicated** when assigned or passed to functions, rather than moved. The copy is always a bit-for-bit copy of stack memory — so only types with no heap allocation can be `Copy`. All primitives (`i32`, `f64`, `bool`, `char`) are `Copy`. `Copy` requires `Clone`.

```rust
#[derive(Copy, Clone, Debug)]
struct Point2D { x: f32, y: f32 }

fn distance(a: Point2D, b: Point2D) -> f32 {
    ((b.x - a.x).powi(2) + (b.y - a.y).powi(2)).sqrt()
}

fn main() {
    let p1 = Point2D { x: 0.0, y: 0.0 };
    let p2 = Point2D { x: 3.0, y: 4.0 };

    let dist = distance(p1, p2); // p1 and p2 copied, not moved
    println!("{p1:?} is still usable!"); // works because Copy
    println!("Distance: {dist}"); // 5
}
```

---

### `Clone`

| | |
|---|---|
| **Purpose** | Explicit deep copy of a heap-allocated value |
| **Category** | Traits |

**Explanation:**
`Clone` provides an explicit `.clone()` method. Unlike `Copy` (implicit), cloning is explicit — signalling a potentially expensive heap allocation. You derive `Clone` on data structs that need to be duplicated. The Serde `Serialize`/`Deserialize` often require `Clone`.

```rust
#[derive(Debug, Clone)]
struct Database {
    name: String,
    records: Vec<String>,
}

fn backup(db: &Database) -> Database {
    db.clone() // explicit deep copy
}

fn main() {
    let db = Database {
        name: "production".to_string(),
        records: vec!["user1".to_string(), "user2".to_string()],
    };
    let backup_db = backup(&db);
    println!("Original: {db:?}");
    println!("Backup: {backup_db:?}");
}
```

---

### `Drop`

| | |
|---|---|
| **Purpose** | Custom destructor — called when a value goes out of scope |
| **Category** | Traits |

**Explanation:**
Implement `Drop` to run cleanup code when a value is destroyed. Rust **guarantees** `drop` is always called (barring `mem::forget` or process abort). This powers RAII — Resource Acquisition Is Initialization — for file handles, locks, network connections, and database transactions.

```rust
struct DatabaseConnection {
    url: String,
}

impl DatabaseConnection {
    fn new(url: &str) -> Self {
        println!("Connecting to {url}");
        DatabaseConnection { url: url.to_string() }
    }

    fn query(&self, sql: &str) {
        println!("Query: {sql}");
    }
}

impl Drop for DatabaseConnection {
    fn drop(&mut self) {
        println!("Closing connection to {}", self.url);
    }
}

fn main() {
    let conn = DatabaseConnection::new("postgres://localhost/mydb");
    conn.query("SELECT * FROM users");
    // conn automatically dropped here — connection closed
}
// Output:
// Connecting to postgres://localhost/mydb
// Query: SELECT * FROM users
// Closing connection to postgres://localhost/mydb
```

---

## 16. Common Traits to Implement

### `Iterator`

| | |
|---|---|
| **Purpose** | Trait for lazy sequences with a `next()` method |
| **Category** | Traits |

**Explanation:**
Implementing `Iterator` on your type unlocks the entire iterator adapter library — `.map()`, `.filter()`, `.fold()`, `.take()`, `.zip()`, `.collect()`, and hundreds more. You only need to implement `next()`.

```rust
struct Fibonacci {
    a: u64,
    b: u64,
}

impl Fibonacci {
    fn new() -> Self { Fibonacci { a: 0, b: 1 } }
}

impl Iterator for Fibonacci {
    type Item = u64;

    fn next(&mut self) -> Option<u64> {
        let result = self.a;
        let next = self.a + self.b;
        self.a = self.b;
        self.b = next;
        Some(result) // infinite iterator
    }
}

fn main() {
    let fibs: Vec<u64> = Fibonacci::new().take(10).collect();
    println!("{fibs:?}"); // [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]

    let sum: u64 = Fibonacci::new().take(20).sum();
    println!("Sum of first 20 Fibonacci: {sum}");
}
```

---

### `Display` / `Debug`

| | |
|---|---|
| **Purpose** | Format a type as a human-readable or debug string |
| **Category** | Traits |

**Explanation:**
- `Debug` (used with `{:?}`) is for developer-facing output — always derive it.
- `Display` (used with `{}`) is for user-facing output — implement it on public types.

```rust
use std::fmt;

#[derive(Debug)]
struct Temperature {
    celsius: f64,
}

impl Temperature {
    fn new(celsius: f64) -> Self { Temperature { celsius } }
    fn to_fahrenheit(&self) -> f64 { self.celsius * 9.0 / 5.0 + 32.0 }
}

impl fmt::Display for Temperature {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        write!(f, "{:.1}°C ({:.1}°F)", self.celsius, self.to_fahrenheit())
    }
}

fn main() {
    let t = Temperature::new(36.6);
    println!("{t}");     // 36.6°C (97.9°F) — Display
    println!("{t:?}");   // Temperature { celsius: 36.6 } — Debug
    println!("{t:#?}");  // Pretty-printed Debug
}
```

---

### `From` / `Into`

| | |
|---|---|
| **Purpose** | Infallible type conversions |
| **Category** | Traits / Conversions |

**Explanation:**
Implementing `From<T>` automatically provides `Into` for free (via a blanket impl). The `?` operator uses `From` to convert between error types. `String::from("hello")` and `"hello".to_string()` both use this machinery.

```rust
#[derive(Debug)]
struct Metres(f64);

#[derive(Debug)]
struct Feet(f64);

impl From<Feet> for Metres {
    fn from(f: Feet) -> Self {
        Metres(f.0 * 0.3048)
    }
}

impl From<Metres> for Feet {
    fn from(m: Metres) -> Self {
        Feet(m.0 / 0.3048)
    }
}

fn main() {
    let m = Metres::from(Feet(100.0));
    println!("{m:.1?}"); // Metres(30.5)

    // Into works automatically
    let f: Feet = Metres(10.0).into();
    println!("{f:.1?}"); // Feet(32.8)
}
```

---

## 17. Lifetimes

### `'a` lifetime parameters

| | |
|---|---|
| **Purpose** | Name a lifetime to express how long a borrow is valid |
| **Category** | Ownership / Lifetimes |

**Explanation:**
Lifetimes tell the compiler how long references are valid relative to each other. In most cases the compiler infers lifetimes automatically (lifetime elision). You only need explicit annotations when the compiler can't figure it out — usually in structs holding references or functions returning references tied to inputs.

**Real-world usage:**
Structs holding string slices, functions returning the longest of two strings, iterators over borrowed data.

```rust
// Function returning a reference — must specify which input it comes from
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() { x } else { y }
}

// Struct holding a reference — needs lifetime annotation
#[derive(Debug)]
struct Excerpt<'a> {
    text: &'a str,
    author: &'a str,
}

impl<'a> Excerpt<'a> {
    fn summarise(&self) -> String {
        format!("'{}' — {}", &self.text[..50.min(self.text.len())], self.author)
    }
}

fn main() {
    let novel = String::from("Call me Ishmael. Some years ago...");
    let excerpt = Excerpt {
        text: &novel,
        author: "Herman Melville",
    };
    println!("{}", excerpt.summarise());
    println!("{}", longest("hello", "world!"));
}
```

---

### `'static` lifetime

| | |
|---|---|
| **Purpose** | Denotes data that lives for the entire program duration |
| **Category** | Lifetimes |

**Explanation:**
`'static` is the longest possible lifetime. String literals (`"hello"`) are `&'static str` — they're baked into the binary. Thread spawning requires `'static` bounds because threads can outlive the scope where they were created.

```rust
// String literals are 'static
fn get_greeting() -> &'static str {
    "Hello, World!" // 'static — lives in the binary
}

// Thread functions need 'static because thread may outlive caller
fn run_background_task(task_name: &'static str) {
    std::thread::spawn(move || {
        println!("Running: {task_name}");
    });
}

fn main() {
    println!("{}", get_greeting());
    run_background_task("sync-job");
    std::thread::sleep(std::time::Duration::from_millis(50));
}
```

---

## 18. Type Conversions

### Summary Table

| Trait | Direction | Fallible? | Use case |
|-------|-----------|-----------|----------|
| `From<T>` | `T → Self` | No | Common infallible conversions |
| `Into<T>` | `Self → T` | No | Auto-derived from `From` |
| `TryFrom<T>` | `T → Self` | Yes (`Result`) | Narrowing, validation |
| `TryInto<T>` | `Self → T` | Yes (`Result`) | Auto-derived from `TryFrom` |
| `AsRef<T>` | `&Self → &T` | No | Cheap reference conversion |
| `AsMut<T>` | `&mut Self → &mut T` | No | Cheap mutable reference conversion |
| `ToString` | `Self → String` | No | Auto-derived from `Display` |
| `FromStr` | `str → Self` | Yes | `"42".parse::<i32>()` |

```rust
use std::str::FromStr;

#[derive(Debug)]
struct Color {
    r: u8, g: u8, b: u8,
}

impl FromStr for Color {
    type Err = String;

    fn from_str(s: &str) -> Result<Self, Self::Err> {
        let parts: Vec<&str> = s.split(',').collect();
        if parts.len() != 3 {
            return Err(format!("Expected 'r,g,b', got '{s}'"));
        }
        Ok(Color {
            r: parts[0].trim().parse().map_err(|e| format!("{e}"))?,
            g: parts[1].trim().parse().map_err(|e| format!("{e}"))?,
            b: parts[2].trim().parse().map_err(|e| format!("{e}"))?,
        })
    }
}

fn main() {
    let c: Color = "255, 128, 0".parse().unwrap();
    println!("{c:?}"); // Color { r: 255, g: 128, b: 0 }
}
```

---

## Quick Reference Cheat Sheet

### Ownership Rules
1. Each value has exactly **one owner**.
2. When the owner goes out of scope, the value is **dropped**.
3. You can have **either** one mutable reference **or** any number of immutable references — never both at the same time.

### Choosing Between Smart Pointers

| Situation | Use |
|-----------|-----|
| Stack value, single owner | `T` (owned) |
| Borrow without ownership | `&T` / `&mut T` |
| Heap allocation | `Box<T>` |
| Shared ownership, single thread | `Rc<T>` |
| Shared ownership, multi-thread | `Arc<T>` |
| Shared mutation, single thread | `Rc<RefCell<T>>` |
| Shared mutation, multi-thread | `Arc<Mutex<T>>` |

### Error Handling Decision Tree

```
Function can fail?
├── No  → return T directly
└── Yes → return Result<T, E>
    ├── Caller handles all cases → use match
    ├── Propagate upward → use ?
    └── Provide default → use .unwrap_or() / .unwrap_or_else()
```

### Async Checklist

- Use `#[tokio::main]` or `#[async_std::main]` as your runtime
- Mark functions with `async fn` if they contain `.await`
- Use `tokio::join!` or `futures::join!` for concurrent tasks
- Use `tokio::spawn` + `move` closure for background tasks
- Return `Pin<Box<dyn Future>>` for trait objects

---

## Resources

- [The Rust Programming Language (The Book)](https://doc.rust-lang.org/book/)
- [Rust by Example](https://doc.rust-lang.org/rust-by-example/)
- [Rustonomicon (Advanced/Unsafe)](https://doc.rust-lang.org/nomicon/)
- [Async Book](https://rust-lang.github.io/async-book/)
- [Standard Library Docs](https://doc.rust-lang.org/std/)
- [Rust Reference](https://doc.rust-lang.org/reference/)
- [Crates.io](https://crates.io/)

---

*Generated with ❤️ for Rustaceans at every level.*
