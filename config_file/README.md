# c3pm.yml

* Owner: @Oursin
* Stakeholders: @gabrielcolson
* State
	* Spec: In progress
	* Implementation: Missing

This specification is about c3pm.yml, the main configuration file for the CLI.

----

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**

- [General description](#general-description)
- [Fields](#fields)
  - [`c3pm_version` - string](#c3pm_version---string)
  - [`type` - string](#type---string)
  - [`name` - string](#name---string)
  - [`description` - string](#description---string)
  - [`version` - semver string](#version---semver-string)
  - [`license` - string](#license---string)
  - [`files` - object](#files---object)
    - [`sources` - string|string array](#sources---stringstring-array)
    - [`includes` - string|string array](#includes---stringstring-array)
    - [`include_dirs` - string array](#include_dirs---string-array)
    - [`exports` - string|string array](#exports---stringstring-array)
  - [`dependencies` - object](#dependencies---object)
    - [Example](#example)
- [Example](#example-1)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## General description

The c3pm.yml file stores various information about a c3pm project.
It must be written using the [YAML](https://yaml.org) language.

## Fields

### `c3pm_version` - string

> Required, Should be `v1`

The version of the c3pm.yml specification used by the file.

### `type` - string

> Required

Either `executable` or `library`. Indicates the type of project to use.

### `name` - string

> Required
>
> May only contain alphanumeric characters, dashes (-), and underscores (_). May only start
> with an alphabetic character. May only contain 255 or fewer characters.

This field stores the name of the project, which will be displayed by the platform and
used by users to find the package and add it as a dependency.

### `description` - string

> Default: `""`

This field provides additional information about the package.

### `version` - [semver](https://semver.org) string

> Default: `v0.0.0`

The current version of the package, following [semver](https://semver.org) convention.

### `license` - string

> Default: `UNLICENSED`

This field contains a string identifying the license used by the project. Its possible values are:
* An [SPDX ID](https://spdx.org/licenses/).
* The special value `UNLICENSED`.
* The special value `SEE LICENSE IN <filename>`, where `<filename>` refers to an existing file in the project.

### `files` - object

> Default:
> ```yaml
> files:
>   sources: '**/*.cpp'
>   includes: '**/*.hpp'
>```

This object lists the rules to help c3pm find the source files of the project.

It contains the following fields:
   
#### `sources` - string|string array

Either a string containing a globbing expression or an array of file names listing the source files to use in order to build the project.

#### `includes` - string|string array

Either a string containing a globbing expression or an array of file names listing the headers to use in order to build the project.

#### `include_dirs` - string array

This string array lists the directories to use as include directories in the CMake generation.
This enables the user to include files like this `#include <dir/header.h>`.

#### `exports` - string|string array

Either a string containing a globbing expression or an array of file names listing the headers to export to other packages.

### `dependencies` - object

> Default: `{}`

This field lists the dependencies of the project.

Each property of this object should have the package name as its key, and the version as its value.

#### Example

```yaml
dependencies:
    boost: 2.0.0
    sfml: 300.5.20
```

## Example

Here is a full example of a c3pm.yml file.

```yaml
c3pm_version: v1
type: library
name: c3pm
description: This is the package c3pm
version: 1.0.0
license: ISC
files:
    sources: 'src/**/*.cpp'
    includes:
        - include/private/header.h
        - include/private/private.h
    include_dirs:
        - include/private
    exports: 'include/public/**/*.h'
dependencies:
    future: 12.2.3
    past: 2.0.0
```
