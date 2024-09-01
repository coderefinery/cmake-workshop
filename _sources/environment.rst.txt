.. _environment:


Detecting your environment
==========================

.. objectives::

   - Learn how to discover the operating system and processor capabilities.
   - Learn how to handle platform- and compiler-dependent source code.

CMake comes pre-configured with sane defaults for a multitude of properties of
the environments in which it can be used.  Default generator, default compilers,
and compiler flags are few and most notable examples of this up-front
configuration.

Run the following if you are curious about what kind of configuration ships for
your version of CMake and operating system:

.. code-block:: console

   $ cmake --system-information

In this episode, we will show how to use CMake to introspect the environment in
which we are running. This is very common in build systems, since it allows to
customize the creation of artifacts on-the-fly.


Discovering the operating system
--------------------------------

Sometimes we need to write code that performs different operations based on
compile-time constants.
Like in this example (``example.cpp``):

.. literalinclude:: examples/os-dependent/example.cpp
   :language: c++


.. typealong:: Conditional compilation with preprocessor definitions

   We can do this with the following ``CMakeLists.txt``:

   .. literalinclude:: examples/os-dependent/CMakeLists.txt
      :language: cmake

   We achieved this with a combination of host system
   introspection and the |target_compile_definitions| command.


Discovering processor capabilities and generating a configuration file
----------------------------------------------------------------------

A common customization is to apply processor-specific compiler flags. We can
gain such information on the host system with the built-in
|cmake_host_system_information| command.

Another thing that is common and convenient is to have a single file containing
all these compile-time constants, rather than passing them to preprocessor.
This can be achieved by having a *scaffold* file and then letting CMake
configure it with |configure_file| after discovering the values for all the
necessary compile-time constants.

Here is an example source file (``example.cpp``):

.. literalinclude:: examples/configure-file/problem/example.cpp
   :language: c++

The file ``config.h`` does not exist, it will be generated at configure time
from ``config-template.h``:

.. literalinclude:: examples/configure-file/problem/config-template.h
   :language: c

Here is the ``CMakeLists.txt`` which takes care of introspection and also
generates the file ``config.h``:

.. literalinclude:: examples/configure-file/problem/CMakeLists.txt
   :language: cmake
   :emphasize-lines: 29


.. challenge:: Exercise: record more useful information using configure_file

   Your goal is to adapt the above example (``content/examples/configure-file/problem/``)
   and make it possible to get the compiler and compiler version into the
   output of your code:

   .. literalinclude:: examples/configure-file/problem/example.cpp
      :language: c++
      :emphasize-lines: 15-16

   Hints:

   - You will only need to uncomment these and only have to modify ``configure-template.h``.
   - You will not need to modify ``CMakeLists.txt``.
   - To find the relevant CMake variable names, have a look in :ref:`flags-definitions-debugging`.


.. discussion::

   **What else to record when configuring?** Here are some ideas:

   - Code version and/or Git hash
   - Compiler versions
   - Compiler flags
   - Compile-time definitions
   - Library versions
   - Some environment variables

   Discuss how they can be useful for you and others running your code.


.. keypoints::

   - CMake can *introspect* the host system.
   - You can build source code differently, based on the OS, the processor, the
     compiler, or any combination thereof.
   - You can generate source code when configuring the project with |configure_file|.
