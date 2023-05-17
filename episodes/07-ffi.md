---
title: "Foreign Function Interface"
teaching: 0
exercises: 0
---

Julia has built-in functions and data types to interface with C and Fortran
code. There are also libraries to interface with Python and R. We will have a
look at the interfaces for C and Python.

Before we continue, we return to the default environment in our Julia REPL:

```shell
(OurVectors) pkg> activate
  Activating project at `~/.julia/environments/v1.9`
```

## C Interface

To call a C function we can either use the function `ccall` or the macro
`@ccall`. For example:

```julia
> ccall(:getenv, Cstring, (Cstring,), "LANG")
Cstring(0x00007ffe2e3eddd9)

> unsafe_string(ans)
"en_US.UTF-8"
```

`ccall` takes the function name, followed by the return type, a tuple of
argument types, and finally the arguments themselves. We can see, that there are
special types defined to interface with `C`, e.g. `Cstring`.

The macro `@ccall` offers an alternate syntax:

```julia
> @ccall getenv("LANG"::Cstring)::Cstring
CString(0x00007ffe2e3eddd9)
```

The type annotations are found at the arguments and after the argument list for
the return type, maybe making the intent a little clearer.

Other than C standard library functions, we can also call any function found on
the path:

```julia
> ccall((:g_basename, "libglib-2.0"), Cstring, (Cstring,), "/usr/bin/bash")
Cstring(0x00007f7173d0a681)

> unsafe_string(ans)
"bash"
```

Julia will automatically try to load `libglib-2.0` in this case.

With the macro `@ccall`, the same call would look as follows

```julia
> @ccall "libglib-2.0".g_basename("/usr/bin/bash"::Cstring)::Cstring
Cstring(0x00007f26ff113e81)
```

## PyCall

To interface with python we can use the `PyCall` package. First, we need to
install it to the current repository:

```shell
(@v1.9) pkg> add PyCall
    Installed Preferences ----- v1.4.0
     Updating `~/.julia/environments/v1.9/Project.toml`
  [438e738f] + PyCall v1.93.1
    Updating `~/.julia/environments/v1.9/Manifest.toml`
  [8f4d0f93] + Conda v1.8.0
  …
```

pThen we can import any installed python library:

```julia
> using PyCall

> datetime = pyimport("datetime")
PyObject <module 'datetime' from '/usr/lib/python3.10/datetime.py'>

> datetime.MAXYEAR
9999
```

::: callout

### Python Environments

We can control the version of Python that is used by Julia with the environment
variable `PYTHON` in the Julia process.

We could, for example, start the Julia process from a shell in which we
activated a particular environment of the `venv` Python package. This would give
the Julia process access to that environments Python version and installed
packages.

:::

We can also execute Python code, through a special syntax:

```julia
> py"[x for x in [1, 2, 3] if x % 2 == 1]"
2-element Vector{Int64}:
 1
 3

> py"""
import datetime

print(datetime.datetime.now())
"""
2022-06-03 14:38:05.903444
```

## RCall

A similar package exists for R with `RCall`. It even gives access to an R-REPL
from within the Julia REPL, similar to package and shell mode.
