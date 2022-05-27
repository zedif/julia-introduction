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

When `main.jl` is loaded in any way all the code in `vectors.jl` will be part of
the `OurVectors` module. On could potentially split the code of a module into
several files this way.

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
need to first export it from the module and then import it outside the module:

```julia
module OurVectors

export length

include("vectors.jl")

end

import .OurVectors

println(OurVectors.length([3]))
```

We need to add the `.` in front of the module name, because otherwise Julia
would look for a package named `OurVectors` which it would not find. The `.`
tells it to look for a module relative to the current one.

Running the example, we get

```bash
$ julia main.jl
3
```

We can also import functions into the current module’s namespace:

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
