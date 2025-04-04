===================
Prerequisites
===================

#. Install anaconda by following the intall instructions at `anaconda.org <https://www.anaconda.com/docs/getting-started/miniconda/install>`_ for your operating system. It is not essential to the operation of the code suite but recommended to setup a conda environment separately for a WinDynB install.


#. Create a new conda environment, we will be using windynb as the environment name for illustration.

   .. code-block:: console

      conda create -n windynb python

#. Activate the conda environment created in the previous step and then add essential packages like a suite of compilers (we will use GNU compilers), openmpi.

   .. code-block:: console

      conda install -c conda-forge gcc gfortran gxx openmpi

   .. note::

      You can also use the compilers provided by your system and other compilers like clang although the testing was exclusively done using g++.

#. Download the boost library from `boost.org <https://www.boost.org/users/download/>`_ depending on your operating system and save it to a location of your choice.

   .. code-block:: console

      wget <boost-download-link> -P <path-to-save-to>

   There is no installation required for boost library, just extract the downloaded package.

   
#. Finally, install Cobaya by following the instructions given in the `documentation <https://cobaya.readthedocs.io/en/latest/>`_. Cobaya implements the bayesian analysis part of the code suite.
