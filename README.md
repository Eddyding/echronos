<!---
eChronos Real-Time Operating System
Copyright (C) 2015  National ICT Australia Limited (NICTA), ABN 62 102 206 173.

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU Affero General Public License as published by
the Free Software Foundation, version 3, provided that these additional
terms apply under section 7:

  No right, title or interest in or to any trade mark, service mark, logo
  or trade name of of National ICT Australia Limited, ABN 62 102 206 173
  ("NICTA") or its licensors is granted. Modified versions of the Program
  must be plainly marked as such, and must not be distributed using
  "eChronos" as a trade mark or product name, or misrepresented as being
  the original Program.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU Affero General Public License for more details.

You should have received a copy of the GNU Affero General Public License
along with this program.  If not, see <http://www.gnu.org/licenses/>.

@TAG(NICTA_DOC_AGPL)
  -->
[![Build Status](https://travis-ci.org/echronos/echronos.svg?branch=master)](https://travis-ci.org/echronos/echronos)

# Yes, We Are Open for Business

If you have any questions, send us an e-mail to echronos@trustworthy.systems or tweet at us [@echronosrtos](https://twitter.com/echronosrtos).

If there is something in the project that you think is worth improving, please create a [github issue](https://github.com/echronos/echronos/issues).

Of course, we are also keen on your changes and contributions if you have any - [here is a primer](CONTRIBUTING.md).


# Overview

The eChronos RTOS is a real-time operating system (RTOS) originally developed by NICTA and Breakaway Consulting Pty. Ltd.

It is intended for tightly resource-constrained devices without memory protection.
To this end, the RTOS code base is designed to be highly modular and configurable on multiple levels, so that only the minimal amount of code necessary is ever compiled into a given system image.

Available implementations currently target ARM Cortex-M4, and (as of writing, only QEMU-emulated) PowerPC e500.


# Quick-start

## Download

Download the [latest _posix_ release](https://github.com/echronos/echronos/releases) and unpack it in a directory of your choice.

The project makes frequent releases, based on improvements flowing into the master branch of the git repository.
For each version, there are different release archives for different target platforms.
The _posix_ release is aimed at running on top of any POSIX host operating system, such as Linux or Windows (with Cygwin or MinGW).

## Tools

You need the following tools:

- Python 3
- GCC compiler + GNU binutils

On Linux, use your package manager to install these tools.
On Windows, obtain and install Python 3 from [python.org](https://www.python.org) and install either [Cygwin](https://cygwin.com) or [MinGW](http://mingw.org) including the GCC compiler.

## Build

The following commands build a simple version of the eChronos RTOS together with a small example application:

    cd eChronos-posix-VERSION
    ./prj/prj.sh build posix.acamar

On Windows, run `prj\prj` instead of `./prj/prj.sh`.

This produces the binary `out/posix/acamar/system`(`.exe` on Windows).

## Run

Run the sample system with the command `./out/posix/acamar/system`.
It prints `task a` and `task b` to the screen until you stop it by pressing `ctrl-c`.

If you have GDB installed, you can also run this RTOS system under the debugger.
Start GDB with `gdb out/posix/acamar/system` and

- set a break point with `b debug_print`
- start the system with `run`
- gdb now stops the system just before it prints `task a` or `task b`, allowing you to inspect the system state or to continue with `continue`

## Under the Hood

### The `prj` Tool

To give you an idea of what goes on when building an running an RTOS system as above, here is a quick overview of what happens under the hood.

The `prj` tool is the build tool of the RTOS.
Its primary purpose is to
- retrieve the system configuration,
- generate RTOS code specifically for the system configuration,
- and build the RTOS and application code into a single system binary.

The command `./prj/prj.sh build posix.acamar` makes `prj` first search for a system configuration file with a `.prx` file name extension.
Think of PRX files as make files.
`prj` finds that system configuration file at [`share/packages/posix/acamar.prx`](packages/posix/acamar.prx), based on the search paths set up in [`project.prj`](prj/release_files/project.prj).

`acamar.prx` lists all the files that go into building this system plus some configuration information.
For example, this system is configured with two tasks, _a_ and _b_, that have given entry point functions and stack sizes.

In the second step, `prj` uses this configuration information to generate a custom copy of the RTOS source code.
This makes the RTOS itself as small as possible to leave more resources for the application.

The third step is to invoke the compiler and linker to build all the source code files listed in the system configuration into a binary.

### The Sample Application

The file [`share/packages/rtos-example/acamar-test.c`](packages/rtos-example/acamar-test.c) contains the main application code of the example system (the PRX file refers to it as `rtos-example.acamar-test`).
This file implements two tasks that perpetually print their name and yield to each other.

You will notice that this file also contains the standard `main()` function found in all C programs.
If necessary, it could, for example, initialize some hardware before starting the RTOS, which in turn starts the two tasks.

### What is _Acamar_?

The eChronos project is not a single RTOS, but provides a family of RTOS variants with different feature sets.
Acamar is the name of the smallest one, but the POSIX release comes with a number of other, more powerful variants.
They provide "proper" RTOS features, such as mutexes, interrupts, and timers.
The [Variants and Components](#variants-and-components) section has more information on this topic.


# Documentation

The rest of this README covers all basic RTOS concepts and how to make use of them.
It refers to the full [source code repository](https://github.com/echronos/echronos/), not just a release as the [Quick-start Guide](#quick-start) did above.

More detailed documentation for the `x.py` tool can be found inside [`x.py`](x.py) itself.
More detailed documentation for the `prj` tool can be found in [`prj/manual/prj-user-manual`](prj/manual/prj-user-manual).

Pregenerated RTOS API manuals can be found on the [eChronos GitHub wiki](https://github.com/echronos/echronos/wiki).
They can also be generated with the command `x.py build docs`.

# Software model

The software model and structure of the RTOS is governed by two stages of customization.

In the first stage, features, in the form of *components*, are customized for and composed into *variants* of the RTOS such that each variant has a specific feature set.
This stage is supported by the `x.py` tool.

In the second stage, the RTOS variant is customized to the specific properties and requirements of a specific application.
Although this customization is limited to the functionality provided by the given variant, it controls details such as the number of tasks required by the application.
This stage is supported by the `prj` tool.

The two stages can optionally be separated by deploying a *product release* to an application project.
The application project is then only exposed to the second stage and the variant and functionality of the RTOS they require.

The following sections cover these concepts in more detail.

## Variants and Components

The RTOS comes in a number of different *variants*, each offering a specific set of features for a particular platform.

For example:

* The RTOS variant *Rigel* supports tasks, co-operative round-robin scheduling, mutexes, signals, and interrupt events which can trigger the sending of signals.
It is available for QEMU-emulated ARMv7-M.

* The RTOS variant *Kochab* supports tasks, preemptive priority scheduling, mutexes with priority inheritance, semaphores, signals, and interrupt events which can cause task preemption and trigger the sending of signals.
It is available for the ARMv7-M STM32F4-Discovery board, and QEMU-emulated PowerPC e500.

Features are implemented as individual *components* in individual C files.
Unlike typical C-based software, the RTOS does not compile components individually and later link them into a single binary.
Instead, the RTOS `x.py` tool merges the component files of a variant into a single C file called the *RTOS module*.
The feature set of each RTOS variant is specified within the `x.py` tool itself.
This allows for better compile-time optimizations.

The `x.py` tool also supports building *product releases* of the RTOS.
A product release bundles a set of variants and target platforms tailored for a certain application product that builds on the RTOS.
Thus, the application product sees only what it needs without being needlessly exposed to all features and platforms supported by the RTOS.
The variants and platforms contained in a release are defined by [`release_cfg.py`](release_cfg.py).


## Systems, Modules and Packages

An RTOS *system* encompasses the entirety of the OS and an application on top of it.
It consists in particular of:
- the OS in the form of a variant of the RTOS with a feature set suitable for the application (e.g., which form of scheduling is supported)
- a *system configuration* that tailors the variant to the specific application instance (e.g., how many task or mutexes the application requires)
- the application code itself

Systems are built via the `prj` tool which implements the RTOS build system.
At the build system level, systems are composed of *modules* (such as the RTOS module), so modules provide the unit of composition and reusability.

In its simplest form, a module is a C file and that is usually all that applications need to know about modules.
However, modules can consist of the following elements:

* Entity definition file named `entity.py` or `<module_name>.py`, specifying the module contents and customization options by providing a `module` Python object.
* C and header file named `<module_name>.c/h`, providing the public interface and its implementation of the module in C
* Assembly file named `<module_name>.s`, providing the module functionality as assembly code
* Linker script named `<module_name>.ld`, specifying linker commands for linking the system (not just the module)
* XML Configuration schema (as a standalone file or integrated into the entity definition script or source code files), specifying the configuration parameters supported by the module
* Builder module script `<module_name>.py`, defining a `system_build()` function to be executed in order for the system to be built.
The presence of this function distinguishes the module as a Builder module.

A system is statically defined and configured in its *system configuration* in a `.prx` file.
This is an XML file that lists the modules that make up a system and provides configuration parameters for each of the modules.
The `.prx` file includes a static declaration of all the RTOS resources used by the system, including all tasks, mutexes, semaphores, interrupt handlers, etc.
The `prj` tool reads `.prx` files and composes, compiles, and links all the code to produce a system binary.

A complete system typically consists of

* an RTOS module
* modules dictating the build process for the target platform
* one or more modules containing platform-specific assembly code needed by the RTOS variant to implement low-level OS functionality
* one or more modules containing user-provided code that implement the application functionality

For example, the Kochab RTOS example system for QEMU-emulated PowerPC e500 (defined in [`packages/machine-qemu-ppce500/example/kochab-system.prx`](packages/machine-qemu-ppce500/example/kochab-system.prx)) contains the following modules:

* `ppce500.rtos-kochab`, the Kochab variant of the RTOS for ppce500.
* `ppce500.build` and `ppce500.default-linker`, which define building and linking for ppce500.
* `ppce500.interrupts-util` and `ppce500.vectable`, which provide assembly-level RTOS code for ppce500.
* `ppce500.debug` and `generic.debug`, which define stubs for debug printing.
* `machine-qemu-ppce500.example.kochab-test`, the user-provided test program for Kochab QEMU-emulated PowerPC e500.

On the file system, modules are grouped into *packages*, allowing modules to be organised based on common characteristics (such as platform or intended usage).
For example, the PowerPC e500 RTOS variant modules are grouped together with the `build`, `default-linker`, `interrupts-util` and `vectable` modules in the [`ppce500`](packages/ppce500) package.
As another example, platform-agnostic RTOS example code such as the `kochab-mutex-test` and `timer-test` modules are grouped together in the [`rtos-example`](packages/rtos-example) package.

## Tool support

As described above, `x.py` provides the means to generate all the different RTOS variants and `prj` provides the means to combine an RTOS module with other modules to produce a system binary.

There are two main steps in building an RTOS-based system from the RTOS repository.

Step 1: Generate the RTOS variants.

     ./x.py build packages

This generates all the RTOS variants.
For each variant specified in `x.py` it finds the appropriate components and combines them into the RTOS variant.
The resulting variant can be found in `packages/<platform>/rtos-<variant>`.

Please look at the documentation inside `x.py` for more information on the tool.


Step 2: Build a system.

     prj/app/prj.py build <system name>

This finds the appropriate `.prx` file, combines the required modules and generates a system binary.
`prj` can be further configured using the top level [`project.prj`](project.prj) file, which specifies tasks that are automatically performed when `prj` is run as well as the locations to look for modules and `.prx` configuration files.

As a convenience `prj` can be configured to automatically regenerate RTOS modules whenever it is run.
This is done by including the following line in the `project.prj` file:

     <startup-script>./x.py build partials --allow-unknown-filetypes</startup-script>

Please see `prj/manuals/prj-user-manual` for more information on the `prj` tool.



# Repository contents

The [`components`](components) directory contains the RTOS component source and documentation.

The [`packages`](packages) directory provides various platform-specific RTOS modules and other source modules.
RTOS modules generated by `x.py` are output to locations within the `packages` directory structure.

The [`x.py`](x.py) tool provides an interface for:
* RTOS module and product release generation (`x.py build`),
* task management (`x.py task`), and
* testing (`x.py test`).

Much of `x.py`'s underlying implementation resides in the [`pylib`](pylib) directory, and its self-test suite (`x.py test x`) is implemented in [`x_test.py`](x_test.py).

The [`prj`](prj) directory contains everything related to the `prj` tool, which provides an interface for configuring and building systems as compositions of source modules.

The `release` directory is created by `x.py`, and contains all releasable artifacts such as product releases.

The [`external_tools`](external_tools) and [`tools`](tools) directories contain external tools committed to the repository in order for the repository to be self-contained in its ability to provide everything that is needed for development.
See [`external_tools/LICENSE.txt`](external_tools/LICENSE.txt) and [`tools/LICENSE.txt`](tools/LICENSE.txt) for license information regarding the contents of these directories.

The [`pm`](pm) directory contains project management related meta-data.
This meta-data is crucial for documenting that our internal development processes are correctly followed, as well as providing a record for external audit.
The feature documentation for development tasks resides in [`pm/tasks`](pm/tasks), and their reviews can be found under [`pm/reviews`](pm/reviews).

The [`docs`](docs) directory contains various release documentation-related content, including templates for auto-generated manuals and top-level README files for product releases.

The [`rtos`](rtos) directory contains a model of the RTOS schedulers implemented in Python for testing purposes.


# Common Development Tasks

## Developing a RTOS variant

To generate the code for all available RTOS variants:

    ./x.py build packages

The resulting RTOS code is placed into `packages/<platform>/rtos-<variant>/`.

The RTOS variants themselves are specified as lists of components within `x.py` itself.
Adding a new RTOS variant means adding the appropriate list entries to `x.py`, and adding new component code in the `components` directory structure if necessary.

Please see the existing component code under [`components`](components) for examples on how to develop RTOS components.


## Building user documentation

Manuals for all RTOS variants are built using:

    ./x.py build docs

The manuals are output to `packages/<platform>/rtos-<variant>/documentation.*` in HTML, Markdown and PDF.

Like the RTOS component code, the user manual content is componentized so that only the user documentation for components present in a particular RTOS variant appears in the user manual for that variant.

See `components/*/docs.md` for examples of componentized user documentation.


## Building the `prj` tool

To build just the `prj` tool binary for stand-alone use:

    # The `prj` binary is output to `prj_build_x86_64-unknown-linux-gnu/prj`
    ./x.py build prj


## Configuring and building a system

To build a system, use the `prj build` sub-tool of `prj`, supplying the system name as argument.
The name of the system is the basename of its `.prx` file, minus its `.prx` extension, appended to a dot-separated string indicating the sub-package location in which it can be found.

For example, to build the system whose `.prx` file is located at [`packages/machine-qemu-ppce500/example/kochab-timer-demo.prx`](packages/machine-qemu-ppce500/example/kochab-timer-demo.prx), from the top level of the repository:

    prj/app/prj.py build machine-qemu-ppce500.example.kochab-timer-demo

Alternatively, assuming the `prj` tool binary is on the PATH:

    prj build machine-qemu-ppce500.example.kochab-timer-demo


## Building a product release

To build all product releases, run:

    ./x.py build prj
    ./x.py build docs
    ./x.py build partials
    ./x.py build release

The releases are defined in the top-level [`release_cfg.py`](release_cfg.py) script and are generated by `x.py` into `release/*.tar.gz`.

Each product release contains:

* a `LICENSE` file
* a `README.md` file containing a brief introduction to the release
* a `build_info` file containing the commit hash of the RTOS repository from which the release was built
* a `share` directory, containing the package directory structure containing all the released packages
* a pre-built `prj` binary for the host platform targeted by the release

Note that any user manuals available for each RTOS variant in the release can be found at `share/packages/<platform>/rtos-<variant>/documentation.*` in HTML, Markdown and PDF.
