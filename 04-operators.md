---
title: "Operators"
teaching: 10
exercises: 0
---

::: questions

- What basic operators/operations does Julia define?

:::

::: objectives

- SOME OBJECTIVE

:::

Julia, like most other languages, defines a core set of basic arithmetic and bitwise operators for its primitive data types.
Most of them are similar to most other languages, but there are some operators that might differ, like the division operator, which which is also differently defined between other languages or even language versions (like in Python 2 vs. 3).

# Arithmetic Operators

The following table lists the basic arithmetic operators which are defined on all primitive numeric data types:

Julia respects the usual mathematical ordering of operators, e.g.:

```julia
> 2+3*4
14
```

and

```julia
> 3*2^2
12
```

# Boolean Operators

Boolean operators work on the ```Bool``` type:

Note that while these operations do not work on integers:

```julia
> 1 && 0
ERROR: TypeError: non-boolean (Int64) used in boolean context
Stacktrace:
 [1] top-level scope
   @ REPL[38]:1
```

`Bool` is an `Integer` type and thus, boolean values can be treated as integers (`false` as `0` and `true` as `1`) where necessary:

```julia
> 1 + true
2
```

# Bitwise Operators

Bitwise operators work on all primitive integer types:


# Assigning operators

binary arithmetic and bitwise operators can be appended by an equal sign to produce an operator that assigns the result of the operation to the left operand, e.g.:

```julia
> a = 1
1

> a += 1
2

> a
2
```

# Numeric Comparisons

Comparisons work like in most other programming languages, with the possible extension to allow also non-ASCII versions of some operators:
