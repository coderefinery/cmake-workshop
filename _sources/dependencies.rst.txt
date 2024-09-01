.. _dependencies:


Finding and using dependencies
==============================

.. objectives::

   - Learn how to use |find_package|.
   - Learn what other detection alternatives exist.

The vast majority of software projects do not happen in a vacuum: they will have
dependencies on existing frameworks and libraries.  Good documentation will
instruct your users to ensure that these are satisfied in their programming
environment. The build system is the appropriate place to check that these
preconditions are met and that your project can be built correctly.
In this episode, we will show you few examples of how to detect and use
dependencies in your CMake build system.


Finding dependencies
--------------------

CMake offers a family of commands to find artifacts installed on your system:

- |find_file| to retrieve the full path to a file.
- |find_library| to find a library, shared or static.
- |find_package| to find and load settings from an external project.
- |find_path| to find the directory containing a file.
- |find_program| to find an executable.

The workhorse of dependency discovery is |find_package|, which will cover your
needs in almost all use cases.

.. signature:: |find_package|

   .. code-block:: cmake

      find_package(<PackageName> [version] [EXACT] [QUIET] [MODULE]
             [REQUIRED] [[COMPONENTS] [components...]]
             [OPTIONAL_COMPONENTS components...]
             [NO_POLICY_SCOPE])

   This command will attempt finding the package with name ``<PackageName>`` by
   searching in a number of `predefined folders
   <https://cmake.org/cmake/help/latest/command/find_package.html?highlight=find_package#search-procedure>`_.
   It is possible to ask for a minimum or exact version. If ``REQUIRED`` is
   given, a failed search will trigger a fatal error.  The rules for the search
   are obtained from modules named ``Find<PackageName>.cmake``.
   Packages can also have *components* and you can ask to detect just a handful of them.

- For a large selection of common dependencies, the ``Find<PackageName>.cmake``
  modules shipped with CMake work flawlessly and are maintained by the CMake
  developers. This lifts the burden of programming your own dependency
  detection tricks.
- |find_package| will set up **imported targets**: targets defined *outside*
  your project that you can use with your own targets.  The properties on
  imported targets defines *usage requirements* for the dependencies. A command
  such as:

  .. code-block:: cmake

     target_link_libraries(your-target
       PUBLIC
         imported-target
       )

  will set compiler flags, definitions, include directories, and link libraries
  from ``imported-target`` to ``your-target`` *and* to all other targets in
  your project that will use ``your-target``.

These two points above simplify **enormously** the burden of dependency detection and
consistent usage within a multi-folder project.


Using find_package
------------------

When attempting dependency detection with |find_package|, you should make sure that:

- A ``Find<PackageName>.cmake`` module exists,
- Which components, if any, it provides, and
- What imported targets it will set up.

You can browse the documentation of a module like this:

.. code-block:: console

   $ cmake --help-module FindOpenMP | less

When browsing documentation of ``Find<PackageName>.cmake`` module, **always
first look for IMPORTED targets**. These are the targets you will want to link
against. Then all the rest will just work and you won't need to deal with
compile flags, definitions, and include paths.

A complete list of ``Find<PackageName>.cmake`` can be found from the command-line interface:

.. code-block:: console

   $ cmake --help-module-list | grep "Find"

Another way is to browse available modules: https://github.com/Kitware/CMake/tree/master/Modules


.. exercise:: Exercise: working with find_package

   Choose one of the examples below which is closest to your work and most
   relevant for your code.

   1. Try to compile and run.
   2. Browse the documentation of the ``Find<PackageName>.cmake`` module.
   3. Try to compile with verbosity and verify how the imported target modified
      compile flags and definitions.
   4. **Bonus**: Try to adapt what we learned to an example which uses the BLAS or
      LAPACK library.

     .. tabs::

        .. tab:: OpenMP (C++)

           Source file (``example.cpp``):

           .. literalinclude:: examples/find-package/omp/cxx/example.cpp
              :language: c++

           And the ``CMakeLists.txt`` file:

           .. literalinclude:: examples/find-package/omp/cxx/CMakeLists.txt
              :language: cmake

        .. tab:: OpenMP (Fortran)

           Source file (``example.f90``):

           .. literalinclude:: examples/find-package/omp/fortran/example.f90
              :language: fortran

           And the ``CMakeLists.txt`` file:

           .. literalinclude:: examples/find-package/omp/fortran/CMakeLists.txt
              :language: cmake

        .. tab:: MPI (C++)

           Source file (``example.cpp``):

           .. literalinclude:: examples/find-package/mpi/cxx/example.cpp
              :language: c++

           And the ``CMakeLists.txt`` file:

           .. literalinclude:: examples/find-package/mpi/cxx/CMakeLists.txt
              :language: cmake

        .. tab:: MPI (Fortran)

           Source file (``example.f90``):

           .. literalinclude:: examples/find-package/mpi/fortran/example.f90
              :language: fortran

           And the ``CMakeLists.txt`` file:

           .. literalinclude:: examples/find-package/mpi/fortran/CMakeLists.txt
              :language: cmake


.. keypoints::

   - CMake has a rich ecosystem of modules for finding software dependencies. They are called ``Find<package>.cmake``.
   - The ``Find<package>.cmake`` modules are used through ``find_package(<package>)``.
