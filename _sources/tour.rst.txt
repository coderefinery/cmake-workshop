.. _tour:


Brief tour to demonstrate concepts/capabilities
===============================================

.. objectives::

   - Get a feel for how CMake looks.
   - See 2-3 use cases where CMake shines (environment/library discovery, code
     generation, testing, modular code structure, Fortran 90 support).
   - The goal here is not to focus on syntax or details but rather the big picture and the potential.


.. note::

   To follow-along in your terminal you can clone or download the tutorial repository
   (https://github.com/coderefinery/cmake-workshop):

   .. code-block:: console

      $ git clone https://github.com/coderefinery/cmake-workshop.git

   You find the example in the `content/examples/calculator/ <https://github.com/coderefinery/cmake-workshop/tree/main/content/examples/calculator>`__
   folder.


We will discuss an example project "calculator" which can add and subtract
and is a mixed Fortran-C++ project with the following layout:

.. code-block:: text
   :emphasize-lines: 2, 6-7, 13

   calculator/
   ├── CMakeLists.txt
   ├── src
   │   ├── add.f90
   │   ├── calculator.h
   │   ├── CMakeLists.txt
   │   ├── git-version.cmake
   │   ├── main.cpp
   │   ├── subtract.f90
   │   └── version-template.h
   └── test
       ├── calculator.cpp
       ├── CMakeLists.txt
       └── driver.cpp

#. Before we have a closer look we open up the top-level ``CMakeLists.txt`` and for the moment comment out
   the part with the testing. We will bring it back later:

   .. literalinclude:: examples/calculator/CMakeLists.txt
      :language: cmake
      :emphasize-lines: 23, 26
      :linenos:

#. Now we build the project and observe what it means to build out of source.

   .. code-block:: console

      $ cmake -S. -Bbuild
      $ cmake --build build

   But many people (especially CMake old-timers like the presenter) do this instead with Unix Makefiles:

   .. code-block:: console

      $ mkdir build
      $ cd build
      $ cmake ..
      $ make

#. We also run the compiled binary:

   .. code-block:: console

      $ ./bin/calculator.x

      Running calculator version v1.0.0
      Configure-time Git hash: fca3425

      2 + 3 = 5
      2 - 3 = -1

#. We browse and discuss the main ``CMakeLists.txt`` and also ``src/CMakeLists.txt``.

#. We discuss how the Git hash ends up in the generated ``version.h`` with the
   help of:

   .. literalinclude:: examples/calculator/src/git-version.cmake
      :language: cmake
      :emphasize-lines: 9
      :linenos:

#. Then we re-activate the testing, reconfigure from scratch (this is not needed but it shows the moment better
   when the external library is downloaded):

   .. literalinclude:: examples/calculator/test/CMakeLists.txt
      :language: cmake
      :emphasize-lines: 2, 5-8, 11, 25
      :linenos:

#. We build and run the test set:

   .. code-block:: console

      $ ctest

#. We also run the test set (consisting of two tests) in parallel:

   .. code-block:: console

      $ ctest -j 2

#. For the Fortran folks we create one more Fortran module and "use" it in
   ``add.f90`` and demonstrate that the order does not matter and CMake figures
   it out.

#. We try the install target:

   .. code-block:: console

      $ mkdir build
      $ cd build
      $ cmake -D CMAKE_INSTALL_PREFIX=/tmp/calculator-example
      $ make
      $ make test
      $ make install
