# Captures and overrides

Consider:

```rust
impl Deserialize for Bar
given
    basic_arena: BasicArena,
{ ... }

fn deserialize_and_print<T>(input: impl Read) -> T
where
    T: Deserialize + Debug,
{ ... }

fn main() {
    given basic_arena = BasicArena::new() {
        // This works, even though `deserialize_and_print` knows nothing about
        // `basic_arena`!
        let _foo: &Foo = deserialize_and_print(std::io::stdin());
    }
}
```

The call to `deserialize_and_print` above knows that `basic_arena` has been
provided, and therefore that the `T: Deserialize` bound can be satisfied by the
impl.

Mechanically, how do we thread the arena through the impl? Is its *value*
captured at the time of using the impl (meaning in `main`, at the call to
`deserialize_and_print`) and "smuggled" through somehow? Or do we only capture
the *knowledge* that this context is available, and allow it to be overridden?

More specifically, what happens if we do this?

```rust
fn main() {
    given basic_arena = BasicArena::new() {
        let _foo: &Foo = deserialize_and_print_with_arena(std::io::stdin());
    }
}

fn deserialize_and_print_with_arena<T>(input: impl Read) -> T
where
    T: Deserialize + Debug,
{
    // Which arena will win, the caller's or ours?
    given basic_arena = BasicArena::new() {
        deserialize_and_print(input)
    }
}
```

Note that this is a pretty contrived example. The above function just creates an
arena *for no particular reason*. If we had written its signature just a little
differently:

```rust
fn deserialize_and_print_with_arena<T>(input: impl Read) -> T
where
    T: given(basic_arena) Deserialize + Debug,
```

Then it would be obvious what the answer should be: The function "knows" that
`T: Deserialize` requires `basic_arena`, so its definition should always win.
So this ambiguous situation may not happen that often in practice.

It still could, however, if we are dealing with one generic argument or trait
object that has already "captured" some context, and another that explicitly
requires that context. If the caller provides the context to the latter, should
it override the context used by the former?

Using different contexts for each object would surely create confusion for the
user. Would this come up often in practice? Is there a way to mitigate the
impact somehow: lints, debugging tools, ...?
