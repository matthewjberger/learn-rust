# Option and Enums

In this section, we are going to make the dog
we created in the last section have some gourmet options for toys.

## Enumerating Bones

Let's add an enumeration to our program
that represents various bone flavors.

```rust
// ...

struct Bone {
    kind: BoneKind,
}

impl Bone {
    pub fn new(kind: BoneKind) -> Self {
        Self { kind }
    }
}

enum BoneKind {
    BaconFlavored,
    TurkeyAndStuffing,
    PeanutButter,
}

fn main() {
    // ...
}
```

## Optional Fields

Now we need a way to represent the dog having
a bone or not having a bone. 

We can represent this using the [`Option`](https://doc.rust-lang.org/std/option/) type.

`Option` is a generic enumeration that looks like this.

```rust
pub enum Option<T> {
    None,
    Some(T),
}
```

With this information, let's add an optional `bone` field to our `Dog`.

```rust
struct Dog {
    age: u8,
    pub bone: Option<Bone>,
}

impl Dog {
    pub fn new(age: u8) -> Self {
        Self { age, bone: None }
    }

    // ...
}

fn main() {
    // ...
}
```

Now our dog can hold onto a `Bone`!

However, things get more complicated when we want
to start giving and taking bones.

What if the dog already has a bone?
What if the dog doesn't like the flavor?

In the next section, we'll cover how to handle 
_fallibility_ in our program.

## Full Program

The full program now looks like this.

```rust
struct Dog {
    age: u8,
    pub bone: Option<Bone>,
}

impl Dog {
    pub fn new(age: u8) -> Self {
        Self { age, bone: None }
    }

    pub fn celebrate_birthday(&mut self) {
        self.age = self.age + 1;
        println!("Wiggly butt is {} wags old!", self.age);
    }
}

struct Bone {
    kind: BoneKind,
}

impl Bone {
    pub fn new(kind: BoneKind) -> Self {
        Self { kind }
    }
}

enum BoneKind {
    BaconFlavored,
    TurkeyAndStuffing,
    PeanutButter,
}

fn main() {
    let mut dog = Dog::new(8);
    dog.celebrate_birthday();
}
```
