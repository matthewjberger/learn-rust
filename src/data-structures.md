# Data Structures

In this section, we'll create a cute `Dog`!

Everyone loves pets, so let's start adding a pet to our project.

```rust
// main.rs
struct Dog;

fn main() {
    let _dog = Dog {};

    println!("Hello, world!");
}
```

This doesn't do much though, so let's change that! 

## Mutability, Functions, and Birthdays

Let's give our dog an `age` and allow
them to celebrate their birthday.

```rust
struct Dog {
    age: u8,
}

impl Dog {
    pub fn celebrate_birthday(&mut self) {
        self.age = self.age + 1;
        println!("Wiggly butt is {} wags old!", self.age);
    }
}

fn main() {
    let mut dog = Dog { age: 8 };
    dog.celebrate_birthday();
}
```

Note that we have to add the `mut` keyword to the dog to be able
to mutate it. In this case, the mutation is incrementing its age
when celebrating its birthday.

In Rust, objects are immutable by default.

## Constructors

Constructors in Rust are just functions that return an instance of an object.
They are not treated specially by the language itself like they are in C++.

```rust
struct Dog {
    age: u8,
}

impl Dog {
    pub fn new(age: u8) -> Self {
        Self { age }
    }

    pub fn celebrate_birthday(&mut self) {
        self.age = self.age + 1;
        println!("Wiggly butt is {} wags old!", self.age);
    }
}

fn main() {
    let mut dog = Dog::new(8);
    dog.celebrate_birthday();
}
```

## Methods

Now we can make the dog speak by adding
a public member method to the `Dog`'s `impl` block.

```rust
struct Dog {
    age: u8,
}

impl Dog {
    pub fn new(age: u8) -> Self {
        Self { age }
    }

    pub fn celebrate_birthday(&mut self) {
        self.age = self.age + 1;
        println!("Wiggly butt is {} wags old!", self.age);
    }

    pub fn speak(&self) {
        println!("Woof!");
    }
}

fn main() {
    let mut dog = Dog::new(8);
    dog.celebrate_birthday();
    dog.speak();
}
```

```rust
struct Dog {
    age: u8,
}

impl Dog {
    pub fn new(age: u8) -> Self {
        Self { age }
    }

    pub fn celebrate_birthday(&mut self) {
        self.age = self.age + 1;
        println!("Wiggly butt is {} wags old!", self.age);
    }
}

fn main() {
    let mut dog = Dog::new(8);
    dog.celebrate_birthday();
}
```

## Extra Details

### Numeric Types

Note that `u8` represents an `8-bit, unsigned integer`. There are other types like this
to represent various numeric values in efficient ways. This is a big distinction
from languages like typescript, which only have a type `number`.

### Printing to the Console

To print to the console, the following code is used.

```rust
println!("My message is: {}", "Hello!");
```

> Note the `!` after the `println`. This indicates that it is a [macro](https://doc.rust-lang.org/book/ch19-06-macros.html), which is
  an advanced topic in Rust that is out of scope for this book.

### Strings in Rust

If you're wondering why we call `.to_string()` on string literals,
here is why!

So what is the difference between `&str` and `String`?

String literals are baked into the final executable
in a specific region of memory that is neither the stack
nor the heap. They exist for the lifetime of the program
and are immutable,

`str` is an immutable sequence of UTF-8 bytes of dynamic length.
String literals have the type `&str`, which is an `immutable reference` (`&`)
to a `str`.

A `String` is a data structure that has a pointer to
a sequence of bytes on the heap as well as a field indicating the total size
of those bytes. This can be `owned` by a particular data structure,
who will manage its lifetime. When the `String` is destroyed,
the bytes are deallocated from the heap.

To convert from a `&str` to a `String`, we call `.to_string()`.

Most of the time we will use `String` in data structures,
and `&str` whenever we need a read-only reference to either a `str` or `String`.

Rust is smart enough to handle either case using [deref coercion](https://doc.rust-lang.org/book/ch15-02-deref.html).