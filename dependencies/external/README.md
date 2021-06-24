# Native and platform-specific dependencies

This document specifies how C3PM means to implement handling for both native and platform specific dependencies. Both terms are defined as follows:

- We define a **platform-specific** dependency as a dependency one needs only on one specific platform. For example one might want to run additional code only on Linux-based platforms, or only on 32bit systems.
- We define a **native** dependency as a dependency that is not meant to be handled by C3PM. Such dependencies often include low-level libraries, such as libX11 or libwayland which are pretty common dependencies for graphical applications and libraries on Linux-based systems.

This document is structured as follows:

0. An exploration of already existing systems to tackle both these kinds of dependencies.
1. A proposal on how to let packages using C3PM specify their dependencies.
2. A proposal on how to implement handling those dependencies in C3PM.

## Part 0 - Exploration

This part is meant as an exploration of how pre-existing package managers handle those dependencies.

* Vcpkg (C++):
  * **Native dependencies**: Vcpkg assumes your native dependencies are installed in order to build properly, and defaults to the system import and link path when asked for those dependencies.
  * **Platform-specific dependencies**: Vcpkg allows package developers to replace the name of a dependency by a JSON object containing a `platform` field, which will contain a description of when to trigger this specific dependency, ex: `!(windows & static) & !osx`. Additionally, Vcpkg also allows package maintainers to specify which platforms their package supports, with the `supports` key in the `vcpkg.json`.
* Npm (Node.js):
  * **Native dependencies**: NPM assumes the native dependencies are already installed, and fails to build otherwise (see https://github.com/nodegit/nodegit/issues/728#issuecomment-247030632).
  * **Platform-specific dependencies**: NPM allows an `os` key in the `optionalDependencies` (https://docs.npmjs.com/cli/v7/configuring-npm/package-json#os) which is a list of the OSes the package is available on.
*  opam (OCaml):
  * **Native dependencies** (still in beta at time of writing): opam defines a `depext` key to specify external dependencies. In practice, this is used by "virtual packages", i.e. packages that do not contain any code, but define external dependencies, to ease the integration with existing packages. See here for an example: https://github.com/ocaml/opam-repository/blob/master/packages/conf-libX11/conf-libX11.1/opam
  * **Platform-specific dependencies**: Opam integrates platform-specific dependencies directly in it's `filter` syntax for specifying which dependency is needed, e.g.: `depends: [ "foo" {>= "3.0" & < "4.0~" & os = "linux"} ]`



## Part 1 - Specifying platform-specific and native dependencies

This part only focuses on how packages handled by C3PM indicate that they want to depend on those types of dependencies.

### Platform-specific dependencies

We introduce an optional syntax for specifying a dependency. For reference, the current way to declare dependencies is as follows:

```yaml
dependencies:
  boost-asio: 1.0.1
  yaml-cpp: 3.2.0
```

We introduce an alternative to this syntax, allowing a YAML object in place of the version string. This YAML object must contain at least a `version` key to be equivalent to the current syntax, thus the previous example rewritten with the new syntax would look like this:

```yaml
dependencies:
  boost-asio:
    version: 1.0.1
  yaml-cpp:
    version: 3.2.0
```

In addition to this new syntax, we introduce a `system` key in the dependency YAML object. This key accepts a platform requirement string (see below for the specification of the platform requirement string itself). In the above example, if we only needed boost-asio on Linux x86 32bit systems, would look like this:

```yaml
dependencies:
  boost-asio:
    version: 1.0.1
    system: linux & i386
  yaml-cpp:
    version: 3.2.0
```

Note that since we don't need a `system` key for the `yaml-cpp` dependency, this is also totally valid:

```yaml
dependencies:
  boost-asio:
    version: 1.0.1
    system: linux & i386
  yaml-cpp: 3.2.0
```

### Native dependencies

For the handling of native dependencies, inspired by the work done on opam (see above for details on how opam handles native dependencies), we introduce a new package type, named `virtual`. This type of package is meant to wrap system-wide dependencies in easy-to-use abstractions for C3PM users. The `c3pm.yml` manifest for a virtual package is as follows.

```yaml
c3pm_version: v1
type: virtual
name: libx11
description: "A virtual package for the X11 library"
version: 1.0.0
external:
  - system: "debian"
    packages:
      - libx11-dev
  - system: "fedora"
    packages:
      - libX11-devel
```

To sum up, this kind of packages only depend on a new `external` top-level key, used to describe native dependencies. `external` is a list of objects, each defining a `system` and its `packages`. `system` is a platform requirement string, as defined below, while `packages` contains a list of package names to be present on the host machine.

### Platform requirement strings

Platform requirement strings are used for both native and platform-specific declarations. They are meant to express a specific type of system on which C3PM operates. These strings are composed of three types of tokens, operators,  platform declarators and parentheses. Parentheses are only meant for disambiguation for operator priority.

* Operators are simple logic operators which operate on plaform declarators. A platform requirement string can be composed of as many such operators as needed. They are as follows:
  * `&` the AND operator.
  * `|` the OR operator.
  * `!` the NOT operator.
* Platform declarators are string describing a particular state of a system. We define two kinds of platform declarator, OS and architecture ones:
  * OS declarators are currently defined as:
    * `linux`
    * `debian`
    * `ubuntu`
    * `alpine`
    * `fedora`
    * `arch`
    * `macos`
    * `windows`
  * Architecture declarators are currently defined as:
    * `i386` (x86 32 bit)
    * `amd64` (x86 64 bit)

For example, this is a valid platform requirement string:

```
(macos | (linux & !arch)) & amd64
```

This would target a system that is either macOS X, or any linux-based system that is not ArchLinux, but only for 64bit systems.

## Part 2 - Implementation

This part is the most subjective of this document, and the most likely to change in the future.

First, the common part of these two features is the platform requirement string, so we'll need a new module to handle those, with an interface like this one:

```go
func CheckPlatform(requirement string) (bool, error) {}
```

### Platform-specific

* Add support for two types of YAML objects for the dependencies, that will be handled as a single new dependency type:

  ```go
  type Dependency struct {
      Version semver.Version `yaml:"version"`
      System string `yaml:"system"`
  }
  ```

* Give this object to the dependency resolution system, and check the platform requirement string before adding a new dependency.

### Native

I suggest we implement support for native dependencies through the addition of a new adapter, to stay coherent with the rest of the codebase.

It will probably require a bit of work to enable the choice of an adapter based on the type of package and not the `build` config.

The biggest part will of course be the implementation of this adapter, with the need to support checking installation and installing system packages for every system dependency.

It will likely also require a bit of specific handling in the long run, since we'll probably want to check all system dependencies needed for a specific installation before downloading and building the packages that depend on those system packages.

