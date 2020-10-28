# CMake generation

* Owner: @Oursin @gabrielcolson
* Stakeholders:
* State
	* Spec: In progress
	* Implementation: Missing

This specification describes the CMake scripts generation by
the c3pm CLI.

----

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**

- [General description](#general-description)
- [Executable](#executable)
- [Library](#library)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## General description

The c3pm CLI will generate the CMake scripts. There are two types
of project, executable and library, and their configuration are
slightly different.

The CMake scripts will be generated on the user's file system to
make the IDEs support easier.

## Executable

```
cmake_minimum_required(VERSION 3.16)
project({{PROJECT_NAME}} VERSION {{PROJECT_VERSION}})

set(CMAKE_CXX_STANDARD 20)

add_executable({{PROJECT_NAME}})

target_sources({{PROJECT_NAME}} PRIVATE {{SOURCES}} {{INCLUDES}})
target_include_directories({{PROJECT_NAME}} PRIVATE {{INCLUDE_DIRS}})
```

## Library

```
cmake_minimum_required(VERSION 3.16)
project({{PROJECT_NAME}} VERSION {{PROJECT_VERSION}})

set(CMAKE_CXX_STANDARD 20)

add_library({{PROJECT_NAME}} STATIC)

target_sources({{PROJECT_NAME}} PRIVATE {{SOURCES}} {{INCLUDES}})
target_include_directories({{PROJECT_NAME}} PRIVATE {{INCLUDE_DIRS}})

set_target_properties({{PROJECT_NAME}} PROPERTIES PUBLIC_HEADER {{EXPORTS}})

install(TARGETS {{PROJECT_NAME}}
        ARCHIVE DESTINATION $ENV{HOME}/.c3pm/lib/{{PROJECT_NAME}}/{{PROJECT_VERSION}}
        PUBLIC_HEADER DESTINATION $ENV{HOME}/.c3pm/include/{{PROJECT_NAME}}/{{PROJECT_VERSION}}
        )
```
