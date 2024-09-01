.. _flags-definitions-debugging:


Compile flags, definitions, and debugging
=========================================

.. objectives::

   - How to get more information.
   - How to "print-debug" in CMake.
   - How to select the compiler.
   - How to see compile flags and definitions and how to control them.
   - How to configure for a debug build or a release build.

In the previous episode we have learned how to configure and build:

.. code-block:: console

   $ cmake -S. -Bbuild -DSOME_DEFINITION="something"
   $ cmake --build build

But many people (especially CMake old-timers like the presenter) do this instead with Unix Makefiles:

.. code-block:: console

   $ mkdir build
   $ cd build
   $ cmake .. -DSOME_DEFINITION="something"
   $ make

The former way is "better" because it is less tied to a specific build tool.


How to see all the flags and definitions in a verbose build
-----------------------------------------------------------

The way in which to trigger a verbose build depends on the native build tool you are using.
For Unix Makefiles:

.. code-block:: console

   $ cmake --build build -- VERBOSE=1

But many people do this with Unix Makefiles:

.. code-block:: console

   $ mkdir build
   $ cd build
   $ cmake ..
   $ make VERBOSE=1

Also this works (on Unix Makefiles):

.. code-block:: console

   $ env VERBOSE=1 make

For Ninja:

.. code-block:: console

   $ cmake --build build -- -v

.. exercise:: Hands-on: Verbose compilation

   Try to see the verbose output from a compilation for our hello world example.
   You might need to "clean" to see a full re-build:

   .. code-block:: console

      $ cmake --build build --target clean
      $ cmake --build build -- VERBOSE=1


Printing messages
-----------------

You will most likely have to engage in debugging your CMake scripts at one point
or another. As in other languages, we often will go for the "print-debugging"
using the |message| command:

.. code-block:: cmake

   message(STATUS "I am here!")
   message(FATAL_ERROR "I am here and I want the configuration to stop here.")

|message| can be a bit awkward to work with when printing variables:

.. code-block:: cmake

   message(STATUS "CMAKE_CXX_COMPILER_ID is ${CMAKE_CXX_COMPILER_ID}")
   message(STATUS "CMAKE_CXX_COMPILER_VERSION is ${CMAKE_CXX_COMPILER_VERSION}")

For this the built-in module ``CMakePrintHelpers`` comes in handy:

.. code-block:: cmake

   include(CMakePrintHelpers)

   cmake_print_variables(CMAKE_CXX_COMPILER_ID CMAKE_CXX_COMPILER_VERSION)

This would produce an output similar to this one::

  -- CMAKE_CXX_COMPILER_ID="GNU" ; CMAKE_CXX_COMPILER_VERSION="9.3.0"


How can we select the compiler?
-------------------------------

.. exercise:: Hands-on: Printing compiler ID/vendor and version

   Try to find out what compiler your CMake picked with:

   .. code-block:: cmake

      include(CMakePrintHelpers)

      cmake_print_variables(CMAKE_CXX_COMPILER_ID)
      cmake_print_variables(CMAKE_CXX_COMPILER_VERSION)
      cmake_print_variables(SOMETHING_UNDEFINED)

   Notice how the command did not fail but printed "" when asking for the value of ``SOMETHING_UNDEFINED``.

We can specify the compilers by exporting the corresponding environment variables:

.. code-block:: console

   $ env FC=gfortran CC=gcc CXX=g++ cmake -S. -Bbuild
   $ cmake --build build

Or by setting CMake variables:

.. code-block:: console

   $ cmake -S. -Bbuild -DCMAKE_Fortran_COMPILER=gfortran -DCMAKE_C_COMPILER=gcc -DCMAKE_CXX_COMPILER=g++
   $ cmake --build build


Variables
---------

These are either CMake- or user-defined. You can obtain the list of CMake-defined variables with:

.. code-block:: console

   $ cmake --help-variable-list

You can create a new variable with the |set| command:

.. code-block:: cmake

   set(MY_VARIABLE "some value")

Variables in CMake are always of string type, but certain commands can interpret
them as other types.  If you want to declare a *list* variable, you will have to
provide it as a ;-separated string. Lists can be manipulated with the ``list``
family of commands.
You can inspect the value of a variable by *dereferencing* it with the ``${}``
operator, as in bash shell.

One of the most confusing aspects in CMake is the scoping of variables. There are three variable scopes in the DSL:

- Function. In effect when a variable is |set| within a function: the variable
  will be visible within the function, but not outside.
- Directory. In effect when processing a ``CMakeLists.txt`` in a directory:
  variables in the parent folder will be available, but any that is |set| in the
  current folder will not be propagated to the parent.
- Cache. These variables are **persistent** across calls to ``cmake`` and
  available to all scopes in the project.
  Modifying a cache variable requires using a special form of the |set| function.

Here is a list of few CMake-defined variables:

- |PROJECT_BINARY_DIR|. This is the build folder for the project.
- |PROJECT_SOURCE_DIR|. This is the location of the root ``CMakeLists.txt`` in the project.
- |CMAKE_CURRENT_LIST_DIR|. This is the folder for the ``CMakeLists.txt`` currently being processed.

Help on a specific built-in variable can be obtained with:

.. code-block:: console

   $ cmake --help-variable PROJECT_BINARY_DIR


Release and debug builds
------------------------

CMake distinguishes the following build types:

- Debug
- Release
- RelWithDebInfo
- MinSizeRel

We can select the build type on the command line:

.. code-block:: console

   $ cmake -S. -Bbuild -DCMAKE_BUILD_TYPE=Debug
   $ cmake --build build

But it is often useful to set a default in the code:

.. code-block:: cmake

   # we default to Release build type
   if(NOT CMAKE_BUILD_TYPE)
     set(CMAKE_BUILD_TYPE "Release")
   endif()


Controlling compiler flags
--------------------------

We can define compiler flags for different compilers and build types:

.. code-block:: cmake

   if(CMAKE_Fortran_COMPILER_ID MATCHES Intel)
       set(CMAKE_Fortran_FLAGS         "${CMAKE_Fortran_FLAGS} -Wall")
       set(CMAKE_Fortran_FLAGS_DEBUG   "-g -traceback")
       set(CMAKE_Fortran_FLAGS_RELEASE "-O3 -ip -xHOST")
   endif()

   if(CMAKE_Fortran_COMPILER_ID MATCHES GNU)
       set(CMAKE_Fortran_FLAGS         "${CMAKE_Fortran_FLAGS} -Wall")
       set(CMAKE_Fortran_FLAGS_DEBUG   "-O0 -g3")
       set(CMAKE_Fortran_FLAGS_RELEASE "-Ofast -march=native")
   endif()

Similarly you can set ``CMAKE_C_FLAGS`` and ``CMAKE_CXX_FLAGS``.

However, this will set the flags for the entire project. If you want
fine-grained control, a nicer way is to define compile flags per target
like in this example (here we want to lower the optimization level for ``mytarget``
to ``-O1``):

.. code-block:: cmake

   target_compile_options(mytarget
     PRIVATE
       -O1
     )

Targets were mentioned only briefly, we talk more about them in the next episode.
