# Bundling and splitting

It might be nice to be able to declare a single context that includes many
"sub-contexts", then split it up into those parts as needed.

Examples:

* Full I/O capabilities of a "regular process"
    * Output only
    * Networking
    * Clocks
* Async executor
    * Task spawner
    * Timer management

## Syntax

If we use the more [argument-like syntax][types] to define context types, we
could reclaim `=` for this:

```rust
context foo;
context bar;
context baz;

context kaboodle = foo + bar + baz;
```

[types]: ./syntax.md#types-and-bounds
