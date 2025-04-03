============================
Standalone Background Solver
============================

------------------------
Installation
------------------------

#. Clone the repository.

.. code-block:: console

   git clone link

#. Navigate to the WinDynB directory.

.. code-block:: console

   cd WinDynB
 
#. Compile the code with C++ compiler of choice (g++ shown as example).

.. code-block:: console

   g++ model_calc.cpp -o model_calc -I <path-to-boost-installation> -O3 -lm

where *<path-to-boost-installation>* should be replaced with the path to your boost library installation.


