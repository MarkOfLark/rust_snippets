# Ownership
## Prevent use after free errors

* Ownership rules enforced by compiler
  1. TODO Each value in Rust has a variable that's called its owner.
  2. There can only be one owner at a time.
  3. When the owner goes out of scope, the value will be dropped.
* C++ has RAII and smart pointer
  * Not as strong as Rust because these are opt-in and still require the programmer to do the right thing.
  * Not enforced at compile time.


```rust
// This function takes ownership of greeting
fn greet_with(greeting: String)
{
    println!("{}",greeting);
}

fn main() {
   let greeting = String::from("Hello, world!");
   greet_with(greeting); // Greeting is moved right here

   println!("The greeting was: {}!", greeting);
}
```

There is a feature in mdBook that lets you hide code lines by prepending them
with a `#`.

```bash
# fn main() {
    let x = 5;
    let y = 6;

    println!("{}", x + y);
# }
```

Will render as

```rust
# fn main() {
    let x = 5;
    let y = 7;

    println!("{}", x + y);
# }
```

## Including files

With the following syntax, you can include files into your book:

```hbs
\{{#include file.rs}}
```

The path to the file has to be relative from the current source file.

Usually, this command is used for including code snippets and examples. In this
case, oftens one would include a specific part of the file e.g. which only
contains the relevant lines for the example. We support four different modes of
partial includes:

```hbs
\{{#include file.rs:2}}
\{{#include file.rs::10}}
\{{#include file.rs:2:}}
\{{#include file.rs:2:10}}
```

The first command only includes the second line from file `file.rs`. The second
command includes all lines up to line 10, i.e. the lines from 11 till the end of
the file are omitted. The third command includes all lines from line 2, i.e. the
first line is omitted. The last command includes the excerpt of `file.rs`
consisting of lines 2 to 10.

## Inserting runnable Rust files

With the following syntax, you can insert runnable Rust files into your book:

```hbs
\{{#playpen file.rs}}
```

The path to the Rust file has to be relative from the current source file.

When play is clicked, the code snippet will be sent to the [Rust Playpen] to be
compiled and run. The result is sent back and displayed directly underneath the
code.

Here is what a rendered code snippet looks like:

{{#playpen example.rs}}

[Rust Playpen]: https://play.rust-lang.org/
