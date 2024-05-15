---
title: "Packages"
teaching: 0
exercises: 0
---

While the module is an organizational unit within a project, packages are the distribution units of Julia.

## Dependency Management

To install a package, we type `]` in the Julia REPL to switch to package mode.
The prompt changes from `julia>` to `(@v1.10) pkg>`.
We can exit package mode by pressing `C-c C-c` or `<Backspace>`.

::: callout

### Pkg.jl

Package mode is implemented by the Julia package
[Pkg.jl](https://github.com/JuliaLang/Pkg.jl). You can find [Pkg.jl’s
documentation](https://pkgdocs.julialang.org/) online.

:::

The prompt in the package mode tells us which environment is currently active (`@v1.10`).
The default environment is named after the running Julia version.
We can install packages into this environment for general experimentation.
Our projects will have their own environments.

First, let us have a look at the status of the current environment:

```shell
(@v1.10) pkg> status
Status `~/.julia/environments/v1.10/Project.toml` (empty project)
```

This shows us the location of the Project.toml that describes the environment.
It also lists explicitly installed packages, in our case currently none.

To install a package we run the command `add`:

```shell
(@v1.10) pkg> add Memoize
    Updating registry at `~/.julia/registries/General.toml`
    Installed Memoize ---- v0.4.4
    Updating `~/.julia/environments/v1.10/Project.toml`
  [c03570c3] + Memoize v0.4.4
    Updating `~/.julia/environments/v1.10/Manifest.toml`
  [1914dd2f] + MacroTools v0.5.13
  [c03570c3] + Memoize v0.4.4
  [2a0f44e3] + Base64
  [d6f4376e] + Markdown
  [9a3f8284] + Random
  [ea8e919c] + SHA v0.7.0
Precompiling project...
   2 dependencies successfully precompiled in 2 seconds
(@v1.10) pkg> status
Status `~/.julia/environments/v1.10/Project.toml`
    [c03570c3] Memoize v0.4.4
```

The command updates two files, `Project.toml` and `Manifest.toml`, and seems to
install six packages, five of those dependencies of `Memoize`.
Now `status` shows us that we have installed `Memoize` (but tells us nothing about its dependencies).

We can update a package using the `update` command:

```shell
(@v1.10) pkg> update Memoize
n    Updating registry at `~/.julia/registries/General.toml`
  No Changes to `~/.julia/environments/v1.10/Project.toml`
  No Changes to `~/.julia/environments/v1.10/Manifest.toml`
```

And we can remove installed packages again with `rm`:

```shell
(@v1.10) pkg> rm Memoize
    Updating `~/.julia/environments/v1.10/Project.toml`
  [c03570c3] - Memoize v0.4.4
    Updating `~/.julia/environments/v1.10/Manifest.toml`
  [1914dd2f] - MacroTools v0.5.13
  [c03570c3] - Memoize v0.4.4
  [2a0f44e3] - Base64
  [d6f4376e] - Markdown
  [9a3f8284] - Random
  [ea8e919c] - SHA v0.7.0
```

To import a package, we need to know what packages there are. There is a
[general package registry](https://github.com/JuliaRegistries/General) which can
be searched through multiple front-ends, for example
https://juliahub.com/ui/Packages .

If we want to install a package that is not in the general (or any other)
registry, we can point to a git source instead:

```shell
(@v1.10) pkg> add https://github.com/JuliaLang/Example.jl#master
     Cloning git-repo `https://github.com/JuliaLang/Example.jl`
    Updating git-repo `https://github.com/JuliaLang/Example.jl`
   Resolving package versions...
    Updating `~/.julia/environments/v1.10/Project.toml`
  [7876af07] + Example v0.5.4 `https://github.com/JuliaLang/Example.jl#master`
    Updating `~/.julia/environments/v1.10/Manifest.toml`
  [7876af07] + Example v0.5.4 `https://github.com/JuliaLang/Example.jl#master`
Precompiling project...
   1 dependency successfully precompiled in 0 seconds
```

All commands discussed here have a lot more options.
Particularly important might be the ability to install specific versions packages or branches of repositories containing packages.
The shell provides documentation for every command when running `help <command>`, for example:

```shell
(@v1.10) pkg> help update
  [up|update] [-p|--project]  [opts] pkg[=uuid] [@version] ...
  [up|update] [-m|--manifest] [opts] pkg[=uuid] [@version] ...

  opts: --major | --minor | --patch | --fixed
        --preserve=<all/direct/none>

  Update pkg within the constraints of the indicated version specifications. These
  specifications are of the form @1, @1.2 or @1.2.3, allowing any version with a
  prefix that matches, or ranges thereof, such as @1.2-3.4.5. In --project mode,
  package specifications only match project packages, while in --manifest mode they
  match any manifest package. Bound level options force the following packages to
  be upgraded only within the current major, minor, patch version; if the --fixed
  upgrade level is given, then the following packages will not be upgraded at all.

  After any package updates the project will be precompiled. For more information
  see pkg> ?precompile.
```

## Creating Packages

After seeing how to use a package, we will have a look into creating one.

Using the shell in the Julia REPL, activated by pressing `;`, we navigate to the folder in which we want to create our project.
For example

```shell
shell> cd ~/temp/
/home/user/temp
```

We leave the shell again by pressing `C-c` and check that we are in the correct
directory, by running `pwd()`:

```julia
> pwd()
"/home/user/temp"
```

Now, we go into package mode (press `]`) and generate a Julia package skeleton:

```shell
(@v1.10) pkg> generate OurVectors
  Generating  project OurVectors:
    OurVectors/Project.toml
    OurVectors/src/OurVectors.jl
```

The output tells us that two files were generated in a new directory with the
project name that we provided. We leave the package mode again, to navigate
Julia into the directory that was just created by pressing `C-c`:

```julia
> cd("OurVectors")

> pwd()
"/home/user/temp/OurVectors"
```

We will have a look at what was created in the shell (press `;`):

```shell
shell> ls -RF
.:
Project.toml  src/

./src:
OurVectors.jl
```

Julia generated two files for us. `Project.toml` is the project configuration
file in TOML Format, while `OurVectors.jl` in the subdirectory `src` is an
initial source file.

Let us first look at the configuration file:

```shell
shell> cat Project.toml
name = "OurVectors"
uuid = "53fd6d90-f326-4ab3-8600-de8f4fd33617"
authors = ["Some One <some.one@example.com>"]
version = "0.1.0"
```

It first states the name of the project (or package). A UUID randomly generated
for the project follows. The authors field is based on your git configuration,
falling back to a [series of environment
variables](https://github.com/JuliaLang/Pkg.jl/blob/f5213cf0019e80004f40e721c16d8a900badb8e4/src/generate.jl#L25).
Finally the initial version is given as `0.1.0`.

The generated source file contains a module with an “Hello World”
implementation:

```shell
shell> cat src/OurVectors.jl
module OurVectors

greet() = print("Hello World!")

end # module OurVectors
```

## Tests

We edit the source file to contain our `length` function:

```julia
module OurVectors

length(v) = sqrt(sum(v.^2))

end # module
```

Now, we want to write tests for our function. Idiomatically, tests are run by
evaluating the file `test/runtests.jl` of a Julia project. We create such a file
and make use of the `Test` package that comes bundled with Julia:

```julia
using Test
using OurVectors

@test OurVectors.length([1]) == 1.0
@test OurVectors.length([3, 4]) == 5.0
@test_throws MethodError OurVectors.length([])
```

To run the script we can use the package mode again. But before switching to it
(by pressing `]`), we make sure that the working directory of our REPL is the
project directory:

```julia
> pwd()
"/home/user/temp/OurVectors"
```

This looks good. If we were in the wrong directory, we would use the function
`cd` to change that.

In package mode, we first have to make our project the current environment. To
do that, we run

```shell
(@v1.10) pkg> activate .
  Activating project at `~/temp/OurVectors`

(OurVectors) pkg>
```

Note, how the prompt changes when switching environments.

To run tests, we can type `test` in package mode:

```shell
(OurVectors) pkg> test
     Testing OurVectors
      Status `/tmp/jl_Bx3UOG/Project.toml`
  [53fd6d90] OurVectors v0.1.0 `~/temp/OurVectors`
      Status `/tmp/jl_Bx3UOG/Manifest.toml`
  [53fd6d90] OurVectors v0.1.0 `~/temp/OurVectors`
     Testing Running tests...
ERROR: LoadError: ArgumentError: Package Test not found in current path:
- Run `import Pkg; Pkg.add("Test")` to install the Test package.

Stacktrace:
 [1] include(fname::String)
   @ Base.MainInclude ./client.jl:489
 [2] top-level scope
   @ none:6
in expression starting at /home/user/temp/OurVectors/test/runtests.jl:1
ERROR: Package OurVectors errored during testing
```

This throws an error. Even though `Test` comes with the Julia installation, we
still need to install it into our environment:

```shell
(OurVectors) pkg> add Test
   Resolving package versions...
    Updating `~/temp/OurVectors/Project.toml`
  [8dfed614] + Test
    Updating `~/temp/OurVectors/Manifest.toml`
  [2a0f44e3] + Base64
  [b77e0a4c] + InteractiveUtils
  [56ddb016] + Logging
  [d6f4376e] + Markdown
  [9a3f8284] + Random
  [ea8e919c] + SHA v0.7.0
  [9e88b42a] + Serialization
  [8dfed614] + Test
```

This installs `Test` and its dependencies. It also seems to make changes to
`Project.toml` and a yet unknown file called `Manifest.toml`. We will have a
look at them in a minute. But first, we try to again run the tests:

```shell
(OurVectors) pkg> test
     Testing OurVectors
      Status `/tmp/jl_3CRrFm/Project.toml`
  [53fd6d90] OurVectors v0.1.0 `~/temp/OurVectors`
  [8dfed614] Test `@stdlib/Test`
      Status `/tmp/jl_3CRrFm/Manifest.toml`
  [53fd6d90] OurVectors v0.1.0 `~/temp/OurVectors`
  [2a0f44e3] Base64 `@stdlib/Base64`
  [b77e0a4c] InteractiveUtils `@stdlib/InteractiveUtils`
  [56ddb016] Logging `@stdlib/Logging`
  [d6f4376e] Markdown `@stdlib/Markdown`
  [9a3f8284] Random `@stdlib/Random`
  [ea8e919c] SHA v0.7.0 `@stdlib/SHA`
  [9e88b42a] Serialization `@stdlib/Serialization`
  [8dfed614] Test `@stdlib/Test`
     Testing Running tests...
     Testing OurVectors tests passed
```

Now the test suite runs and our tests passed.

## Tracking Dependencies

Getting back to the file that changed, when we add `Test` to the project, we
first have look at `Project.toml`:

```toml
…
[deps]
Test = "8dfed614-e22c-5e08-85e1-65c5234f0b40"
```

The file got a new section, `[deps]`, listing dependencies. We added `Test`, so
it shows up in the list with its UUID.

Now for the content of the new file, `Manifest.toml`:

```toml
# This file is machine-generated - editing it directly is not advised

julia_version = "1.9.0"
manifest_format = "2.0"
project_hash = "71d91126b5a1fb1020e1098d9d492de2a4438fd2"

[[deps.Base64]]
uuid = "2a0f44e3-6c83-55bd-87e4-b1978d98bd5f"

[[deps.InteractiveUtils]]
deps = ["Markdown"]
uuid = "b77e0a4c-d291-57a0-90e8-8db25a27a240"

[[deps.Logging]]
uuid = "56ddb016-857b-54e1-b83d-db4d58db5568"

[[deps.Markdown]]
deps = ["Base64"]
uuid = "d6f4376e-aef5-505a-96c1-9c027394607a"

[[deps.Random]]
deps = ["SHA", "Serialization"]
uuid = "9a3f8284-a2c9-5f02-9a11-845980a1fd5c"

[[deps.SHA]]
uuid = "ea8e919c-243c-51af-8825-aaa63cd721ce"
version = "0.7.0"

[[deps.Serialization]]
uuid = "9e88b42a-f829-5b0c-bbe9-9e923198166b"

[[deps.Test]]
deps = ["InteractiveUtils", "Logging", "Random", "Serialization"]
uuid = "8dfed614-e22c-5e08-85e1-65c5234f0b40"
```

This file lists not only the explicitly installed dependencies, but the whole
dependency tree.

When we added the `Test` package, we did not specify a version. If we had done
that, this would be recorded in `Manifest.toml`.
