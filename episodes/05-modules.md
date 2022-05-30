---
title: "Modules"
teaching: 0
exercises: 0
---

Julia organizes code into modules and packages. A package is the distribution
unit. Libraries and applications are usually distributed as packages. Modules
are for organizing code within a package.

In this episode we will learn about modules. Modules are used for namespace
management.

A module starts with the keyword `module`, a name end ends with `end`. Let us
open a file called `main.jl` in an empty directory and add the following code:

```julia
module OurVectors

end
```

Unlike in Python, a file can contain multiple modules, so module name and file
name do not have to fit in any way to one another.

But, for examples sake, we will put the actual contents of our module in another
file. We create an empty file `vectors.jl` in the same folder and include it
using the `include` function in our module, so that `main.jl` will look as
follows:

```julia
module OurVectors

include("vectors.jl")

end
```

When `main.jl` is loaded in any way all the code in `vectors.jl` will be
evaluated in the scope of the `OurVectors` module. On could potentially split
the code of a module into several files this way.

Also note, that it is idiomatic to not indent the code in a module relative to
the `module` statement. Module names are usually set in upper camel-case.

We will now add a function to `vectors.jl`:

```julia
length(v) = sqrt(sum(v))
```

Next, we try to use it in `main.jl` by appending

```julia
println(length([3]))
```

and running the file with

```bash
$ julia main.jl
1
```

We expected `3` but got `1`. Julia calls the standard library function `length`
instead of our module’s function. To use our function outside the module, we
need to first export it from the module and then use it outside the module:

```julia
module OurVectors

export length

include("vectors.jl")

end

using .OurVectors

println(length([3]))
```

We need to add the `.` in front of the module name, because otherwise Julia
would look for a package named `OurVectors` which it would not find. The `.`
tells it to look for a module relative to the current one.

We put the `export` keyword at the top of the module, so that is the first
information an user of the module sees.

Running the example, we get

```bash
$ julia main.jl
WARNING: both OurVectors and Base export "length"; uses of it in module Main must be qualified
ERROR: LoadError: UndefVarError: length not defined
Stacktrace:
 [1] top-level scope
   @ ~/temp/main.jl:11
in expression starting at /home/user/project/main.jl:11
```

`Base` is a module provided by Julia and `Base.length` is the function that
returns the element count of various container or iterator types. This conflicts
with us using the exported `length` of our module. Instead we have to write

```julia
println(OurVectors.length([3]))
```

which gets us

```julia
$ julia main.jl
3
```

Alternatively, we can import the function into the current module’s namespace:

```julia
…

import .OurVectors: length

println(length([3]))
```

This, of course, is very unsafe, since it overrides a standard library function.
Instead we could give it a new name in the current module:

```julia
…

import .OurVectors: length as vlength

println(vlength([3]))
```

All this shows that each module has its own namespace, but we can import names
directly or with a new name from module one into another.

We can force users to always use our `length` prefixed by the module name
`OurVectors` by not exporting it. It is still accessible, but it can no longer
be imported by name or brought into another namespace via `using`.

The difference between `using` and `import` is that, by default, `using` makes
the module itself and all exported names available in the using module
namespace, wheres `import` only makes the module available. Then `using` can be
modified to only import specific names (including the module itself) and
`import` can be modified to import additional names. In both cases the imported
entities can be renamed using the keyword `as`.

## Bare modules and standard modules

Whenever we create a module, the modules `Core` and `Base` are automatically
contained as if imported with `using`. If a module is declared with the
`baremodule` keyword instead, they are not contained.

In addition to `Core` and `Base` a Julia distribution comes with further
standard modules that do not have to be installed explicitly; among them `Test`.
In addition, `Base` has several submodules, e.g. `Base.Threads`.

`Main` is the top level module of the REPL.
