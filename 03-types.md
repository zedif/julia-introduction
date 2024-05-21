---
title: "Types"
teaching: 30
exercises: 0
---

::: questions

- What data types does Julia offer out of the box?
- How can we define our own types?

:::

::: objectives

- SOME OBJECTIVE

:::

Even though Julia is by default dynamic, types play an important role. We need
to specify them to make use of the method dispatch feature, where one of Julia’s
distinguishing features is that it offers multiple dispatch. We also need it to
write high performance code.

::: callout

### Method Dispatch Primer

Method dispatch is the process of choosing which method of a function (more
on that in a later episode) to execute based on the argument count and argument
types.

For example, adding two integers requires different code than adding to floating
point numbers, and again different code than adding an integer to a floating
point number.

:::

## Types and Type Hierarchy

Every value in Julia has a type. We can determine the type of a value using the
function `typeof`.

```julia
> typeof(1)
Int64

> typeof(2.0)
Float64

> typeof(typeof)
typeof(typeof) (singleton type of function typeof, subtype of Function)
```

We can see that the default type for integers as well as floats is the
respective 64-bit version. We can also see that functions are types just like
any other. This allows to write functional code, which is part of idiomatic
Julia.

All types in Julia form a hierarchy with `Any` at the top. We can explore that
hierarchy using the functions `supertype` and `supertypes`.

We determine the type of the value represented by the number literal `1`.

```julia
> typeof(1)
Int64
```

The Julia REPL offers a special variable, `ans`, that always holds the result of
the previous evaluation. We use it with `supertype` to climb up the type
hierarchy:

```
> supertype(ans)
Signed

> supertype(ans)
Integer

> supertype(ans)
Real

> supertype(ans)
Number

> supertype(ans)
Any

> supertype(ans)
Any
```

`supertype` returns `Any` for input `Any` which happens to be at the top of the
type hierarchy. It is an ancestor of every data type and so every value is of
type `Any`.

We can use `supertypes` to get the full inheritance chain of a type as a tuple
of types.

```
> supertypes(Int64)
(Int64, Signed, Integer, Real, Number, Any)
```

## Number Types

Julia provides various common number types.

There are four floating point number types:

```julia
> Float16(1.0) # This might be broken
Float16(0.0)

> Float32(1.0)
1.0f0

> Float64(1.0)
1.0

> supertypes(BigFloat)
(BigFloat, AbstractFloat, Real, Number, Any)
```

`BigFloat` is an arbitrary precision floating point number type.

And signed and unsigned integer types for 8, 16, 32, 64, and 128 bits, for
example:

```julia
> Int8(32)
32

> UInt32(42)
0x0000002a
```

As for floating point numbers, there is an arbitrary precision integer type:

```julia
> supertypes(BigInt)
(BigInt, Signed, Integer, Real, Number, Any)
```

Bool is a number type in Julia:

```julia
> supertypes(Bool)
(Bool, Integer, Real, Number, Any)

> false == 0
true

> 0 + true
1
```

There are types for complex and rational numbers that are parameterised on
subtypes of `Real` and `Integer` respectively:

```julia
> typeof(3//7)
Rational{Int64}

> Complex{Bool}(1, 0)
Complex(true, false)

> Complex(3//7, -1//5)
3//7 - 1//5*im

> im*im
-1 * 0im
```

Finally, Julia has a type to represent the exact values of irrational
constants:

```julia
> typeof(π)
Irrational{:π}

> typeof(pi)
Irrational{:π}
```

## Strings

There are types for strings and characters:

```julia
> typeof("Hello")
String
```

```julia
> typeof("Hello"[3])
Char
```

::: challenge

### Indexing in Julia

What does the following expression evaluate to:

```
[2, 3, 5][2]
```

:::: solution

In Julia indexing starts with 1 instead of the more common 0. So, for example,
to get the second element of an array, one would use the index `2`:

```julia
> [2, 3, 5][2]
3
```

::::

:::

## (Named) Tuples

Julia features a type for tuples, similar to tuples in C++ and Python or lists
in many Lisp dialects. Tuples is a fixed length ordered container whose elements
may have any type. The syntax is the same as in Python:

```julia
> typeof((1, "a"))
Tuple{Int64, String}

> typeof((1))
Int64

> typeof((1,))
Tuple{Int64}
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

## Arrays

Arrays and associated features are arguably one of the most important defining
traits of the Julia programming language.

Julia supports arrays of arbitrary dimensions.

We have 0-dimensional arrays (scalars):

```julia
> zeros(Int8, ()))
0-dimensional Array{Int8, 0}:
 0
```

1-dimensional arrays are called vectors:

```julia
> ones(Int8, (2))
2-element Vector{Int8}:
 1
 1
```

2-dimensional arrays are called matrices:

```julia
> zeros(Int8, (2, 2))
2×2 Matrix{Int8}:
 0  0
 0  0
```

And any higher dimensional arrays are just called arrays:

```
> zeros(Int8, (2, 2, 2))
2×2×3 Array{Int8, 3}:
[:, :, 1] =
 0  0
 0  0

[:, :, 2] =
 0  0
 0  0
```

### Working with arrays

Julia supports matrix operations with the usual operators:

```julia
> A = [[0, 1] [1, 0]]
2×2 Matrix{Int64}:
 0  1
 1  0

> b = [1, 2]
2-element Vector{Int64}:
 1
 2

> b+b
2-element Vector{Int64}:
 2
 4

> A-A
2×2 Matrix{Int64}:
 0  0
 0  0

> A * b
2-element Vector{Int64}:
 2
 1

> A*A
2×2 Matrix{Int64}:
 1  0
 0  1
```

Julia also supports a special syntax, the dot-operator, to element-wise apply
functions to arrays:

```julia
> [1, 4, 9, 16, 25] .> 7
5-element BitVector:
 0
 0
 1
 1
 1

> sqrt.([1, 4, 9, 16, 25])
5-element Vector{Float64}:
 1.0
 2.0
 3.0
 4.0
 5.0
```

With the `@.` we can make every function call in an expression be applied
element-wise:

```julia
> @. [1 1] * [1 1] + [1 1]
1×2 Matrix{Int64}:
 2  2

> [1 1] * [1 1] + [1 1]
ERROR: DimensionMismatch("matrix A has dimensions (1,2), matrix B has dimensions (1,2)")
Stacktrace:
 [1] _generic_matmatmul!(C::Matrix{Int64}, tA::Char, tB::Char, A::Matrix{Int64}, B::Matrix{Int64}, _add::LinearAlgebra.MulAddMul{true, true, Bool, Bool})
   @ LinearAlgebra /usr/share/julia/stdlib/v1.7/LinearAlgebra/src/matmul.jl:810
 [2] generic_matmatmul!(C::Matrix{Int64}, tA::Char, tB::Char, A::Matrix{Int64}, B::Matrix{Int64}, _add::LinearAlgebra.MulAddMul{true, true, Bool, Bool})
   @ LinearAlgebra /usr/share/julia/stdlib/v1.7/LinearAlgebra/src/matmul.jl:798
 [3] mul!
   @ /usr/share/julia/stdlib/v1.7/LinearAlgebra/src/matmul.jl:302 [inlined]
 [4] mul!
   @ /usr/share/julia/stdlib/v1.7/LinearAlgebra/src/matmul.jl:275 [inlined]
 [5] *(A::Matrix{Int64}, B::Matrix{Int64})
   @ LinearAlgebra /usr/share/julia/stdlib/v1.7/LinearAlgebra/src/matmul.jl:153
 [6] top-level scope
   @ REPL[2]:1
```

### Element Types

We can determine the element types of an array type (and some other container
types) using the function `eltype`:

```julia
> eltype([1, 2])
Int64
```

### Generic Array types

There are more types that behave similar to arrays. With strings we already saw
one of them. Another are ranges:

```julia
> 1:10
1:10

> typeof(ans)
UnitRange{Int64}

> eltype(ans)
Int64

> (1:10)[9]
9

> collect(1:10)
10-element Vector{Int64}:
  1
  2
  3
  4
  5
  6
  7
  8
  9
 10

> collect(1:3:10)
4-element Vector{Int64}:
  1
  4
  7
 10

> typeof(1:3:10)
StepRange{Int64}

> range(-0.1, 0.1, length=3)
-0.1:0.1:0.1

> typeof(range(-0.1, 0.1, length=3))
StepRangeLen{Float64, Base.TwicePrecision{Float64},
Base.TwicePrecision{Float64}, Int64}

> collect(range(-0.1, 0.1, length=3))
3-element Vector{Float64}:
 -0.1
  0.0
  0.1
```

## Structs

Structs are what allows us to define our own types in Julia. In our first
example we will created a parameterized type for 2-dimensional points:

```julia
> struct Point{X}
         x::X
         y::X
  end
```

### Mutability

Instances of structs are by default immutable.

```julia
> p = Point(1, 2)
Point{Int64}(1, 2)

> p.x = 3
ERROR: setfield!: immutable struct of type Point cannot be changed
Stacktrace:
 [1] setproperty!(x::Point{Int64}, f::Symbol, v::Int64)
   @ Base ./Base.jl:43
 [2] top-level scope
   @ REPL[3]:1
```

Putting the keyword `mutable` in front of the definition changes that:

```
> mutable struct MPoint{X}
          x::X
          y::Y
  end

> mp = MPoint(1, 2)
MPoint{Int64}(1, 2)

> mp.x = 3
3
```

### Constructors

We already used the default constructor of a struct. It takes as many arguments
as the struct has fields and assigns them to the fields in the order they are
specified.

We can also define further or alternate constructors; either as part of the
struct definition, inner constructor, or separately, outer constructor.

We will start with a separate, parameterless constructor for our `Point` struct:

```julia
> Point() = Point(0, 0)
Point

> Point()
Point{Int64}(0, 0)
```

In this case we get an additional constructor.

When we define constructors as part of the struct definition, the default
constructor does not exist.

```julia
> struct Foo
      x
      Foo() = new(0)
  end

> Foo()
Foo(0)

> Foo(1)
ERROR: MethodError: no method matching Foo(::Int64)
Closest candidates are:
  Foo() at REPL[11]:3
Stacktrace:
 [1] top-level scope
   @ REPL[3]:1
```

Note, that we need to use a preexisting constructor to define an outer
constructor, while we use the keyword `new` as if we were calling the default
constructor when defining inner constructors.

## More Types

The Julia language provides a lot more types; for example `CartesianIndex`,
`IOStream`, or `Atomic{T}`. We again refer to the documentation.
