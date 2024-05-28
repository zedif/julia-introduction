---
title: "Loops and Conditionals"
teaching: 20
exercises: 0
---

::: questions

- What basic loop constructs does Julia offer?
- What is the syntax for conditionals in Julia?

:::

::: objectives

- SOME OBJECTIVE

:::

We only cover two basic loop types here: `while` and `for` loops, and both are similar to most other programming languages.

# while loops

`while` loops in Julia look like this:

```julia
while <condition>
    <do something>
end
```

For example, the following prints the first few squares:
```julia
> n = 0
> while n < 5
      println(n^2)
      n += 1
  end
```

# for loops

`for` loops in Julia do look similar to those in Python:

```julia
for <variable> in <iterable>
    <do something>
end
```

The equvalent `for` loop to the previous example for a `while` loop would look like this:

```julia
> for n in 0:4
      println(n^2)
  end
```

`<iterable>` can be anything that can be iterated over and `<variable>` will, one by one, take values from that iterable, e.g., an array.

# Conditionals

## using `if`

The probalbly most-used syntax for a conditional in programming uses the `if` keyword and Julia does the same.
The general syntax of an `if` conditional is as follows:
```julia
if <condition 1>
    <do something>
elseif <condition 2>
    <do something else>
else
    <do something entirely different>
end
```

The blocks `elseif` and `else` are optional, also as usual.

## using the ternary operator

Conditionals can also be written differently in a number of other programming languages, using ternary operators, like this:

```julia
a ? b : c
```
which is equivalent to
```julia
if a
    b
else
    c
end
```
Note that `?` binds rather strongly, so usually you will have to enclose the expression within parentheses:
```julia
(4 > 0) ? "larger than 0" : "equal to or less than zero"
```

