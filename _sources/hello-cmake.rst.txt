.. _sources-to-executables:


From sources to executables
===========================

.. objectives::

   - Learn how to write a simple ``CMakeLists.txt``.
   - Learn how to build executables and libraries.


.. _hello-world-executable:

Type-along: building an executable
----------------------------------

.. exercise:: Compiling "Hello, world" with CMake

   We will now proceed to compile a single source file to an executable. Choose
   your favorite language and start typing along!

   1. Create a new folder and in the folder create a source file:

     .. tabs::

        .. tab:: C++

           .. literalinclude:: examples/hello-executable/cxx/hello.cpp
              :language: c++

        .. tab:: Fortran

           .. literalinclude:: examples/hello-executable/fortran/hello.f90
              :language: fortran

   2. The folder contains only the source code. We need to add a file called
      ``CMakeLists.txt`` to it.  CMake reads the contents of these special files
      when generating the build system:

     .. tabs::

        .. tab:: C++

           .. literalinclude:: examples/hello-executable/cxx/CMakeLists.txt
              :language: cmake

        .. tab:: Fortran

           .. literalinclude:: examples/hello-executable/fortran/CMakeLists.txt
              :language: cmake

   3. We are ready to call CMake and get our build system:

      .. code-block:: console

         $ cmake -S. -Bbuild

   4. And finally build our executable:

      .. code-block:: console

         $ cmake --build build

   5. Try to also run the executable.


.. discussion:: Discussion: We prefer out-of-source builds

   The ``-S`` switch specifies which source directory CMake should scan: this is
   the folder containing the *root* ``CMakeLists.txt``, *i.e.* the one containing
   the |project| command.
   By default, CMake will allow *in-source* builds, *i.e.* storing build
   artifacts alongside source files. This is **not** good practice: you should
   always keep build artifacts from sources separate. Fortunately, the ``-B``
   switch helps with that, as it is used to give where to store build artifacts,
   including the generated build system. This is the minimal invocation of ``cmake``:

   .. code-block:: console

      $ cmake -S. -Bbuild

   To switch to another generator, we will use the ``-G`` switch:

   .. code-block:: console

      $ cmake -S. -Bbuild -GNinja

   Options to be used at build-system generation are passed with the ``-D``
   switch. For example, to change compilers:

   .. code-block:: console

      $ cmake -S. -Bbuild -GNinja -DCMAKE_CXX_COMPILER=clang++

   Why prefer out-of-source builds?

   - You can build several builds with the same source without having to copy the entire project
     and merging changes later (sequential and parallel, debug and release).

We have learned met three CMake directives (you can click on these to jump to
the official documentation help text):

- |cmake_minimum_required|
- |project|
- |add_executable|

The case of CMake commands and variables does not matter: the DSL is
case-insensitive. However, the plain-text files that CMake parses **must be
called** ``CMakeLists.txt`` and the case matters!


.. _hello-world-library:

Exercise: building and linking a library
----------------------------------------

.. exercise:: A more modular "Hello, world"

   Only rarely we have one-source-file projects and more realistically, as
   projects grow, we split them up into separate files. This simplifies
   (re)compilation but also helps humans maintaining and understanding the
   project.

   We stay with the toy project but also here things got more real and more
   modular and we decided to split the project up into several files:

   .. tabs::

      .. tab:: C++

         hello.cpp:

         .. literalinclude:: examples/hello-library/cxx/hello.cpp
            :language: c++

         greeting.cpp:

         .. literalinclude:: examples/hello-library/cxx/greeting.cpp
            :language: c++

         greeting.hpp:

         .. literalinclude:: examples/hello-library/cxx/greeting.hpp
            :language: c++

      .. tab:: Fortran

         hello.f90:

         .. literalinclude:: examples/hello-library/fortran/hello.f90
            :language: fortran

         greeting.f90:

         .. literalinclude:: examples/hello-library/fortran/greeting.f90
            :language: fortran

   **Your first goal**: try to build this by adapting the `CMakeLists.txt` from
   earlier by first adding all the source files into the same |add_executable|.

CMake can of course be used to produce libraries as well as executables.  The
relevant command is |add_library|.  You can link libraries can be linked into
other targets (executables or other libraries) with |target_link_libraries|.

.. callout:: Executables and libraries are targets

   We will encounter the term **target** repeatedly. In CMake, a target is any
   object given as first argument to |add_executable| or |add_library|.  Targets
   are the basic atom in CMake.  Whenever you will need to organize complex
   projects, think in terms of its targets and their mutual dependencies.  The
   whole family of CMake commands ``target_*`` can be used to express chains of
   dependencies and is much more effective than keeping track of state with
   variables.  We will clarify these concepts in :ref:`targets`.


.. exercise:: Collecting files into libraries

   **Your second goal**: now try to build a greeting library and link against this library
   instead of collecting all sources into the executable target:

   .. tabs::

      .. tab:: C++

         .. literalinclude:: examples/hello-library/cxx/CMakeLists.txt
            :language: cmake

      .. tab:: Fortran

         .. literalinclude:: examples/hello-library/fortran/CMakeLists.txt
            :language: cmake

   Which solution did you like better? Discuss the pros and cons.

   What kind of library did you get? Static or shared? Try to get the other
   one.


.. discussion:: Discussion: Granulatity of libraries

   How granular should we organize our targets?

   - Collect all sources into one executable?
   - One library?
   - Many libraries?

   Discuss pros and cons and how you do this in your projects.


.. keypoints::

   - CMake is a **build system generator**, not a build system.
   - You write ``CMakeLists.txt`` to describe how the build tools will create artifacts from sources.
   - We can define a multi-language project like this: ``project(example LANGUAGES Fortran C CXX)``
   - You can use the CMake suite of tools to manage the whole lifetime: from source files to tests to deployment.
   - The structure of the project is mirrored in the build folder.
