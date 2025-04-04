=================================
Background and Bayesian Analysis
=================================

This is where the full implementation of WinDynB code suite is discussed. A lot of usage instructions are similar to the :ref:`std_bg` . We will only highlight the differences and additional features.

------------------
Installation
------------------

#. Clone the repository.

   .. code-block:: console

      git clone link
      
   .. note::
   
  	 This step can be ignored if you have already cloned the repository before.

#. Navigate to the WinDynB bayes directory.

   .. code-block:: console

      cd WinDynB/bayes
      
#. Compile the code with C++ compiler of choice (g++ shown as example).

   .. code-block:: console

      g++ -shared -fPIC model_calc.cpp -o libmodel.so -I <path-to-boost-installation> -O3 -ftree-vectorize -funroll-loops -lm

   where *<path-to-boost-installation>* should be replaced with the path to your boost library installation.
   
   .. note::
   
   	The compilation command is very different in this as compared to the standalone solver because we are creating a shared library to interface with python. The compiler flags ``-shared`` and ``-fPIC`` ensure that. Make sure to modify the output file extension, linux expects a ``.so`` extension for shared libraries.
   	
   If errors arise during the compilation, try adding the ``-std=C++20`` flag during compilation.
   	
#. Ensure that your python environment is ready to work with shared libraries by installing `CFFI <https://cffi.readthedocs.io/en/stable/>`_.

	.. code-block:: console
	
		conda install -c conda-forge cffi
	  
#. Ensure that Cobaya is installed with one of the cosmological datasets (eg. Planck NPIPE) and working (preferably with MPI).


-----------------------------------------
Constraining :math:`A_s` and :math:`n_s`
-----------------------------------------

In this section we will discuss a simpler (and faster) method to constrain the WI model parameters. Note that this method does not guarantee compatibility with the full CMB Angular Power Spectrum.

We define the log-likelihood :math:`(\mathcal{L})` in the form:

.. math:: 
  \mathcal{\ln \mathcal{L}(\Theta : \zeta)} = -\dfrac{1}{2} \sum_{i} \left[ \ln 2\pi \sigma_{i}^{2} + \left(\dfrac{\theta_i - \mu_i}{\sigma_i}\right)^2 \right]
  
where :math:`\Theta` is a vector of predicted observables (eg. :math:`[A_s,n_s]`) and :math:`\zeta` is a vector of actual observation of these observables :math:`[\mu,\sigma]`. By default the values for :math:`\mu` and :math:`\sigma` are set to the values obtained from TT,TE,EE+lowE+lensing [Planck2018]_.

	.. [Planck2018] Planck Collaboration, "Planck 2018 results. X. Constraints on inflation," *Astron.Astrophys*, 2020, DOI: `10.1051/0004-6361/201833887`
