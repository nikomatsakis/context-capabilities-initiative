# Moves and mutation

Should we allow passing contexts (including non-`Copy` contexts) by value, or
require them to be passed by reference?

## Option 1: Shared references only

This is the simplest option; shared references are always `Copy`. We can save
users the trouble of writing `&` everywhere by implicitly adding it for them.
Contexts that need mutation can trivially add it with `RefCell` or `Mutex`.

## Option 2: Shared and mutable references only

Here we would always know that a declared context object remains available, but
not whether we could hold a valid reference to it. I'm not sure why it would be
any easier to implement than option 3.

## Option 3: Allow passing by value or by reference

This option is attractive because it means contexts are not special; they behave
just like other arguments. However, it might be significantly less ergonomic. It
could also have unexpected consequences we haven't thought of.

[This blog post](https://jam1.re/blog/thoughts-on-contexts-and-capabilities-in-rust)
has thoughts on how this could work. It raises the idea of treating contexts
like closure captures in that they are (mutability-inferred) references by
default, but can be explicitly declared `move`. Thinking of contexts as captures
rather than regular arguments in this sense could be justification for such an
idea.
