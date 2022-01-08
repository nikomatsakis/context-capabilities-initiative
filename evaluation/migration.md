# Migrating existing APIs

If a library with an existing API could benefit from using context objects in
its public API, is there a soft migration path that doesn't require both the
library and its users to change at the same time?

## Injectable arguments

An earlier proposal for [`#[inject]`able functions][inject] hints at an answer:
make contexts regular function arguments, and allow callers to leave off
arguments that are already `given`.

```rust
fn do_stuff(
    num: u32,
    #[inject(given log::logger)]
    logger: Logger,
) {
    info!(logger, "do_stuff({})", num);
}
```

This is similar to how implicit arguments work in Scala. There are some challenges, however.

* What happens if some of your `#[inject]` arguments are provided and some aren't?
* Should the function signature effectively be rewritten to remove the
  provided arguments in a given scope? Spooky!
* It's tempting to only allow `#[inject]` arguments at the end to reduce
  confusion. However, it would severely limit its usefulness for migrating
  existing APIs.

[inject]: https://internals.rust-lang.org/t/can-we-make-a-rusty-effect-system/11697/14
