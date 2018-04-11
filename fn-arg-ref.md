# Background

It's well-known that Rust has outstanding support for pattern matching.  And pattern matching is
available almost everywhere!

`ref` keyword is one of the goodies you have in Rust. It lets you avoid `&` and `&mut` when it comes to pattern matching.

```rust
let x = 42;
let a1 = &x;
let ref a2 = x;

let mut y = 114514;
{ let b1 = &mut y; }
{ let ref mut b2 = y; }
```

Here, both of `a1` and `a2` are `&i32`, while both of `b1` and `b2` are `&mut i32`.


# Examples

Naively, you think they are synonyms, and perhaps you want to use `ref` in a function argument like
this,

```rust
// does it mean `fn say_hi(name: &String)`?
fn say_hi(ref name: String) {
    println!("Hi, {}!", *name)
}

let me = "Rust".to_string();
say_hi(me);
println!("Thanks {}!", me);   // Error: use of moved value `me`
```

Or increment a counter like this,

```rust
// does it mean `fn increment(v: &mut i32)`?
fn increment(ref mut v: i32) {
    *v += 1
}

let mut x = 0;
increment(x);
println!("{}", x);            // Prints 0... What?!
```

# Explanation
You might have already noticed we didn't pass `&name` or `&mut x`.

In fact, destructuring a function argument (pattern) is not a synonym to writing out variables and
their types explicitly.

The reason is obvious if we desugar them,

``` rust
// Unexpected move
fn say_hi(name: String) {
    let name = &name;         // ***
    println!("Hi, {}!", name)
}

// We modified a copy!
fn increment(v: i32) {
    let v = &mut v;           // ***
    *v += 1
}
```

