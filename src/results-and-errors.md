# Results and Errors

Now we want to be able to give 
the dog a bone and take it away to throw for fetching.
These operations can fail for various reasons.

If we take a bone but the dog does not have one, 
we get nothing back. That can be represented by an `Option`.
You either get the bone or you do not.

However, if we try to give the dog a bone but it already has one
then we consider that an error state.

## The Result Type

We can represent this using the [`Result`](https://doc.rust-lang.org/std/result/) type.

`Result` is a generic enum that looks like this:

```rust
enum Result<T, E> {
   Ok(T),
   Err(E),
}
```

We can represent fallible operations by making them return
a `Result` type, and we will specify our own `Error` type.

## Custom Errors

To represent an error when interacting
with an animal, we can create a custom error type.

```Rust
struct AnimalError {
    details: String,
}

impl AnimalError {
    fn new(msg: &str) -> Self {
        Self {
            details: msg.to_string(),
        }
    }
}

impl std::error::Error for AnimalError {
    fn description(&self) -> &str {
        &self.details
    }
}

impl std::fmt::Display for AnimalError {
    fn fmt(&self, f: &mut std::fmt::Formatter) -> std::fmt::Result {
        write!(f, "{}", self.details)
    }
}

// ...

fn main() {
    // ...
}
```

### Traits

To understand what is happening here, we'll need to discuss the concept of [traits](https://doc.rust-lang.org/book/ch10-02-traits.html).
Traits are like interfaces in other languages, except in Rust, traits only specify methods and not fields.
There is a [std::error::Error](https://doc.rust-lang.org/std/error/trait.Error.html) trait that the `Result` type uses as a type-constraint on its generic `Error` parameter.

For us to create a custom error type we have to implement the `std::error::Error` trait on our `AnimalError` custom error type.

We can implement other similar traits as well, such as [Display](https://doc.rust-lang.org/std/fmt/trait.Display.html).
`Display` specifies how an object should present itself in a user-facing for text. The equivalent for debugging is called [Debug](https://doc.rust-lang.org/std/fmt/trait.Debug.html).

## Introducing a Type Alias

To prevent us from having to write the full signature for a `Result` that uses our
custom error type, let's create a type alias to shorten it.

```Rust
pub type Result<T, E = Box<dyn std::error::Error>> = std::result::Result<T, E>;

// ...

fn main() {
    // ...
}
```

With this type alias, we can now omit the error type when specifying a fallible method.

## Writing Fallible Methods

Now we can write out methods for giving and taking a bone from a dog.

```rust
// ...

struct Dog {
    // ...
}

impl Dog {
    // ...

    pub fn receive_bone(&mut self, bone: Bone) -> Result<()> {
        match self.bone.as_ref() {
            Some(bone) => {
                return Err(Box::new(AnimalError::new(&format!(
                    "Dog already has a bone! ({:?})",
                    bone
                ))))
            }
            None => {
                println!("Doggy grabbed the {:?} bone!", bone.kind);
                self.bone = Some(bone);
            }
        };
        Ok(())
    }
}

fn main() {
    // ...
}
```

Additionally, the dog won't be able to speak while holding the bone. Let's add that now.

```rust
struct Dog {
    // ...
}

impl Dog {
    // ...

    pub fn speak(&self) -> Result<()> {
            match self.bone.as_ref() {
                Some(bone) => Err(Box::new(AnimalError::new(&format!(
                    "Dog can't speak because of the {:?} bone!",
                    bone
                )))),
                None => Ok(println!("Woof!")),
            }
        }
    }
}

fn main() {
    // ...
}
```

## Give That Dog a Bone

Now we can invoke these methods in main with a slight change to the return type
of `main`.

```rust
// ...

fn main() -> Result<()> {
    let mut dog = Dog::new(8);
    dog.celebrate_birthday();
    dog.speak()?; // Now we can invoke dog.speak()

    let bone = Bone::new(BoneKind::BaconFlavored);
    dog.receive_bone(bone)?;

    Ok(())
}
```

Note that the `?` operator can invoke fallible methods
and forward their errors to the caller if they fail.
We can call `dog.speak()?` now because `main` has a return type
of `Result<()>`.

## Debug Output

Earlier we mentioned that we could get debug representations of types by implementing
the `Debug` trait.

Luckily, there is an easy way to automatically derive this trait for structs
made of primitives or other structs that implement `Debug`.

```rust
#[derive(Debug)] // <-- Add this to any struct or plain enum you want to print debug string output for
struct AnimalError {
    details: String,
}
```

Then, add this to the rest of our structs and plain enums.

## Full Program

The full program now looks like this.

```rust
pub type Result<T, E = Box<dyn std::error::Error>> = std::result::Result<T, E>;

#[derive(Debug)]
struct AnimalError {
    details: String,
}

impl AnimalError {
    fn new(msg: &str) -> Self {
        Self {
            details: msg.to_string(),
        }
    }
}

impl std::error::Error for AnimalError {
    fn description(&self) -> &str {
        &self.details
    }
}

impl std::fmt::Display for AnimalError {
    fn fmt(&self, f: &mut std::fmt::Formatter) -> std::fmt::Result {
        write!(f, "{}", self.details)
    }
}

#[derive(Debug)]
struct Bone {
    kind: BoneKind,
}

impl Bone {
    pub fn new(kind: BoneKind) -> Self {
        Self { kind }
    }
}

#[derive(Debug)]
enum BoneKind {
    BaconFlavored,
    TurkeyAndStuffing,
    PeanutButter,
}

#[derive(Debug)]
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

    pub fn receive_bone(&mut self, bone: Bone) -> Result<()> {
        match self.bone.as_ref() {
            Some(bone) => {
                return Err(Box::new(AnimalError::new(&format!(
                    "Dog already has a bone! ({:?})",
                    bone
                ))))
            }
            None => {
                println!("Doggy grabbed the {:?} bone!", bone.kind);
                self.bone = Some(bone);
            }
        };
        Ok(())
    }

    pub fn speak(&self) -> Result<()> {
        match self.bone.as_ref() {
            Some(bone) => Err(Box::new(AnimalError::new(&format!(
                "Dog can't speak because of the {:?} bone!",
                bone
            )))),
            None => Ok(println!("Woof!")),
        }
    }
}

fn main() -> Result<()> {
    let mut dog = Dog::new(8);
    dog.celebrate_birthday();
    dog.speak()?;

    let bone = Bone::new(BoneKind::BaconFlavored);
    dog.receive_bone(bone)?;

    // Uncomment this to give the dog a bone while he already has one!
    //
    // let bone = Bone::from(BoneKind::BaconFlavored);
    // dog.receive_bone(bone)?;

    // Uncomment this to have the dog try to speak while holding the bone
    //
    // dog.speak()?;

    Ok(())
}
```
