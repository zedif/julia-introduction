---
title: "Loops"
teaching: 20
exercises: 0
---

::: questions

- What basic loop constructs does Julia offer?

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

