---
title: "Introduction"
teaching: 0
exercises: 0
---

::: questions

- What kind of programming language is Julia?

:::

::: objectives

- State the main properties of the Julia programming language.

:::

# Preface

This lesson will teach about the idiosyncrasies of the Julia programming
language. We will teach and learn through participatory live coding, that means
I, the instructor, will write code in a Julia REPL so that you can see it. I
will write slowly and, in addition, will explain everything that I have typed,
so that you can code along.

Language syntax, semantics, and constructs will be explained along the way,
though we assume a familiarity with common programming language terminology and
its meaning. The syntax is similar to Python’s syntax.

# Language Properties

We will have a look at Julia’s properties as a programming language.

## High-Level

Julia is a high-level language abstracting most hardware features away.

## Compiled

Julia is compiled language. Code get’s compiled just-in-time, but always.
Because of this, its compiler is sometimes called a just-ahead-of-time compiler.

## High-performance

High-performance software can be written in Julia. The language is, by design,
particularly well suited for dealing with (large) arrays of numbers. This makes
it well suited for writing code for numerical analysis, optimization, and thus
many forms of machine learning.

[Benchmarks on the language’s website](https://julialang.org/benchmarks/) place
its performance close to that of languages like C or Rust.

With [CUDA.jl](https://juliagpu.org/cuda/) there is an officially supported
library that allows to write code for Nvidia GPUs directly in Julia.

## Dynamic

Julia is dynamically typed by default, but allows programmers to specify the
type of values. When provided, this information is used by the compiler to
perform type checks and to generate more efficient code.

We will give it a try and define two methods, one with and one without
specifying the parameter type:

```julia
> a1(x, y) = x + y
a1 (generic function with 1 method)
> a1(2, 3)
5
> a1(2.0, 3.0)
5.0
> a2(x::Int, y::Int) = x + y
a2 (generic function with 1 method)
> a2(2, 3)
5
> a2(2.0, 3.0)
ERROR: MethodError: no method matching a2(::Float64, ::Float64)
Stacktrace:
 [1] top-level scope
   @ REPL[6]:1
```

## Garbage Collected

Julia uses garbage collection for memory management.

# Idiosyncrasies

## Types

Even though Julia is by default dynamic, types play an important role when
developing high-performance code. Specifying the type of a value helps the
compiler to produce efficient code.

We can the determine the type of a value using the function `typeof`. It will
always return the type used to represent the value.

```julia
> typeof(1)
Int64

> tyepof(Int32(1))
Int32

> typeof(2.0)
Float64

> typeof(1:2)
UnitRange{Int64}

> typeof(typeof)
typeof(typeof) (singleton type of function typeof, subtype of Function)
```

As we can see, functions are first-order objects. This allows for functional
style programming, which is part of idiomatic Julia programming.

Types for storing vectors and matrices are and important part of the language.
Such a types element type is part of its type and can be determined
programmatically as well:

```julia
> typeof(1:2)
UnitRange{Int64}

> eltype(1:2)
Int64
```

All types in Julia form a hierarchy with `Any` at the top. We can explore that
hierarchy using the function `supertype`.

```julia
> typeof(1:2)
UnitRange{Int64}

> supertype(ans)
AbstractUnitRange{Int64}

> supertype(ans)
OrdinalRange{Int64, Int64}

> supertype(ans)
AbstractRange{Int64}

> supertype(ans)
AbstractVector{Int64} (alias for AbstractArray{Int64, 1})

> supertype(ans)
Any

> supertypes(typeof(1:2))
(UnitRange{Int64}, AbstractUnitRange{Int64}, OrdinalRange{Int64, Int64},
AbstractRange{Int64}, AbstractVector{Int64}, Any)
```

## Data Types

We will now quickly go through the available data types.

### Number Types

Julia provides various common number types. We begin with floats:

```julia
> supertypes(Float16)
(Float16, AbstractFloat, Real, Number, Any)

> supertypes(Float32)
(Float32, AbstractFloat, Real, Number, Any)

> supertypes(Float64)
(Float64, AbstractFloat, Real, Number, Any)

> supertypes(BigFloat)
(BigFloat, AbstractFloat, Real, Number, Any)
```

Bool is a Number in Julia:

```julia
> supertypes(Bool)
(Bool, Integer, Real, Number, Any)

> false == 0
true

> 0 + true
1
```

For integer types there are 8, 16, 32, 64, and 128 variants of signed and
unsigned integers, for example:

```julia
> supertypes(Int128)
(Int128, Signed, Integer, Real, Number, Any)

> supertypes(UInt32)
(UInt32, Signed, Integer, Real, Number, Any)
```

In addition, there are arbitrary size integers:

```julia
> supertypes(BigInt)
(BigInt, Signed, Integer, Real, Number, Any)
```

Then there are types for complex and rational numbers that are parameterized on
subtypes of `Real` and `Integer` respectively:

```julia
> supertypes(Rational{Int8})
(Rational{Int8}, Real, Number, Any)

> supertypes(Complex{Bool})
(Complex{Bool}, Number, Any)

> supertypes(Complex{Rational{Int8}})
(Complex{Rational{Int8}}, Number, Any)
```

Finally, Julia has a type for represent the exact values of irrational
constants:

```julia
> typeof(π)
Irrational{:π}

> typeof(pi)
Irrational{:π}
```

### Strings

There are types for strings and characters:

```julia
> typeof("Hello")
String

> typeof("Hello"[1])
Char
```

::: callout

#### Indices

In Julia indexing starts with 1 instead of the more common 0. So, for example,
to get the second element of an array, one would use the index `2`:

```julia
> [2, 3, 5][2]
3
```

:::

### (Named) Tuples

As a simple fixed length ordered container that allows to mix types of elements
arbitrarily, Julia features tuples. Their behavior is similar to that of tuples
in C++ and Python or lists in many Lisp dialects.

```julia
> (1, "a")
(1, "a")

> typeof((1, "a"))
Tuple{Int64, String}

> supertypes(typeof((1, "a")))
(Tuple{Int64, String}, Any)
```

We can use tuples to return multiple values from a function:

```julia
> t1() = (1, 2)
t1 (generic function with 1 method)

> t1()
(1, 2)
```

The elements of tuples can be accessed through decomposition and indexing:

```julia
> a, b = t1()
(1, 2)

> a
1

> t1()[1]
1
```

In addition to plain tuples, there are named tuples, where every element has its
own name:

```julia
> v = (x = 1, y = -1)
(x = 1, y = -1)

> v[2]
-1

> v.y
-1
```

### Arrays

Next, we will have a look at array types, which are arguably one of the
distinguishing features of the Julia programming language.

One kind of one-dimensional arrays is called vectors:

```julia
> [1, 2]
2-element Vector{Int64}:
 1
 2
```

There is another kind of one-dimensional array:

```julia
> [1 2]
1×2 Matrix{Int64}:
 1  2
```

The former represent what is sometimes called column vectors, while the latter
represent row vectors. Julia allows us to do matrix arithmetic on them:

```julia
> [1, 2] * [1 2]
2×2 Matrix{Int64}:
 1  2
 2  4

> [1 2] * [1, 2]
1-element Vector{Int64}:
 5
```



### Structs

## Generic Functions/Multiple Dispatch

Another important type that deserves its own section are generic functions.

