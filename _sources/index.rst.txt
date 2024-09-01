CMake introduction and hands-on workshop
========================================

CMake is a language-agnostic, cross-platform build tool and is nowadays the *de
facto* standard,  with many large projects using it to reliably build, test, and
deploy their codebases.


Learning outcomes
-----------------

You will learn how to:

- Write a CMake build system for C, C++, and Fortran projects producing
  libraries and/or executables.
- Run tests for your code with CTest.
- Ensure your build system will work on different platforms.
- Detect and use external dependencies in your project.
- Recognize modular patterns but also recognize and know how to avoid
  problematic and later unmaintainable "spaghetti" patterns.


Topics not covered
------------------

- Probing compilation, linking, and execution
- Composing CMake projects from non-CMake projects
- Superbuilds
- Cross-compilation
- GPUs
- Mixing C/C++/Fortran with Python
- pkg-config for finding dependencies


.. prereq::

   **Software requirements**

   Before attending this workshop, please make sure that you have access
   to a computer with a compiler for your favorite language and a recent version of CMake.
   Instructions on how to set up these dependencies can be found
   on the :doc:`setup` page.

   **Who is the course for?**

   This course is for students, researchers, engineers, and programmers that have
   heard of `CMake`_ and want to learn how to use it effectively with projects they
   are working on.
   This course assumes no previous experience with `CMake`_. You will have to be
   familiar with the tools commonly used to build software in your compiled
   language of choice: C++, C, Fortran.
   Specifically, this lesson assumes that participants have some prior experience
   with or knowledge of the following topics (but no expertise is required):

   - Compiling and linking executables and libraries.
   - Differences between shared and static libraries.
   - Automated testing.


.. toctree::
   :hidden:
   :maxdepth: 1

   setup


.. note::

   You can find all examples, exercises, and solutions at https://github.com/coderefinery/cmake-workshop.
   In there navigate to `content/examples/ <https://github.com/coderefinery/cmake-workshop/tree/main/content/examples>`__.


Schedule
--------

Timing still being adjusted. It's still too much material:

.. csv-table::
   :widths: auto
   :align: center
   :delim: ;

    15 min ; :ref:`motivation`
    15 min ; :ref:`tour`

           ; Lunch break

    15 min ; :ref:`hello-world-executable` and setup help
    15 min ; :ref:`hello-world-library`
    10 min ; :ref:`flags-definitions-debugging`
    20 min ; :ref:`targets`

    10 min ; Break

    15 min ; :ref:`multiple-folders`
    20 min ; :ref:`environment`
    20 min ; :ref:`dependencies`

    10 min ; Break

    10 min ; :ref:`testing`
    15 min ; :ref:`testing_exercises`
    20 min ; :ref:`fetch-content`

    15 min ; :ref:`growing-projects`


.. toctree::
   :hidden:
   :maxdepth: 1
   :caption: Introduction

   motivation
   tour


.. toctree::
   :hidden:
   :maxdepth: 1
   :caption: Hands-on workshop

   hello-cmake
   flags-definitions-debugging
   targets
   environment
   dependencies
   testing
   fetch-content
   growing-projects


.. toctree::
   :maxdepth: 1
   :caption: Reference

   resources
   credits
