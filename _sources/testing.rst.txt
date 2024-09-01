.. _testing:


Creating and running tests with CTest
=====================================

.. objectives::

   - Learn how to produce test executables with CMake.
   - Learn how to run your tests through CTest.

Testing is an essential activity in the development cycle. A well-designed test
suite will help you detect bugs and can also facilitate the onboarding of new
developers.  In this episode, we will look into how to use CTest to define and
run our tests.


Adding tests to your project
----------------------------

In CMake and CTest, a test is any command returning an exit code. It does not
really matter how the command is issued or what is run: it can be a C++
executable or a Python script or a shell script. As long as the execution
returns a zero or non-zero exit code, CMake will be able to classify the test
as succeeded or failed, respectively.

There are two steps to perform to integrate your CMake build system with the CTest tool:

1. Call the ``enable_testing`` command. This takes no arguments.
2. Add tests with the |add_test| command.

.. signature:: |add_test|

   .. code-block:: cmake

      add_test(NAME <name> COMMAND <command> [<arg>...]
         [CONFIGURATIONS <config>...]
         [WORKING_DIRECTORY <dir>]
         [COMMAND_EXPAND_LISTS])

   This command accepts *named arguments*, only ``NAME`` and ``COMMAND`` are
   mandatory.  The former specifies the identifying name of the test, while the
   latter sets up what command to run.


.. typealong:: Our first test project

   We will build a simple library to sum integers and an executable using this library.
   This example is in ``content/examples/testing/``.

   If you compile the code (please try!) you get an executable that can sum integers
   given on the command line:

   .. code-block:: console

      $ ./sum_up 1 2 3 4 5

      15

      $ ./sum_up 100 200

      300

   The core of this example project is the ``sum_integers`` function:

   .. literalinclude:: examples/testing/sum_integers.cpp
      :language: c++

   Our goal will be to write tests for this function.

   As we wrote above, any script or binary that can return zero or non-zero can be used for
   this and we will start with this basic ``test.cpp``:

   .. literalinclude:: examples/testing/test.cpp
      :language: c++
      :emphasize-lines: 9, 11

   This is how we can hook it up to CMake/CTest:

   .. literalinclude:: examples/testing/CMakeLists.txt
      :language: cmake
      :emphasize-lines: 20, 21, 24, 27-30

   Note the use of `generator expression (gen-exp)
   <https://cmake.org/cmake/help/latest/manual/cmake-generator-expressions.7.html>`_
   to avoid specifying the complete path to the executable ``cpp_test``.

   We can now compile and run our test:

   .. code-block:: console

      $ cmake -S. -Bbuild
      $ cd build
      $ cmake --build .
      $ ctest


Test properties: labels, timeout, and cost
------------------------------------------

When you use |add_test|, you give a unique name to each test.  But using
|set_tests_properties| we can give tests other properties such as labels,
timeout, cost, and many more.

For a complete list of properties that can be set on tests search for
"Properties on Tests" in the output of:

.. code-block:: console

   $ cmake --help-properties

or visit the CMake documentation `online <https://cmake.org/cmake/help/v3.19/manual/cmake-properties.7.html#properties-on-tests>`_.


The CTest command-line interface
--------------------------------

.. typealong:: How to use CTest effectively.

   We will now demonstrate the CTest command-line interface (CLI) using the
   solution of the previous exercise.

   The ``ctest`` command is part of the CMake installation. We can find help on its usage with:

   .. code-block:: console

      $ ctest --help

   **Remember**, to run your tests through CTest, you will first need to move
   into the build folder:

   .. code-block:: console

      $ cd build
      $ ctest

   This will run all the tests in your test suite.
   You can list the names of the tests in the test suite with:

   .. code-block:: console

      $ ctest -N

   Verbosity options are also quite helpful, especially when debugging failures:

   .. code-block:: text

      -V,--verbose                 = Enable verbose output from tests.
      -VV,--extra-verbose          = Enable more verbose output from tests.

   With ``--output-on-failure``, CTest will print to screen the output of
   failing tests.

   You can select *subsets* of test to run:

   - By *name*, with the ``-R <regex>`` flag. Any test whose *name* can be
     captured by the passed regex will be run.  The ``-RE <regex>`` option
     *excludes* tests by name using a regex.
   - By *label*, with the ``-L <regex>`` flag. Any test whose *labels* can be
     captured by the passed regex will be run.  The ``-LE <regex>`` option
     *excludes* tests by label using a regex.
   - By *number*, with the ``-I [Start,End,Stride,test#,test#|Test file]`` flag.
     This is usually not the most convenient option for selecting subsets of
     tests.

   It is possible to rerun failed tests with:

   .. code-block:: console

      $ ctest --rerun-failed

   Finally, you can parallelize test execution:

   .. code-block:: console

      $ ctest -j N
      $ ctest --parallel N


.. _testing_exercises:

Exercises: testing with CTest
-----------------------------

.. challenge:: Exercise: adding tests and labels

   1. Build the "summing up" example from above.

   2. Run the ``cpp_test`` binary directly (it will produce no output).

   3. Run ``ctest --verbose``.

   4. Try to break the code and check whether CTest will detect the degradation.

   5. Try to add a second test to the project.


.. challenge:: Exercise: running tests in parallel and understanding the COST property

   This example is in ``content/examples/testing-parallel/``.

   1. Build the project and run the test set with ``ctest``, observe the order of tests.

   2. Now uncomment the lines containing COST in ``CMakeLists.txt``:

   .. literalinclude:: examples/testing-parallel/CMakeLists.txt
      :language: cmake
      :emphasize-lines: 17, 22, 25, 28, 31

   3. Run the tests again and observe the order now.

   4. Run the tests in parallel on several cores (if you have them available).

   5. Discuss why it can be beneficial to define the COST if some tests take
      much longer than others (we could have also reordered them manually).

.. keypoints::

   - Any custom command can be defined as a test in CMake.
   - Tests can be run through CTest.
   - CTest particularly shines when running sequential tests in parallel.
