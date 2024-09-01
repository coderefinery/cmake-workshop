.. _fetch-content:


Automated dependency handling with FetchContent
===============================================

.. objectives::

   - Learn how to download your dependencies at configure-time with ``FetchContent``.
   - Learn how fetched content can be used natively within your build system.


CMake offers **two modules** to satisfy missing dependencies on-the-fly:
the ``ExternalProject`` and ``FetchContent`` modules.

Using ``ExternalProject``
    - The download step happens at `project build-time
      <https://cmake.org/cmake/help/latest/module/ExternalProject.html>`_.
    - You can handle dependencies that **do not** use CMake.
    - You need to rewrite your whole build system as a `superbuild
      <https://github.com/dev-cafe/cmake-cookbook/blob/master/chapter-08/README.md>`_.
Using ``FetchContent``
    - The download step happens at `project configure-time
      <https://cmake.org/cmake/help/latest/module/FetchContent.html>`_.
    - You can handle dependencies that **do not** use CMake.
    - It's an well-delimited change to an existing CMake build system.

Both are extremely powerful mechanisms.  In this episode, we will discuss the
``FetchContent`` module.


The FetchContent module
-----------------------

To fetch dependencies on-the-fly at configure-time you will include the built-in
CMake module ``FetchContent``.  This module has been part of CMake since its
3.11 version and has been steadily improved since then.

There are two steps in a ``FetchContent``-based workflow:

#. **Declaring** the content to fetch with |FetchContent_Declare|. This can be a
   tarball (local or remote), a local folder, or a version control repository
   (Git, SVN, etc.).

#. **Populating** the content with |FetchContent_MakeAvailable|. This commands
   *adds* the targets declared in the external content to your build system.

   Since targets from the external project are added to your own project, you
   will be able to use them in the same way you would when obtaining them
   through a call to |find_package|: you can use *found* and *fetched* content
   in the same exact way.
   If you need to set options for building the external project, you will set
   them as CMake variables *before* calling |FetchContent_MakeAvailable|.


.. _fetch-content-exercise:

Exercise: Unit testing with Catch2
----------------------------------

Unit testing is a valuable technique in software engineering: it can help
identify functional regressions with a very fine level of control, since each
unit test is meant to exercise isolated components in your codebase.  Equipping
your codebase with integration *and* unit tests is very good practice.

There are many unit testing frameworks for the C++ language. Each of them
stresses a slightly different approach to unit testing and comes with its own
peculiarities in set up and usage.  In this episode, we will show how to use
`Catch2 <https://github.com/catchorg/Catch2>`_ a very popular unit testing
framework which emphasizes a test-driven development workflow.  Catch2 is
distributed as a single header file, which is one of its most appealing
features: it can easily be included in any project. Rather than download the
header file and adding it to our codebase, we can use ``FetchContent`` to
satisfy this dependency for us when needed.


.. challenge:: Exercise: Fetching and using Catch2 to test our code

   We want to use the Catch2 unit testing framework for our test code
   (we stay with the example from :ref:`testing`)
   and so we changed ``test.cpp`` to now contain:

   .. literalinclude:: examples/fetch-content/test.cpp
      :language: c++

   There are two tests now, they also have tags (``[short]`` and ``[long]``).

   1. Adapt the ``CMakeLists.txt`` with these new lines:

   .. literalinclude:: examples/fetch-content/CMakeLists.txt
      :language: cmake
      :emphasize-lines: 20, 23-26, 29, 33, 40-41


   2. Build the code and run the tests with:

     .. code-block:: console

        $ ./cpp_test --success

   3. Also try to run only one of the two:

     .. code-block:: console

        $ ./cpp_test [short] --success

   4. Now run them via CTest:

     .. code-block:: console

        $ ctest --verbose

   5. How would you adapt ``CMakeLists.txt`` to run them as two separate tests in CTest?
      Try it out.

   6. Discuss the pros and cons of running them as one or as two from
      CMake/CTest perspective.


.. keypoints::

   - CMake lets you satisfy dependencies *on-the-fly*.
   - You can do so at build-time with ``ExternalProject``, but you need to adopt
     a superbuild framework.
   - At configure-time, you can use the ``FetchContent`` module: it is more a appropriate with dependencies that also use CMake.
