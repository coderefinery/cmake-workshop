Setting up your system
======================

In order to follow this workshop, you will need access to compilers,
Python and CMake. You can use an HPC cluster if you have access to
one, but the instructions here cover how to install the prerequisites
on your own computer.

These instructions are based on installing compilers and CMake via the `Conda
package and enviroment manager <https://docs.conda.io/en/latest/>`_, as it
provides a convenient way to install binary packages in an isolated software
environment, without affecting the rest of your operating system.

If you have a recent CMake (at least 3.14) and recent compilers and Python 3
on your system already, you can also most probably use these directly.


For Windows users
^^^^^^^^^^^^^^^^^

We strongly recommend to use (and install if necessary) the Windows
Subsystem for Linux (WSL) as it is a powerful tool which will likely
be useful also after the workshop.  Inside WSL you will need Python 3
and the conda environment manager.  A useful guide to doing
this is found at https://github.com/kapsakcj/win10-linux-conda-how-to.
The installation of the required dependencies in a WSL terminal is
documented below.


For MacOS and Linux users
^^^^^^^^^^^^^^^^^^^^^^^^^

MacOS and Linux users can open a terminal and install
`Miniconda <https://docs.conda.io/en/latest/miniconda.html>`_:

- For MacOS see https://docs.conda.io/en/latest/miniconda.html#macosx-installers
- For Linux see https://docs.conda.io/en/latest/miniconda.html#linux-installers


Creating an environment and installing packages
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Once you have ``conda`` installed (and WSL if you're on Windows) you
can use the :download:`environment.yml <../environment.yml>` file to
install the dependencies.  First save it to your hard drive by
clicking the link. On your hard-drive move the file to a folder where you
would like to work in during the workshop, and then in a terminal navigate to the folder
and type::

  conda env create -f environment.yml


You then need to activate the new environment by::

  conda activate cmake-workshop


Now you should have CMake, compilers, Python and a few other packages
installed!
