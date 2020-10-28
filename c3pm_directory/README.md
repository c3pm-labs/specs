# .c3pm directory

* Owner: @Oursin
* Stakeholders: @gabrielcolson @Codelax
* State
  * Spec: In Progress
  * Implementation: Missing

This specification defines the structure of the `.c3pm` directory, found at the root of each c3pm project, as well as the `$HOME/.c3pm` directory.

----

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**

- [General presentation](#general-presentation)
- [Project-local .c3pm directory](#project-local-c3pm-directory)
- [User-level .c3pm directory](#user-level-c3pm-directory)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## General presentation

The `.c3pm` directory hosts all files used by c3pm that are not supposed to be edited by the
average user.

## Project-local .c3pm directory

In the `.c3pm` directory found at the root of a project, the CLI stores the following files:
* All CMake-related files, in a `cmake` subdirectory.
* All build-related files, in a `build` subdirectory.

## User-level .c3pm directory

The `$HOME/.c3pm` directory contains the installation path of all the code downloaded by c3pm.
The directory's structure is as follows:

* The `/bin` subdirectory contains all executables related to c3pm.
* The `/include` subdirectory contains all header files from libraries downloaded via c3pm.
The headers will be stored in subdirectories following this pattern: `/include/<name>/<version>`.
* The `/lib` subdirectory contains all binary releases of libraries downloaded via c3pm. The libraries will be stored in subdirectories following this pattern: `/lib/<name>/<version>`.
* The `/auth.cfg` stores the user authentication key.
