.. WI_Docs documentation master file, created by
   sphinx-quickstart on Fri Mar 28 14:37:37 2025.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

WinDynB, Warm Inflationary Dynamics and Bayesian analysis 
=========================================================

WinDynB (**W**\arm **in**\flationary **Dyn**\amics and **B**\ayesian analysis ) is a suite of applications that numerically solve the Warm Inflationary background equations, compute inflationary observables like :math:`A_s`, :math:`n_s` and :math:`r` that can further be used to perform bayesian analysis on the warm inflation model of interest.

The suite has two components:

#. C++ code that solves the background and computes the primordial scalar and tensor power-spectrum.
#. Python codes to perform the bayesian analysis (based on Cobaya) and generate plots.

The C++ code handles the computationally intensive background solution efficiently especially when used with Cobaya to perform bayesian analysis. The python codes are used for ease of analysis and plotting with seamless interface to the C++ code. The WinDynB code suite has been designed to be customizeable and extensible to accomodate different Warm Inflationary models. The potential :math:`V(\phi)` and form of the dissipation coefficient :math:`\Upsilon(\phi,T)` can be changed without touching the solver code; as well as the growth function :math:'`G(Q)` appearing in the scalar power spectrum.

Main goal of this code suite is to simplify the testing of warm inflationary models against the current Cosmic Microwave Backgorund (CMB) constraints. You have to supply your model and run Cobaya to perform the bayesian analysis with the dataset of your choice. Some knowledge of C++ and Python would help but is not required, even though you will have to modify the C++ code to supply your model, it can be done without prior knowledge of C++. Advanced features are also provided if you wish to place additional constraints on your model (for example: :math:`Q_* \leq 1`), obtain the full power spectrum as a function of :math:`k`, automatically calculate the exit of pivot-scale :math:`k_*` assuming a smooth transition to radiation domination after the end of inflation and so on...

The documentation will be separated into two main subsections:

- Standalone background solver.
- Complete WinDynB suite of applications with bayesian analysis.

.. note::

   This project is under active development.

Contents
--------
.. toctree::
   install/install
   install/quickstart
   :maxdepth: 2


