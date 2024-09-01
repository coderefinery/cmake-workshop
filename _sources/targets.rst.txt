.. _targets:


Target-based build systems with CMake
=====================================

.. objectives::

   - Learn that the basic elements in CMake are *not* variables, *but* targets.
   - Learn about properties of targets and how to use them.
   - Learn how to use *visibility levels* to express dependencies between targets.
   - Learn how to work with projects spanning multiple folders.
   - Learn how to handle multiple targets in one project.

Real-world projects require more than compiling a few source files into
executables and/or libraries.  In the vast majority of cases, you will be faced
with projects comprising dozens to hundreds of source files sprawling in a complex source
tree.  Using modern CMake helps you keep the complexity of the build system in
check.

With the advent of CMake 3.0, there has been a
significant shift in the way the CMake domain-specific language (DSL) is
structured.  Rather than relying on global scope **variables** to convey
information in a project, we should shift to using **targets** and
**properties**.

We will first demonstrate the better, nicer way using targets and properties,
but we will also contrast this approach with less nice solutions that many
codes use (global variables and less abstraction), so that we learn recognizing
good patterns and stay away from less maintainable patterns for our own
projects.


Targets
-------

A target is declared by either |add_executable| or |add_library|: thus, in broad
terms, a target maps to a build artifact in the project.

You can add custom targets to the build system with |add_custom_target|.
Custom targets are not necessarily build artifacts.

Any target has a collection of **properties**, which define *how* the build
artifact should be produced **and** *how* it should be used by other dependent
targets in the project.

.. figure:: img/target.svg
   :align: center

   A target is the basic element in the CMake DSL. Each target has *properties*,
   which can be read with |get_target_property| and modified with
   |set_target_properties|.  Compile options, definitions, include directories,
   source files, link libraries, and link options are properties of targets.

The five most used commands used to handle targets are:

- |target_sources|
- |target_compile_options|
- |target_compile_definitions|
- |target_include_directories|
- |target_link_libraries|

There are additional commands in the ``target_*`` family:

.. code-block:: console

  $ cmake --help-command-list | grep "^target_"

  target_compile_definitions
  target_compile_features
  target_compile_options
  target_include_directories
  target_link_directories
  target_link_libraries
  target_link_options
  target_precompile_headers
  target_sources


Properties
----------

CMake lets you set properties at many different levels of visibility across the
project:

- **Global scope**. These are equivalent to variables set in the root
  ``CMakeLists.txt``. Their use is, however, more powerful as they can be set
  from *any* leaf ``CMakeLists.txt``.
- **Directory scope**. These are equivalent to variables set in a given leaf ``CMakeLists.txt``.
- **Target**. These are the properties set on targets that we discussed above.
- **Test**.
- **Source files**. For example, compiler flags.
- **Cache entries**.
- **Installed files**.

For a complete list of properties known to CMake:

.. code-block:: console

   $ cmake --help-properties | less

You can get the current value of any property with |get_property|
and set the value of any property with |set_property|.


Visibility levels
-----------------

It is much more robust to use targets and properties than using variables and
here we will discuss why.

.. figure:: img/target_inheritance.svg
   :align: center

   Properties on targets have **visibility levels**, which determine how CMake
   should propagate them between interdependent targets.

Visibility levels ``PRIVATE``, ``PUBLIC``, or ``INTERFACE`` are very powerful
but not easy to describe and imagine in words. Maybe a better approach to
demonstrate what visibility levels is to see it in action.

We will demonstrate this with a hello world example where somebody went a bit
too far with modularity and where we have split the code into 3 libraries and
the main function (``content/examples/property-visibility/``)::

  .
  ├── CMakeLists.txt
  ├── greeting
  │   ├── greeting.cpp
  │   └── greeting.hpp
  ├── hello_world
  │   ├── hello_world.cpp
  │   └── hello_world.hpp
  ├── main.cpp
  └── world
      ├── world.cpp
      └── world.hpp

Here the main function links to greeting which links to hello_world which links to world.


.. callout:: The internal dependency tree

  If you have Graphviz installed, you can visualize the dependencies between
  the targets:

  .. code-block:: console

     $ cd build
     $ cmake --graphviz=project.dot ..
     $ dot -T svg project.dot -o project.svg


  .. figure:: img/project.svg
     :align: center

     The dependencies between the four targets in the example project.


This is the ``CMakeLists.txt`` - take some time to study it since there is a quite a lot going on:

.. literalinclude:: examples/property-visibility/CMakeLists.txt
   :language: cmake
   :linenos:
   :emphasize-lines: 17

.. exercise:: Testing the 3 different visibility levels

   1. Browse, configure, build, and run the code.

   2. Now uncomment the highlighted line (line 17) with |target_compile_definitions|, configure into a fresh folder, and build:

      .. code-block:: console

         $ cmake -S. -Bbuild_private
         $ cmake --build build_private

      You will see that the definition is used in ``world.cpp`` but nowhere else.

   3. Now change the definition to ``PUBLIC``, configure into a fresh folder, and build.

      .. code-block:: console

         $ cmake -S. -Bbuild_public
         $ cmake --build build_public

      You will see that the definition is used both in ``world.cpp`` and ``hello_world.cpp``.

   4. Now change the definition to ``INTERFACE``, configure into a fresh folder, and build.

      .. code-block:: console

         $ cmake -S. -Bbuild_interface
         $ cmake --build build_interface

      You will see that the definition is used only in ``hello_world.cpp`` but not in ``world.cpp``.


.. discussion:: Visibility levels

   Discuss what this means and how we can use it to fine-tune visibility of
   definitions, include directories, and libraries.

   What do you think will happen if we change the visibility in line 14 of the
   above code to ``PRIVATE``?


.. _multiple-folders:

Building a larger project with multiple folders
-----------------------------------------------

In the example above we have split a project into folders and libraries but we
kept one ``CMakeLists.txt``. As the project grows, this becomes impractical for
humans (the CMake computer overlords will not mind) and maintenance becomes
easier if we split the CMake configuration into multiple ``CMakeLists.txt``
with the help of |add_subdirectory|. Our goal is to have a ``CMakeLists.txt`` as
close as possible to the source files.

We will soon practice with an example project where instead of this:

.. code-block:: text

   project/
   ├── CMakeLists.txt           <--- Only at root
   ├── external
   └── src
       ├── evolution
       ├── initial
       ├── io
       └── parser

we rather wish this:

.. code-block:: text

   project/
   ├── CMakeLists.txt           <--- Root
   ├── external
   │   ├── CMakeLists.txt       <--- Leaf
   └── src
       ├── CMakeLists.txt       <--- Another leaf
       ├── evolution
       │   ├── CMakeLists.txt   <--- Leaf of leaf
       ├── initial
       │   ├── CMakeLists.txt   <--- Leaf of leaf
       ├── io
       │   ├── CMakeLists.txt   <--- Leaf of leaf
       └── parser
           └── CMakeLists.txt   <--- Leaf of leaf

Each folder in a multi-folder project will contain a ``CMakeLists.txt``: a
source tree with one **root** and many **leaves**.

The root ``CMakeLists.txt`` will contain the invocation of the |project|
command: variables and targets declared in the root have effectively global
scope. Remember also that |PROJECT_SOURCE_DIR| will point to the folder
containing the root ``CMakeLists.txt``.
In order to move between the root and a leaf or between leaves, you will use the
|add_subdirectory| command.

We can declare targets at any level, not necessarily the root: a target is
visible at the level at which it is declared and all higher levels.

.. challenge:: Exercise: practicing structuring projects with cellular automata

   Let's move beyond "Hello, world" and work with a project spanning multiple
   folders. We will implement a relatively simple code to compute and print to
   screen elementary `cellular automata
   <https://en.wikipedia.org/wiki/Cellular_automaton#Elementary_cellular_automata>`_.
   We separate the sources into ``src`` and ``external`` to simulate a nested project
   which reuses an external project.
   Your goal is to:

   1. Build the main executable at ``content/examples/multiple-folders/problem/``.

   2. Where is it located in the build tree? Remember
      that CMake generates a build tree mirroring the source tree.

   3. The executable will accept 3 arguments: the length, number of steps, and
      automaton rule.  You can run it with:

     .. code-block:: console

        $ automata 40 5 30

     This is the output:

     .. code-block:: text

        length: 40
        number of steps: 5
        rule: 30
                            *
                           ***
                          **  *
                         ** ****
                        **  *   *
                       ** **** ***

   4. Push the definition of targets "down" into folders and subfolders, as close
      as possible to the source files with the help of |add_subdirectory|.

   .. tabs::

      .. tab:: C++

         You want to arrive at this (``content/examples/multiple-folders/solution/cxx/``) structure:

         .. code-block:: text

           .
           ├── CMakeLists.txt
           ├── external
           │   ├── CMakeLists.txt
           │   ├── conversion.cpp
           │   └── conversion.hpp
           └── src
               ├── CMakeLists.txt
               ├── evolution
               │   ├── CMakeLists.txt
               │   ├── evolution.cpp
               │   └── evolution.hpp
               ├── initial
               │   ├── CMakeLists.txt
               │   ├── initial.cpp
               │   └── initial.hpp
               ├── io
               │   ├── CMakeLists.txt
               │   ├── io.cpp
               │   └── io.hpp
               ├── main.cpp
               └── parser
                   ├── CMakeLists.txt
                   ├── parser.cpp
                   └── parser.hpp

         In |target_sources|, does using absolute
         (``${CMAKE_CURRENT_LIST_DIR}/parser.cpp``) or relative
         (``parser.cpp``) paths make any difference?

      .. tab:: Fortran

         You want to arrive at this (``content/examples/multiple-folders/solution/fortran/``) structure:

         .. code-block:: text

           .
           ├── CMakeLists.txt
           ├── external
           │   ├── CMakeLists.txt
           │   └── conversion.f90
           └── src
               ├── CMakeLists.txt
               ├── evolution
               │   ├── ancestors.f90
               │   ├── CMakeLists.txt
               │   ├── empty.f90
               │   └── evolution.f90
               ├── initial
               │   ├── CMakeLists.txt
               │   └── initial.f90
               ├── io
               │   ├── CMakeLists.txt
               │   └── io.f90
               ├── main.f90
               └── parser
                   ├── CMakeLists.txt
                   └── parser.f90

         Note that CMake can understand the compilation order imposed by the
         Fortran modules without further intervention. Where are the ``.mod``
         files?

      .. tab:: Bonus

         You can decide where executables, static and shared libraries, and
         Fortran ``.mod`` files will be stored within the build tree.
         The relevant variables are:

         - ``CMAKE_RUNTIME_OUTPUT_DIRECTORY``, for executables.
         - ``CMAKE_ARCHIVE_OUTPUT_DIRECTORY``, for static libraries.
         - ``CMAKE_LIBRARY_OUTPUT_DIRECTORY``, for shared libraries.
         - ``CMAKE_Fortran_MODULE_DIRECTORY``, for Fortran ``.mod`` files.

         Modify your ``CMakeLists.txt`` to output the ``automata`` executable in
         ``build/bin`` and the libraries in ``build/lib``.

   5. Discuss pros and cons of one ``CMakeLists.txt`` compared to many.


.. discussion:: Target properties vs. global settings

   Let us return to the very granular hello world example from futher above.
   In CMake there are many ways to achieve something and here we compare two.

   Please discuss pros and cons and what possible problems you see or
   anticipate.

   .. tabs::

      .. tab:: OK example

         This is the example we have used further above. It is not perfect
         but much better than the example in the other tab.

         .. literalinclude:: examples/property-visibility/CMakeLists.txt
            :language: cmake

      .. tab:: Bad example

         **This is a bad example** although it might look more compact and possibly
         neater at first sight.  It probably also works, at least on Linux,
         probably not on macOS and Windows.  Can you point out the problems
         that you see or anticipate with this structure?

         .. literalinclude:: examples/bad-example-using-variables/CMakeLists.txt
            :language: cmake
            :emphasize-lines: 3

   .. solution::

      Let us first note that the left solution is not perfect either. In a real
      larger project we would probably want to define targets closer to the
      sources.

      Problems with the "bad" solution (perhaps you found other problems?):

      - Compile definition ``-DMY_DEFINITION`` affects entire project.
      - Also the include directories and library link line settings affect the entire project.
      - The order of ``link_libraries`` matters.
      - Library names are hard-coded and it will not work on macOS or Windows where libraries
        have different names.
      - Library names have been hard-coded to static libraries and it will be more difficult now
        for us to switch between static or dynamic libraries.
      - If you rename a library you suddenly need to edit more places than in
        the example using targets.
      - ``CMakeLists.txt`` became more brittle to order of lines.
      - It became more difficult to move sections to other ``CMakeLists.txt`` files.
      - Risk is higher to forget some definitions or library location or
        include directories behind after moving sections away.
      - Settings in the target-based approach have more local scope. In the "bad" example
        settings have global scope and risk having unintended side effects on other CMake code
        in a larger project.


.. keypoints::

   - Using targets, you can achieve granular control over how artifacts are
     built and how their dependencies are handled.
   - Compiler flags, definitions, source files, include folders, link libraries,
     and linker options are **properties** of a target.
   - Avoid using variables to express dependencies between targets: use the
     visibility levels ``PRIVATE``, ``INTERFACE``, ``PUBLIC`` and let CMake
     figure out the details.
   - Use |get_property| to inquire and |set_property| to modify values of
     properties.
   - To keep the complexity of the build system at a minimum, each folder in a
     multi-folder project should have its own ``CMakeLists.txt``.
