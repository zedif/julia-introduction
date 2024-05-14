---
title: "Generic Functions"
teaching: 0
exercises: 0
---

::: questions

- How does Julia implement functions?

:::

::: objectives

- Julia uses generic functions and their methods to implement functions.
- Julia implements multiple dispatch.

:::

Julia’s functions deserve their own episodes. Julia implements generic
functions. A generic function is implemented by zero or more methods. When a
(generic) function is called, a method that best fits to the provided arguments
is selected and executed.

## Methods

In the case of Julia the method is chosen based on the number of arguments and
their types.

This is used to provide implementations for specific argument types without
having to litter the code with conditionals based on value types. An impressive
example for the number of methods is the generic function `+`:

```julia
> methods(+)
[1] +(x::T, y::T) where T<:Union{Int128, Int16, Int32, Int64, Int8, UInt128, UInt16, UInt32, UInt64, UInt8} in Base at int.jl:87
[2] +(c::Union{UInt16, UInt32, UInt64, UInt8}, x::BigInt) in Base.GMP at gmp.jl:529
[3] +(c::Union{Int16, Int32, Int64, Int8}, x::BigInt) in Base.GMP at gmp.jl:535
…
```

::: callout

### Generic Methods on Steroids

For an example of a very complex generic function system you can have look at
the [Common Lisp Object
System](https://en.wikipedia.org/wiki/Common_Lisp_Object_System) (CLOS). There
methods can call less specific methods, there are so called before, after, and
around methods, and all applicable methods can be combined in several ways (for
example, summing the result of all applicable methods).

:::

We can define a generic function without any methods:

```julia
> function dist end
dist (generic function with 0 methods)
```

This is not necessary. If we provide an argument list and implementation in such
a definition and the generic function did not exist before, it will exist after
including the first method that will have been defined.

### Multiple Dispatch

Julia uses all argument types—and not just the first—to determine, which method
is most applicable. This is called multiple dispatch; in contrast to single
dispatch, as Java and C++ implement it, where the called method is (during
runtime) solely determined by the type of the object who’s method is called.
The object is, in the form of `this`, effectively the first argument to the
method.

```julia
> function length(x, y)
      sqrt(x^2 + y^2)
  end
length (generic function with 1 method)

> function length(x, y::Int64)
      println("Not implemented yet")
  end
length (generic function with 2 methods)

> length(1.0, 2.0)
2.23606797749979

> length(1, 2)
Not implemented yet
```

More important is the number of arguments:

```julia
> length(x) = x
length (generic function with 3 methods)

> length(1)
1
```

Multiple dispatch is similar to function overloading in languages like Java and
C++. There the appropriate method (except for dispatch on the first argument) is
determined at compile time based on the parameter types. In the case of Julia
this happens at runtime and thus allows to add further methods to a generic
functions in a running image of a Julia process.

## Arguments

In Julia arguments are pass-by-sharing, also known as -by-pointer or
-by-reference. For values of immutable types this may be optimized to
pass-by-value if the values fit into a single register. But because of the
immutability this is indistinguishable.

Julia also supports optional arguments:

```julia
> with_optional(x = 1) = 1
with_optional (generic function with 2 methods)

> with_optional(3)
3

> with_optional()
1
```

Note that `with_optional` immediately has two methods:

```julia
> methods(with_optional)
# 2 methods for generic function "with_optional":
[1] with_optional() in Main at REPL[1]:1
[2] with_optional(x) in Main at REPL[1]:1
```

This shows that optional arguments are implemented through multiple methods of a
generic function with different parameter lists. In fact, `with_optional()` is
defined as `= with_optional(1)`. Thus, changing the definition of
`with_optional(x)` will change the behavior of `with_optional()`:

```julia
> with_optional(x) = x + x
with_optional (generic function with 2 methods)

> with_optional()
2
```

Julia also supports keyword arguments:

```julia
> with_keyword(;x) = x
with_keyword (generic function with 1 method)

> with_keyword(x = 1)
1
```

Keyword parameters are defined after the other parameters separated by `;`. They
can have default values as well. Keyword parameters are not involved in method
dispatch.

Another, less common feature, is destructuring for tuple parameters:

```julia
> destructured((a, b)) = a + b
destructured (generic function with 1 method)

> t = (1, 2)
(1, 2)

> destructured(t)
3
```

Note that any additional elements of a tuple with be silently ignored, but we
get an error, when the tuple is to short:

```julia
> destructured((1, 2, 3))
3

> destructured((1,))
ERROR: BoundsError: attempt to access Tuple{Int64} at index [2]
Stacktrace:
 [1] indexed_iterate
   @ ./tuple.jl:89 [inlined]
 [2] destructured(::Tuple{Int64})
   @ Main ./REPL[1]:1
 [3] top-level scope
   @ REPL[5]:1
```

## Anonymous Functions

For short, one-off functions Julia implements anonymous functions:

```julia
> map(x -> x + 1, [1, 2, ])
3-element Vector{Int64}:
 2
 3
 4
```

There is also a long form:

```julia
> (function(x)
       x
   end)(3)
3
```

For functions passed as a first argument to another function, there is special
syntax that makes providing multiple line functions look clean:

```julia
> map([1, 2, 3]) do x
      if x == 1
         println("one")
      else
         println("not one")
      end
  end
one
not one
not one
3-element Vector{Nothing}:
 nothing
 nothing
 nothing
```

## Function Composition

Julia has an operator, `∘`, for function composition. You can write it in the
Julia-REPL typing `\circ<tab>`. It is useful to create function arguments that
are just compositions of other functions:

```julia
> map(sqrt ∘ sum, [[1, 1], [2, 2]])
2-element Vector{Float64}:
 1.4142135623730951
 2.0
```

An alternate method to `f(g(x))` for composition with a concrete value is the
operator `|>`:

```julia
> [2, 2] |> sum |> sqrt
2.0
```
