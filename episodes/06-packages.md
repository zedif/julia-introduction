---
title: "Packages"
teaching: 0
exercises: 0
---

While the module is an organizational unit within a project, packages are the
distribution units of packages.

## Dependency Management

To install a package, we type `]` in the Julia REPL to switch to package mode.
The prompt changes from `julia>` to `(@v1.7) pkg>`. We can exit package mode by
pressing `C-c`.

The prompt in the package mode tells us which environment is currently active
(`@v1.7`). The default environment is named after the running Julia version. We
can install packages into this environment for general experimentation. Our
projects have their own environments, as we will see in a bit.

First, let us have a look at the status of the current environment:

```shell
(@v1.7) pkg> status
      Status `~/.julia/environments/v1.7/Project.toml`
```

This shows us the location of the Project.toml that describes the environment.
It also lists explicitly installed packages, in our case currently none.

To install a package we run the command `add`:

```shell
(@v1.7) pkg> add Memoize
   Resolving package versions...
    Updating `~/.julia/environments/v1.7/Project.toml`
  [c03570c3] + Memoize v0.4.4
    Updating `~/.julia/environments/v1.7/Manifest.toml`
  [1914dd2f] + MacroTools v0.5.9
  [c03570c3] + Memoize v0.4.4
(@v1.7) pkg> status
      Status `~/.julia/environments/v1.7/Project.toml`
    [c03570c3] Memoize v0.4.4
```

The command updates two files, `Project.toml` and `Manifest.toml`, and seems to
install two packages, `Memoize` and `MacroTools`, the latter probably a
dependency of the former. Now `status` shows us that we have installed `Memoize`
(but tells us nothing about `MacroTools`.

We can update a package using the `update` command:

```shell
(@v1.7) pkg> update Memoize
    Updating registry at `~/.julia/registries/General.toml`
  No Changes to `~/.julia/environments/v1.7/Project.toml`
  No Changes to `~/.julia/environments/v1.7/Manifest.toml`
```

And we can remove installed packages again with `rm`:

```shell
(@v1.7) pkg> rm Memoize
    Updating `~/.julia/environments/v1.7/Project.toml`
  [c03570c3] - Memoize v0.4.4
    Updating `~/.julia/environments/v1.7/Manifest.toml`
  [1914dd2f] - MacroTools v0.5.9
  [c03570c3] - Memoize v0.4.4
```

To import a package, we need to know what packages there are. There is a
[general package registry](https://github.com/JuliaRegistries/General) which can
be searched through multiple front-ends, for example
https://juliahub.com/ui/Packages .

If we want to install a package that is not in the general (or any other)
registry, we can point to a git source instead:

```shell
(@v1.7) pkg> add https://github.com/JuliaLang/Example.jl#master
     Cloning git-repo `https://github.com/JuliaLang/Example.jl`
    Updating git-repo `https://github.com/JuliaLang/Example.jl`
   Resolving package versions...
    Updating `~/.julia/environments/v1.7/Project.toml`
  [7876af07] + Example v0.5.4 `https://github.com/JuliaLang/Example.jl#master`
    Updating `~/.julia/environments/v1.7/Manifest.toml`
  [7876af07] + Example v0.5.4 `https://github.com/JuliaLang/Example.jl#master`
Precompiling project...
   1 dependency successfully precompiled in 0 seconds (20 already precompiled)
```

All commands discussed here have a lot more options. Particularly important
might be the ability to install specific versions or branches of packages. The
shell provides documentation for every command when running `help <command>`,
for example:

```shell
(@v1.7) pkg> help update
  [up|update] [-p|--project]  [opts] pkg[=uuid] [@version] ...
  [up|update] [-m|--manifest] [opts] pkg[=uuid] [@version] ...

  opts: --major | --minor | --patch | --fixed

  Update pkg within the constraints of the indicated version specifications. These specifications are of the form
  @1, @1.2 or @1.2.3, allowing any version with a prefix that matches, or ranges thereof, such as @1.2-3.4.5. In
  --project mode, package specifications only match project packages, while in --manifest mode they match any
  manifest package. Bound level options force the following packages to be upgraded only within the current major,
  minor, patch version; if the --fixed upgrade level is given, then the following packages will not be upgraded at
  all.
```
