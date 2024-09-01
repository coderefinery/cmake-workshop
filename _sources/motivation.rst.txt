.. _motivation:


Motivation and overview
=======================

.. objectives::

   - Motivate why CMake.
   - Learn what tools available in the CMake suite.
   - Learn the difference between *build systems*, *build tools*, and *build system generator*.
   - Learn to distinguish between *configuration*, *generation*, and *build* time.


Software is everywhere and so are build systems. Whenever you run a piece of
software,  anything from calendar apps to computationally-intensive programs,
there was a build system involved in transforming the plain-text source code
into binary files that could run on the device you are using. In research code
projects, CMake is often involved in this.


Why is Make not enough?
-----------------------

- Make knows about targets and dependencies
- Make does not know which compiler (options) we want and which environment we are on
- We need to tell Make what depends on what (Fortran 90 projects)


What is CMake?
--------------

- Cross-platform (this is the C in CMake, not the C language)
- Open-source
- Actively developed: https://github.com/Kitware/CMake
- Manages the build process in a compiler-independent manner
- Written in C++ (does not matter but if you want to build CMake yourself, C++ is all you need)

CMake is a **build-system generator**: it provides a family of tools and a
*domain-specific language* (DSL) to **describe** what the build system should
achieve when the appropriate build tools are invoked.
The DSL is platform- *and* compiler-agnostic: you can reuse the same CMake
scripts to obtain *native* build systems on any platform.

CMake is not a build system. It generates files for build systems to use
to build the software.

.. figure:: img/build-systems.svg
   :align: center

   On GNU/Linux, the native build system will be a collection of Makefiles.
   The ``make`` build tool uses these Makefiles to transform sources to
   executables and libraries.
   CMake abstracts the process of generating the Makefiles away into a
   generic DSL.


Having a look at available generators
-------------------------------------

Let us get acquainted with the CMake and especially its command-line interface.

We can get help at any time with:

.. code-block:: console

   $ cmake --help

This will output quite a number of options to your screen.
We can analyze the last few lines first (your output may differ):

.. code-block:: text

  Generators

  The following generators are available on this platform (* marks default):
    Green Hills MULTI            = Generates Green Hills MULTI files
                                   (experimental, work-in-progress).
  * Unix Makefiles               = Generates standard UNIX makefiles.
    Ninja                        = Generates build.ninja files.
    Ninja Multi-Config           = Generates build-<Config>.ninja files.
    Watcom WMake                 = Generates Watcom WMake makefiles.
    CodeBlocks - Ninja           = Generates CodeBlocks project files.
    CodeBlocks - Unix Makefiles  = Generates CodeBlocks project files.
    CodeLite - Ninja             = Generates CodeLite project files.
    CodeLite - Unix Makefiles    = Generates CodeLite project files.
    Eclipse CDT4 - Ninja         = Generates Eclipse CDT 4.0 project files.
    Eclipse CDT4 - Unix Makefiles= Generates Eclipse CDT 4.0 project files.
    Kate - Ninja                 = Generates Kate project files.
    Kate - Unix Makefiles        = Generates Kate project files.
    Sublime Text 2 - Ninja       = Generates Sublime Text 2 project files.
    Sublime Text 2 - Unix Makefiles
                                 = Generates Sublime Text 2 project files.

In CMake terminology, the native build scripts and build tools are called
**generators**. On any particular platform, the list will show which native
build tools can be used through CMake. They can either be "plain", such as
Makefiles or Ninja, or IDE-like projects.

You can also inquire about a specific module, command or variable:

.. code-block:: console

   $ cmake --help-variable CMAKE_GENERATOR

Finally, you can access the full CMake manual with:

.. code-block:: console

   $ cmake --help-full


A complete toolchain
--------------------

The family of tools provided with CMake offers a complete toolchain to manage
the development cycle: from sources to build artifacts, testing, and deployment.
We refer to these stages as *CMake times* and each tool is appropriate at a specific time. In this workshop, we will discuss:

- **CMake time** or **configure time**. This is the stage when ``cmake`` is
  invoked to parse the ``CMakeLists.txt`` in your project, configure and generate the build
  system.
- **Build time**. This is handled by the native build tools, but, as we have
  seen, these can be effectively wrapped by ``cmake`` itself.
- **CTest time** or **test time**. At this stage, you will test your build
  artifacts.


.. figure:: img/cmake-times.jpg
   :align: center

   You can manage all the stages of a software project's lifetime with the tools provided by CMake.
   This figure shows all these stages (*times*) and which tool is appropriate for each.
   The figure is reproduced from `CMake Cookbook
   <https://github.com/dev-cafe/cmake-cookbook>`_ and is licensed under the
   terms of the `CC-BY-SA
   <https://creativecommons.org/licenses/by-sa/4.0/legalcode>`_.


Why CMake?
----------

Separation of source and build path:

- **Out-of-source compilation** (possibility to compile several builds with the same source)

Portability:

- Really **cross-platform** (Linux, Mac, Windows, AIX, iOS, Android)
- CMake defines portable variables about the system
- Cross-platform system- and library-discovery

Language support:

- Excellent support for **Fortran, C, C++**, and Java, as well as mixed-language projects
- CMake understands Fortran 90 dependencies very well; no need to program a dependency scanner
- Excellent support for multi-component and multi-library projects

Supports modular code development:

- Makes it possible and relatively easy to download, configure, build, install, and link **external modules**

Provides tools:

- Generates user interface (command-line or text-UI or GUI)
- Full-fledged **testing and packaging framework** with CTest and CPack
- CTest can run sequential tests in parallel

Popular:

- CMake is used by **many prominent projects**:
  MySQL, Boost, VTK, Blender, KDE, LyX, Mendeley, MikTeX, Compiz,
  Google Test, ParaView, Second Life, Avogadro, and many more ...

General:

- Not bound to the generation of Makefiles

Discovery of environment, libraries, packages:

- Huge community
- Many solutions already exist: https://github.com/Kitware/CMake/tree/master/Modules

Can co-exist with other tools:

- Non-intrusive: All you need is a ``CMakeLists.txt``. CMake won't mind if other build tools
  are there as well in the project.


CMake is a domain-specific language
-----------------------------------

CMake offers a domain-specific language (DSL) to describe how to generate a
build system native to the specific platform you might be running on.

It contains everything we expect from a language:

- variables
- means to iterate through collections: foreach
- flow-control (if-statements)
- options
- command-line interface
- modules
- includes
- functions
- macros

To see all available commands:

.. code-block:: console

   $ cmake --help-command-list

Help on a specific built-in command, function or macro can be obtained with:

.. code-block:: console

   $ cmake --help-command target_link_libraries

This means we can implement "everything" in CMake and there will be many ways
to achieve any solution. But the language will also not prevent us from
implementing complicated contraptions. In the tutorial/workshop part we will
revisit some good and less good solutions.


What problems to expect when moving to CMake
--------------------------------------------

- CMake has so many good defaults and most of the time it just works and it can feel
  a bit magic.
- The language and the module ecosystem offers all the possibilities and it is
  easy to go a bit overboard.
- With more CMake code complexity one can then compromise some portability and
  maintainability.
- When things stop working, then it can be difficult for beginners to know where
  to look and where to edit.
- For larger development teams it is yet another tool: the "better" tool we
  don't know compared to the familiar tool that everybody understands.
