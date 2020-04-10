# macroback-mre

This shows that the "help" messages by cargo with regard to macro backtraces are confusing and wrong.

Specifically, the code in question is:

```
fn main() {
    unreachable!();
    println!("Hello, world!");
}
```

And `cargo check` complains that:

```
warning: unreachable statement
 --> src/main.rs:3:5
  |
2 |     unreachable!();
  |     --------------- any code following this expression is unreachable
3 |     println!("Hello, world!");
  |     ^^^^^^^^^^^^^^^^^^^^^^^^^^ unreachable statement
  |
  = note: `#[warn(unreachable_code)]` on by default
  = note: this warning originates in a macro (in Nightly builds, run with -Z macro-backtrace for more info)
```

Let's assume we actually want to look at the macro backtrace for some reason:

```
$ cargo check -Z macro-backtrace
error: unknown `-Z` flag specified: macro-backtrace
[$? = 101]
```

So cargo suggests to do something that doesn't work.

Instead, here's how it's supposed to work:

```
$ RUSTFLAGS="-Z macro-backtrace" cargo check
warning: unreachable statement
   | 
  ::: src/main.rs:3:5
   |
3  |        println!("Hello, world!");
   |        -------------------------- in this macro invocation
   |
   = note: `#[warn(unreachable_code)]` on by default
```

Note that this also disagrees with the documentation in [the cargo issue](https://github.com/rust-lang/cargo/issues/6049), where it is called "external-macro-backtrace" instead.

---

So, if you run into the same trouble as I did, now you know how to call it correctly:
`RUSTFLAGS="-Z macro-backtrace" cargo check`
