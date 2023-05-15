---
title: "Programming Language Classification"
teaching: 20
exercises: 0
---

::: questions

- How does Julia roughly compare to other programming languages?

:::

::: objectives

- State important global properties of Julia.

:::

In this episode we will place Julia within the world of programming languages.

## High-Level

Julia is a high-level language, that is, the language abstracts away the details
of the hardware that it runs on. Its abstaction level is comparable to languages
like Java and Python.

## Dynamic

Julia is dynamically typed by default. This puts it in the company of Python and
distinguishes it from Java and C++.

Even though it is dynamically typed by default, it does allow programmers to
specify the type of a value, for example for method parameters or method return
values. This is mainly necessary to make use of method dispatch based on argument
types, a topic we will get to in a later episode. It also helps to ensure that
functions only work for intended parameter types. In some cases, it can even
[improve performance](https://docs.julialang.org/en/v1/manual/types/#Type-Declarations).

We will give it a try and define two methods, one with and one without
specifying the parameter type:

```julia
> identity(x) = x
identity (generic function with 1 method)

> identity(2.0)
2.0

> identity(2)
2

> float_identity(x::Float64) = x
float_identity (generic function with 1 method)

> float_identity(2.0)
2.0

> float_identity(2)
ERROR: MethodError: no method matching float_identity(::Int64)
Closest candidates are:
  float_identity(::Float64) at REPL[4]:1
Stacktrace:
 [1] top-level scope
   @ REPL[6]:1
```

## Garbage Collected

Julia uses garbage collection for memory management.

## Compiled

Julia is a compiled programming language. However, functions are compiled
just-in-time for any given list argument types. For example, when we define a
method, it does not get compiled.

```julia
> foo(x) = x
foo (generic function with 1 method)
```

Only when we call it for the first time, this happens.

```julia
> foo(3)
3
```

And it only compiles the function for arguments of type `Int64`. We can look at
the compilation results for different argument types using the function
`code_native`:

```julia
julia> code_native(foo, (Int64,))
        .text
; ┌ @ REPL[1]:1 within `foo`
        endbr64
        movq    %rdi, %rax
        retq
        nopl    (%rax,%rax)
; └

julia> code_native(foo, (Float64,))
        .text
; ┌ @ REPL[2]:1 within `foo`
        endbr64
        movabsq $.rodata.cst8, %rax
        vmovsd  (%rax), %xmm0                   # xmm0 = mem[0],zero
        retq
        nopw    %cs:(%rax,%rax)
; └
```

Since Julia always complies and never evaluates, the compiler is sometimes
called a just-ahead-of-time compiler.

## High-performance

The [intention behind developing
Julia](https://julialang.org/blog/2012/02/why-we-created-julia/) was to create a
fast dynamic language. And so it comes as no surprise that high-performance
software can be written in Julia. The language is, by design, particularly well
suited for dealing with (large) arrays of numbers. This makes it a good language
for writing code for numerical analysis, optimization, and many forms of machine
learning.

[Benchmarks on the language’s website](https://julialang.org/benchmarks/) place
its performance close to that of languages like C or Rust.

With [CUDA.jl](https://juliagpu.org/cuda/) there is an officially supported
library that allows to write code for Nvidia GPUs directly in Julia.
