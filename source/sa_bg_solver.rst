============================
Standalone Background Solver
============================

------------------------
Installation
------------------------

#. Clone the repository.

   .. code-block:: console

      git clone link

#. Navigate to the WinDynB background solver directory.

   .. code-block:: console

      cd WinDynB/bg_solver
 
#. Compile the code with C++ compiler of choice (g++ shown as example).

   .. code-block:: console

      g++ model_calc.cpp -o model_calc -I <path-to-boost-installation> -O3 -lm

   where *<path-to-boost-installation>* should be replaced with the path to your boost library installation.

#. This should create an executable file named model_calc in the same directory which can be run as follows:

   .. code-block:: console

      ./model_calc

   if errors arise during the compilation, try adding the ``-std=C++20`` flag during compilation.

.. _basic:

------------------
Basic Usage
------------------

The standalone background solver comes with the following two files:

#. ``model_calc.cpp``
#. ``Bg.cpp``

The ``model_calc.cpp`` can be used to specify the Warm Inflationary model. You need to supply the potential :math:`V(\phi)`, derivative of the potential with respect to :math:`\phi` :math:`(V_{,\phi})` and the dissipation coefficient :math:`\Upsilon(\phi,T)`. By default the code comes with a generalized form of dissipation coefficient:

.. math::

   \Upsilon(\phi,T) = C_{\Upsilon} T^p \phi^c \ ,


with the dimension of :math:`C_{\Upsilon}` depending upon the parameters :math:`p` and :math:`c`.

.. note::

   In all the equations and parameters in the code suite we set :math:`M_{\text{Pl}} = \dfrac{1}{\sqrt{8\pi G}} = 1`.


The general structure of the code is as follows:

There are constant declarations followed by the function ``model`` that specifies the Warm Inflationary model to be considered. In the ``model`` function, there are individual functions to specify the WI model, set the initial conditions, constraint on initial value of :math:`T_0/H_0>1`, specification of the growth factor :math:`G(Q)` appearing in the WI scalar power spectrum and memory clean-up functions. The ``model`` function supplies the WI model to the background solver ``Bg.cpp`` which implements the numerical solution of the WI background equations as well as power spectrum calculation.


To specify the potential, modify the function ``auto V``. For example, for the quadratic potential :math:`(\frac{1}{2} m^2 \phi^2)`:

.. code-block:: C++

   auto V = [m] (double phi) -> double {
            return 0.5*m*m*phi*phi;
        };

The above function definition is known as a `lambda <https://en.cppreference.com/w/cpp/language/lambda>`_ expression in C++. This helps us write down the potential only as a function of :math:`\phi` while obtaining the value of the constant :math:`m` from the top-level function ``void model``. The constant (fixed) parameters of the potential go in the square brackets following ``auto V``. We consider the natural inflation potential to demonstrate this point:

.. code-block:: C++

   auto V = [lmbd,f] (double phi) -> double {
            return pow(lmbd,4.0)*(1.0+cos(phi/f));
        };

Similarly the potential derivative and dissipation coefficient can be supplied to the code:

.. code-block:: C++

   auto Vd = [m] (double phi) -> double {
            return m*m*phi;
        };


   auto Ups = [Cy,p,c] (double phi,double T) -> double {
            return Cy * pow(T,p) * pow(phi,c);
        };

Next, modify the arguments of the `void model` function to accept the model parameters like :math:`m`, :math:`C_{\Upsilon}`, :math:`g_*` etc.

.. note::

   The arguments of the ``model`` function will depend on the parameters needed for your particular WI model. The parameters ``gst``, ``Np`` and ``therm`` are always required.

.. code-block:: C++

 void model (double phi_ini,double gst,double m,double Cy,double Np,int p,int c,int therm)

Finally, modify the ``int main()`` function and supply your model parameters while calling the ``model`` function.

.. code-block:: C++

   int main () {
      model(13.0,1.0,1e-6,1e4,1.0,3,-2,1);

    ...

By default, the solver prints the value of :math:`N_{\text{end}}` and saves the complete scalar power spectrum (if ``camb_mcmc = 1``) and prints the values of :math:`A_s` and :math:`n_s` (if ``camb_mcmc = 0``).

---------------
Advanced Usage
---------------

For more control over the solver code there are parameters and functions that can be modified as per your requirements.

In the ``model_calc.cpp`` we have:

.. cpp:var:: const double klow = -6.0

   Specifies the *lower* limit of the range of :math:`k \ (\text{Mpc}^{-1})` in :math:`\log_{10}`.

.. cpp:var:: const double kup = 2.0

   Specifies the *upper* limit of the range of :math:`k \ (\text{Mpc}^{-1})` in :math:`\log_{10}`.


.. cpp:var:: const int camb_mcmc = 0

   Set to :math:`0` if you want the solver to not compute the whole power spectrum and just output :math:`A_s` and :math:`n_s`. The ``klow`` and ``kup`` variables have no effect in this case. Set to :math:`1` if you want to compute and save the full scalar power spectrum between :math:`10^{\text{klow}}` and :math:`10^{\text{kup}}`.

.. cpp:var:: const int want_Np_autocalc = 1

   Set to :math:`0` if you wish to specify the number of e-foldings at pivot scale exit (:math:`N_P`) or set to :math:`1` if you want the solver to calculate :math:`N_P` automatically (only when there's a smooth transition to radiation domination at the end of inflation). In both cases, supply a value of ``Np`` to the solver.

.. cpp:var:: const int verbose = 0

   Set to :math:`1` if you want to see the errors encountered by the solver. Useful for debugging.

.. cpp:var:: int npts = 2000

   Sets the number points to be evaluated for the power-spectrum. Will be ignored when ``camb_mcmc=0``.

.. cpp:function:: void model(double param1, double param2, int paramA, int paramB, ...)

   Function specifying the WI model with model parameters as arguments. The ``int therm`` argument of this function specifies the status of thermalization of the inflaton with the radiation bath (``0`` for no thermalization and ``1`` for Bose-Einstein distribution).

.. cpp:function:: double V(double phi)

    A lambda function that specifies the potential :math:`V(\phi)` and captures the potential parameters. Should be modified when specifying a WI model.

    .. code-block:: cpp

        auto V = [param1,param2,...](double phi) -> double {
            return (param1*param2) * phi;
        };


   The parameters ``param1`` and ``param2`` are captured by ``V`` from the top-level function ``model``. The function then behaves like :math:`V(\phi)`.



.. cpp:function:: double Vd(double phi)

    A lambda function that specifies the derivative of the potential with respect to :math:`\phi` :math:`(V_{,\phi})`. Should be modified when specifying a WI model.

    .. code-block:: cpp

        auto Vd = [param1,param2,...](double phi) -> double {
            return (param1*param2) * phi;
        };


.. cpp:function:: double Ups(double phi, double T)

    A lambda function that specifies the form of dissipation coefficient :math:`\Upsilon(\phi,T)`. Should be modified when specifying a WI model.

    .. code-block:: cpp

        auto Ups = [param1,param2,...] (double phi,double T) -> double {
            return param1 * pow(T,param2) * pow(phi,param3);
        };


.. cpp:function:: double GQ(double Q)

    This function specifies the form of the growth factor appearing in the scalar power spectrum for WI. The code by default ships with :math:`G(Q)` specified in the article [Kamali2023]_ valid for differnt forms of :math:`\Upsilon`. Modify this function if your form of :math:`G(Q)` differs from the ones pre-specified.

    .. [Kamali2023] Vahid Kamali et. al., "Recent Developments in Warm Inflation," *Universe 9*, 2023, DOI: `10.3390/universe9030124`

    .. code-block:: cpp

        auto GQ = [param1,param2,...] (double Q) -> double {
            return param1 * Q;
        };

There are other functions within ``model_calc.cpp`` that automatically calculates initial conditions needed to integrate the background equations once :math:`\phi_{\text{initial}}` is specified.

.. code-block:: C++
      :caption: code-block that implements the automatic calculation of ``Q_ini`` and subsequently ``T_ini`` and ``php_ini``.

      try {
            auto res = boost::math::tools::bisect(Qi_find, -20.0, 5.0,root_stop(),max_iter);
            Q_ini = (res.second + res.first)/2;
            Q_ini = pow(10.0,Q_ini);
      }


If your model is more complex and requires setting the initial conditions manually, you can pass them to the ``model`` function and comment out the lines that call ``set_php_ini(Q_ini)``, ``set_T_ini(Q_ini)`` functions, and also the initialization lines for these variables (for eg. comment out the line ``double T_ini=0.0;`` if you are supplying ``T_ini``).

.. warning::
   If your model has a form of :math:`\Upsilon` other than :math:`C_{\Upsilon} T^p \phi^c` then deactivate (comment out) the automatic ``Q_ini`` calculation as the current implementation assumes the form of :math:`\Upsilon` mentioned before.

In the ``Bg.cpp`` we have:

.. cpp:var:: uintmax_t max_iter = 1000000

   Specifies the upper limit for root finding algorithm iterations.

.. cpp:var:: double maxiter_bg = 1e5

   Specifies the upper limit for the background integration iterations.

.. cpp:var:: double maxiter_dxdN = 1e4

   Specifies the upper limit for the :math:`\frac{\text{d}\ln k}{\text{d}N}` integration iterations.

.. cpp:var:: double atol = 1e-10

   Specifies the absolute tolerance for all the integration routines involved.


.. cpp:var:: double rtol = 1e-8

   Specifies the relative tolerance for all the integration routines involved.

.. cpp:var:: double tend = 200.0

   Specifies the upper limit of :math:`N_{\text{end}}`.

Most of the above parameters can be left to their default values and can be changed when more finer control over the solvers is required. The structure of the ``Bg.cpp`` is very similar to the ``model_calc.cpp`` file, this file is meant to be untouched for basic usage but can be modified for more advanced usage involving (but not limited to) modifying background equations, modifying the computation of WI power-spectra and adding constraints on intermediate computation products.

.. cpp:function:: void bg_solver (const function<double(double)> &V,const function<double(double)> &Vd,const function<double(double,double)> &Ups,const function<double(double)> &GQ,double Cr,double Np,double phi_ini,double php_ini,double T_ini,int therm,double kp,double klow,double kup,int npts,int camb_mcmc, int want_Np_autocalc, int verbose)

   Function that contains the background solver as well as the power-spectra calculator. It takes the defined model from ``model_calc.cpp`` file and solves the relevant equations to produce the observables. It is recommended that you do not modify the arguments of this function as that might break the functionality, the arguments essentially capture the variables and functions defined in the ``model_calc.cpp`` file.

.. cpp:function:: double P(double k)

    This function should be modified when your model requires a change in the WI scalar power-spectrum. For most cases, the default function should work as it assumes WI power spectra of the form:

    .. math::
      P_{\mathcal{R}}(k_*) = \left(\dfrac{H_*}{2\pi \phi'_*}\right)^2 \left[1 + 2 n_* + \dfrac{T_*}{H_*}\dfrac{2 \sqrt{3}\pi Q_*}{\sqrt{3+4\pi Q_*}}\right] G(Q_*)


    .. code-block:: cpp

       auto P = [Nask,phiasN,phpasN,TasN,H,Q,therm,GQ] (double k) -> double {
         double NN = Nask(k);
         double phin = phiasN(NN);
         double phpn = phpasN(NN);
         double Tn = TasN(NN);
         double Hn = H(phin,phpn,Tn);
         double Qn = Q(phin,phpn,Tn);
         double distrib = 0.0;
         if (therm == 1) {
            distrib = 1/tanh(Hn/(2*Tn));
         }
         else if (therm == 0) {
            distrib = 1.0;
         }
         return pow((Hn/(2.0*M_PI*phpn)),2.0) * (distrib + (Tn/Hn)*( 2.0*sqrt(3.0)*M_PI*Qn/sqrt(3+4*M_PI*Qn) )) * GQ(Qn);
      };

.. cpp:function:: double PT(double k)

    This function implements the tensor power spectrum, same as for the standard (cold) inflationary case.

    .. code-block:: cpp

       auto PT = [Nask,phiasN,phpasN,TasN,H] (double k) -> double {
         double NN = Nask(k);
         double phin = phiasN(NN);
         double phpn = phpasN(NN);
         double Tn = TasN(NN);
         double Hn = H(phin,phpn,Tn);
         return (2.0*pow(Hn,2.0))/pow(M_PI,2.0);
      };

If you wish to add additional constraints on a parameter

The ``main`` function can then be modified according to the :ref:`basic` section to obtain the results.
