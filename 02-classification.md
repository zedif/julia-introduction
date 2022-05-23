---
title: "Programming Language Classification"
teaching: 0
exercises: 0
---

::: questions

- How does Julia roughly compare to other programming languages?

:::

::: objectives

- State important global properties of Julia.

:::

REWRITE FROM HERE

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
