# Specs

* Owner: @Oursin
* Stakeholders: @gabrielcolson @Codelax
* State
    * Spec: In Progress
    * Implementation: In Progress

This is a specification about the behavior of C3PM's command line interface.

----

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**

- [General Presentation](#general-presentation)
  - [`add`](#add)
    - [Command format](#command-format)
    - [Options](#options)
  - [`build`](#build)
    - [Options](#options-1)
  - [`init`](#init)
    - [User interaction](#user-interaction)
    - [Options](#options-2)
  - [`logout`](#logout)
  - [`login`](#login)
    - [User interaction](#user-interaction-1)
  - [`publish`](#publish)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## General Presentation


### `add`

This commands adds a new dependency to the current project. It downloads, unpacks, builds and installs the dependency
following the [dependency handling spec](../dependencies/README.md).

#### Command format

```bash
c3pm add [packages ...] [options]
```

`packages` is a list of terms, each indicating a package to add as a dependency. Each entry should follow the format:

```
name[@version]
```

Where `name` is the name of the package to install, and `version` describes the version to install, defaulting to `latest`.

#### Options

* `--force` - Will redo the whole installation process even if the package is already installed.

### `build`

This command compiles the current project. It generates the cmake scripts following the
configuration in `c3pm.yml` then executes the `cmake` and `make` commands.

It stores the generated files following the [.c3pm directory specs](../c3pm_directory/README.md).

The command then copies the build output in the project's root.

#### Options

* `--output, -o <dest>` - change the output file name

### `init`

This command initializes a project so that the user can start programming right away.

By default, running this command will create a few files:

* A [c3pm.yml file](../config_file/README.md).
* Depending on the user's choice, a project template of either a library or an executable.
* A CMakeLists.txt file.
* A [.c3pm directory](../c3pm_directory/README.md).
* Depending on the user's choice, a LICENSE file.

The command accepts one positional argument, indicating the directory in which to init the project.
If the user doesn't provide any arguments, the project will be initialized in the current directory.

#### User interaction

In order to init the project correctly, the CLI will ask some information about it.

* The **type** of the project, which can be either `executable` or `library`.
* The **name** of the project.
* A short **description** of the project.
* The **version** of the project, using SemVer format.
* The **licence** to use.

#### Options

In addition, this command has some options to alter its behavior.

* `--no-template` - will not create a code template.

### `logout`

This command logs the user out of the application.

It must delete the authentification key from the [user-level c3pm directory](../c3pm_directory/README.md).

### `login`

This command logs the user in with the backend.

It should ask the user for it's credentials, send them to the backend, then store the user key retreived in the [user-level c3pm directory](../c3pm_directory/README.md).

#### User interaction

This command will ask the following information to the user:

* Its **username**.
* Its **password**.

This information must be sent via HTTPS to the server to avoid any credentials leaks.

### `publish`

The publish command handles the package publication process.

It must first check if an authentication file exists under the [user-level .c3pm directory](../c3pm_directory/README.md), and aborts if none exists. Otherwise, it must follow the [publication process specification](../dependencies/README.md).
