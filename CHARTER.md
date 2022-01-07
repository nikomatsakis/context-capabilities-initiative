# 📜 {{INITIATIVE_NAME}} Charter
<!--
 Provide an introduction summarising the goals and motivation behind your
 initiative.
-->

Context objects in Rust must be passed manually today. This means that they
are difficult or impossible to use with code you don't control. It also pushes
programmers toward making context objects more coarse-grained than they have to
be and using globals where they aren't desirable.

The goal of this initiative is to create a first-class way of working with
contexts in Rust that solves these problems:

* It should be possible to thread contexts through code you don't control,
  without the code having to make any accommodations.
* Contexts are allowed to exist on the stack.
* Contexts can be generic / type erased.
* Contexts can be "decomposed" into smaller pieces or "bundled" into larger
  ones.
* Contexts can be used to implement compile-time dependency injection.
* Contexts work well with `dyn`, higher order functions, and the like.

Eventually this mechanism can be used to solve a number of other challenges
in the language and standard library:

* Making "global" objects like allocators and async executors defineable and
  overridable by the user.
* Describing one's runtime environment in a more detailed way than
  `core`/`alloc`/`std`, which is not always appropriate for runtimes like wasm.
* Making the Rust ecosystem more composable and interoperable.

## Proposal

<!--
Copy and paste the proposal into here.

Feel free to move some elements, like design questions that came up,
into the approriate section.
-->

TODO

## Membership

| Role | Github |
| ---  | --- |
| [Owner] | [ghost](https://github.com/ghost) |
| [Liaison] | [ghost](https://github.com/ghost) |

[Owner]: https://lang-team.rust-lang.org/initiatives/process/roles/owner.html
[Liaison]: https://lang-team.rust-lang.org/initiatives/process/roles/liaison.html
