# Syntax and naming

This page describes various keywords and surface-level syntactical approaches
that can be used to implement the feature. More may be added as new ideas
arise.

## Defining context items

### Keyword

The original [blog post] about this feature called context items a `capability`:

```rust
capability basic_arena<'a> = &'a BasicArena;
```

Some people have pushed back against this. While these items are related to
capabilities, that isn't the full story. A more neutral name is `context`.

```rust
context basic_arena<'a> = &'a BasicArena;
```

### Types and bounds

Context objects are simply items that may have where clauses attached to them.
This means it is possible to define a "bare" context with no type:

```rust
context anything_goes;
```

Or a specific type, or a generic context with bounds:

```rust
context basic_arena = BasicArena;
context any_allocator: Allocator;
context debug_iter: Iterator where Self::Item: Sized;
```

It's unclear what the best way is to write these. People might be confused by
the use of `=` to name an exact type. One approach is to treat them just like
function arguments.

```rust
context basic_arena: BasicArena;
context any_allocator: impl Allocator;
```

But then we also want to be able to write `where` clauses on the type, which you
can do with argument-position impl Trait. How would we write `debug_iter`?

```rust
// This could work...
context debug_iter: impl Iterator where debug_iter::Item: Sized;

// ...or we could allow you to write this.
context debug_iter: impl Iterator where Self::Item: Sized;
```

The problem with using `Self` is that it looks more like could we would write in
a trait definition, where `:` is used to denote supertraits. It's not clear
whether this would be confusing or not, just something to consider.

Whatever we choose, we would probably want `given` clauses to act the same way.

Note that in where clauses we treat the name of the context as a
*type* (as in the first example above), while in expression position we treat it
as a *value*.

## Providing and demanding context

The original [blog post] about this feature introduced the `with` keyword for
both introducing contexts in an expression and requiring them as clauses.

```rust
fn deserialize<'a>(bytes: &[u8]) -> Result<&'a Foo, Error>
with
    arena::basic_arena,
{
    arena::basic_arena.alloc(Foo::from_bytes(bytes)?)
}

fn main() -> Result<(), Error> {
    with arena::basic_arena = &arena::BasicArena::new() {
        let foo: &Foo = deserialize(read_bytes()?)?;
        println!("foo: {:?}", foo);
    }
    Ok(())
}
```

However, it was pointed out that `with` can mean many things, and more specific
keywords could be used like `given`:

```rust
fn deserialize<'a>(bytes: &[u8]) -> Result<&'a Foo, Error>
given
    arena::basic_arena,
{
    arena::basic_arena.alloc(Foo::from_bytes(bytes)?)
}

fn main() -> Result<(), Error> {
    given arena::basic_arena = &arena::BasicArena::new() {
        let foo: &Foo = deserialize(read_bytes()?)?;
        println!("foo: {:?}", foo);
    }
    Ok(())
}
```

This can still be used in both places. It's arguably much more clear than `with`
clauses that a `given` clause requires context to be provided to it.

[blog post]: https://tmandry.gitlab.io/blog/posts/2021-12-21-context-capabilities/
