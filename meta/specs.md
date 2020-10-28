# Specs

* Owner: @Oursin
* Stakeholders:
* State
    * Spec: Stable
    * Implementation: Implemented

This is a specification for how to write specifications. It contains a minimal definition
of what a spec should look like.

----

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**

- [Minimal Format](#minimal-format)
  - [Example](#example)
- [Implementation status](#implementation-status)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Minimal Format
* A spec start with `# Topic`, which should be the same as the folder or file name 
of the spec.
* The spec begins with some metadata:
    * Owner: The person responsible for the spec.
    * Stakeholders: Other people interested in the spec.
    * State: Describes the progress of the spec, and it's implementation.
        * Spec: The progress of the spec itself. It should be one of
            * Stable
            * In Progress
            * Incomplete
            * Missing
            * Outdated
            * Unknown
        * Implementation: The progress of the spec's implementation
            * Implemented
            * In progress
            * Future
            * Outdated
            * Unknown
* A first paragraph introducing the spec, that gives a first idea about what the spec is about.
* A horizontal line to separate the header from the body of the specification.
* The body of the spec, starting with a `## Section` to start the first content section.
* Any number of sections (and any `###Subsections` needed).

### Example
```md
# Spec Name

* Owner: @Owner
* Stakeholders: @sh1 @sh2
* State
    * Spec: Stable
    * Implementation: Future

This spec is an example of specs.

----

## First section

Sample text
```

## Implementation status

We defined two extremes states of the implementation of a spec: `Future` and `Implemeted`.
Since `In Progress` means that some parts of the spec are implemented but not all, it
can be useful to specify which parts are not implemented yet.

To indicate which parts are to be implemented, we add this warning (which is created with
this markdown: `> **Warning**: This is not implemented yet.`):
   
> **Warning**: This is not implemented yet.

The warning text can be modified to add context information, such as a link to a
relevant issue.
