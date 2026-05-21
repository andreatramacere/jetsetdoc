.. _jet_leptonic_equilibrium:

Leptonic equilibrium
====================

In this section we show hot to build a leptonic jet model implementing the leptonic equilibrium. Primaries :math:`e^-`, are evolved to the equilibrium following the approach in [Inoue96]_. 
A validation of the integral solution for the :math:`e^{-}` equilibrium used for the leptonic jet against the Fokker-Plank equation solution, implemented in the  :class:`.JetTimeEvol` class, is presented in  :ref:`leptonic_equilibrium_jet_validation_guide`.

An application of this model to model fitting can be found here:  :ref:`model_fitting_lept_eq_SSC`, and for the EC can be found here: :ref:`model_fitting_lept_eq_EC`.

.. note:: The approach presented in this notebook can not reproduce self-consistently the effect of the competition between radiative cooling and acceleration, hence parameters such as ``gmax`` have to be provided according to the object class (I/L/HSP or FSRQ/BL Lac).

.. code:: ipython3

    import warnings
    warnings.filterwarnings('ignore')
    import matplotlib.pylab as plt
    import jetset

.. code:: ipython3

    print('tested with',jetset.__version__)


.. parsed-literal::

    tested with 1.4.0rc3


.. code:: ipython3

    import numpy as np
    
    def pl_fit(jet_obj,x_eq,y_lim=None):
        """ helper function to make a pl fit of the emitters above and below the cooling break.
        The function will try to avoid the cut-off close to gmax
        """
        msk=jet_obj.emitters_distribution.n_gamma_e>0
        
        msk2=np.logical_and(msk,jet_obj.emitters_distribution.gamma_e>x_eq*1.5)
        cut_off=max(jet_obj.parameters.gmax.val/2, x_eq*2)
        msk2=np.logical_and(msk2,jet_obj.emitters_distribution.gamma_e<cut_off)
        
        low_cut_off=min(jet_obj.parameters.gmin.val*2,x_eq*.5)
        msk1=np.logical_and(msk,jet_obj.emitters_distribution.gamma_e<=x_eq*.5)
        msk1=np.logical_and(msk1,jet_obj.emitters_distribution.gamma_e>low_cut_off)
        f=plt.figure()
        x = np.log10(jet_obj.emitters_distribution.gamma_e[msk])
        y = np.log10(jet_obj.emitters_distribution.n_gamma_e[msk])
        plt.plot(x, y)
        for m in [msk1,msk2]:
            x = np.log10(jet_obj.emitters_distribution.gamma_e[m])
            y = np.log10(jet_obj.emitters_distribution.n_gamma_e[m])
    
            (m,q), cov = np.polyfit(x, y, 1, cov=True)
            y_fit = m * x + q
    
            
            
            plt.plot(x, y_fit, label=f'spectral slope={m:.3g}')
           
            plt.tight_layout()
        plt.axvline(np.log10(x_eq),ls='--',label='eq.')
        plt.xlabel('log(n(gamma))')
        plt.ylabel('log(gamma)')
        plt.legend()
        if y_lim is not None:
            plt.ylim(y_lim)
        plt.show()
    
        return f
        

Setting up the model for SSC
----------------------------

The jet model implementing the leptonic equilibrium is created by passing an :class:`~jetset.jet_emitters_factory.InjEmittersFactory` product as ``emitters_distribution`` parameter.

.. code:: ipython3

    from jetset.jet_model import Jet
    from jetset.jet_emitters_factory import InjEmittersFactory
    
    q_inj = InjEmittersFactory().create_inj_emitters('pl')
    
    my_jet=Jet(emitters_distribution=q_inj)
    my_jet.parameters.p.val=2.2
    my_jet.parameters.z_cosm.val=3.080000e-02
    my_jet.parameters.R.val=1E16
    my_jet.parameters.B.val=.1
    my_jet.parameters.L_inj.val=5E38
    my_jet.parameters.gmin.val=100
    my_jet.parameters.gmax.val=1E6
    my_jet.parameters.T_esc_e_primaries.val=1.0
    my_jet.parameters.beam_obj.val=25
    my_jet.eval()

The ``Jet`` model inherits as parameters the parameters of the injected
emitters. Of course, when creating the ``q_inj`` object you can use the
same approach used for ``emitters``, the only difference is that in
place of the emitters density ``N`` you will pass the injected
luminosity ``L_inj``.

.. note:: the parameter  ``T_esc_e_primaries``,  is expressed in units of `R/c`, will drive the equilibrium energy.

.. note:: When using  the leptonic equilibrium, the ``parameters`` attribute of ``jet.emitters_distribution`` will not be accessible, use directly the ``parameters`` attribute of ``Jet``.


.. code:: ipython3

    my_jet.parameters



.. raw:: html

    <i>Table length=11</i>
    <table id="table4849481728-583288" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>par type</th><th>units</th><th>val</th><th>phys. bound. min</th><th>phys. bound. max</th><th>log</th><th>frozen</th></tr></thead>
    <tr><td>jet_leptonic</td><td>R</td><td>region_size</td><td>cm</td><td>1.000000e+16</td><td>1.000000e+03</td><td>1.000000e+30</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R_H</td><td>region_position</td><td>cm</td><td>1.000000e+17</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>magnetic_field</td><td>gauss</td><td>1.000000e-01</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>cold_p_to_rel_e_ratio</td><td></td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>beaming</td><td></td><td>2.500000e+01</td><td>1.000000e-04</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>redshift</td><td></td><td>3.080000e-02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmin</td><td>low-energy-cut-off</td><td>lorentz-factor*</td><td>1.000000e+02</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>high-energy-cut-off</td><td>lorentz-factor*</td><td>1.000000e+06</td><td>1.000000e+00</td><td>1.000000e+15</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>p</td><td>LE_spectral_slope</td><td></td><td>2.200000e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>T_esc_e_primaries</td><td>escape_time</td><td>R / c</td><td>1.000000e+00</td><td>1.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>L_inj</td><td>L_inj</td><td>erg / s</td><td>5.000000e+38</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    </table><style>table.dataTable {clear: both; width: auto !important; margin: 0 !important;}
    .dataTables_info, .dataTables_length, .dataTables_filter, .dataTables_paginate{
    display: inline-block; margin-right: 1em; }
    .paginate_button { margin-right: 5px; }
    </style>
    <script>
    
    var astropy_sort_num = function(a, b) {
        var a_num = parseFloat(a);
        var b_num = parseFloat(b);
    
        if (isNaN(a_num) && isNaN(b_num))
            return ((a < b) ? -1 : ((a > b) ? 1 : 0));
        else if (!isNaN(a_num) && !isNaN(b_num))
            return ((a_num < b_num) ? -1 : ((a_num > b_num) ? 1 : 0));
        else
            return isNaN(a_num) ? -1 : 1;
    }
    
    require.config({paths: {
        datatables: 'https://cdn.datatables.net/1.10.12/js/jquery.dataTables.min'
    }});
    require(["datatables"], function(){
        console.log("$('#table4849481728-583288').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table4849481728-583288').dataTable({
            order: [],
            pageLength: 100,
            lengthMenu: [[10, 25, 50, 100, 500, 1000, -1], [10, 25, 50, 100, 500, 1000, 'All']],
            pagingType: "full_numbers",
            columnDefs: [{targets: [4, 5, 6], type: "optionalnum"}]
        });
    });
    </script>





.. parsed-literal::

    None



.. code:: ipython3

    my_jet.eval()
    my_jet.energetic_report()



.. raw:: html

    <i>Table length=43</i>
    <table id="table5172659792-485184" class="table-striped table-bordered table-condensed">
    <thead><tr><th>name</th><th>type</th><th>units</th><th>val</th></tr></thead>
    <tr><td>BulkLorentzFactor</td><td>jet-bulk-factor</td><td></td><td>2.500000e+01</td></tr>
    <tr><td>U_e</td><td>Energy dens. blob rest. frame</td><td>erg / cm3</td><td>3.656928e-05</td></tr>
    <tr><td>U_p_cold</td><td>Energy dens. blob rest. frame</td><td>erg / cm3</td><td>1.503276e-01</td></tr>
    <tr><td>U_B</td><td>Energy dens. blob rest. frame</td><td>erg / cm3</td><td>3.978874e-04</td></tr>
    <tr><td>U_Synch</td><td>Energy dens. blob rest. frame</td><td>erg / cm3</td><td>9.898015e-07</td></tr>
    <tr><td>U_Synch_DRF</td><td>Energy dens. disk rest. frame</td><td>erg / cm3</td><td>3.866412e-01</td></tr>
    <tr><td>U_Disk</td><td>Energy dens. blob rest. frame</td><td>erg / cm3</td><td>0.000000e+00</td></tr>
    <tr><td>U_BLR</td><td>Energy dens. blob rest. frame</td><td>erg / cm3</td><td>0.000000e+00</td></tr>
    <tr><td>U_DT</td><td>Energy dens. blob rest. frame</td><td>erg / cm3</td><td>0.000000e+00</td></tr>
    <tr><td>U_Corona</td><td>Energy dens. blob rest. frame</td><td>erg / cm3</td><td>0.000000e+00</td></tr>
    <tr><td>U_CMB</td><td>Energy dens. blob rest. frame</td><td>erg / cm3</td><td>0.000000e+00</td></tr>
    <tr><td>U_Star</td><td>Energy dens. blob rest. frame</td><td>erg / cm3</td><td>0.000000e+00</td></tr>
    <tr><td>U_Disk_DRF</td><td>Energy dens. disk rest. frame</td><td>erg / cm3</td><td>0.000000e+00</td></tr>
    <tr><td>U_BLR_DRF</td><td>Energy dens. disk rest. frame</td><td>erg / cm3</td><td>0.000000e+00</td></tr>
    <tr><td>U_DT_DRF</td><td>Energy dens. disk rest. frame</td><td>erg / cm3</td><td>0.000000e+00</td></tr>
    <tr><td>U_Corona_DRF</td><td>Energy dens. disk rest. frame</td><td>erg / cm3</td><td>0.000000e+00</td></tr>
    <tr><td>U_CMB_DRF</td><td>Energy dens. disk rest. frame</td><td>erg / cm3</td><td>0.000000e+00</td></tr>
    <tr><td>U_Star_DRF</td><td>Energy dens. disk rest. frame</td><td>erg / cm3</td><td>0.000000e+00</td></tr>
    <tr><td>U_seed_tot</td><td>Energy dens. blob rest. frame</td><td>erg / cm3</td><td>9.898015e-07</td></tr>
    <tr><td>L_Sync_rf</td><td>Lum. blob rest. frame.</td><td>erg / s</td><td>3.728882e+37</td></tr>
    <tr><td>L_SSC_rf</td><td>Lum. blob rest. frame.</td><td>erg / s</td><td>4.209003e+34</td></tr>
    <tr><td>L_EC_Disk_rf</td><td>Lum. blob rest. frame.</td><td>erg / s</td><td>0.000000e+00</td></tr>
    <tr><td>L_EC_BLR_rf</td><td>Lum. blob rest. frame.</td><td>erg / s</td><td>0.000000e+00</td></tr>
    <tr><td>L_EC_DT_rf</td><td>Lum. blob rest. frame.</td><td>erg / s</td><td>0.000000e+00</td></tr>
    <tr><td>L_EC_Corona_rf</td><td>Lum. blob rest. frame.</td><td>erg / s</td><td>0.000000e+00</td></tr>
    <tr><td>L_EC_CMB_rf</td><td>Lum. blob rest. frame.</td><td>erg / s</td><td>0.000000e+00</td></tr>
    <tr><td>L_EC_Star_rf</td><td>Lum. blob rest. frame.</td><td>erg / s</td><td>0.000000e+00</td></tr>
    <tr><td>jet_L_Sync</td><td>jet Lum.</td><td>erg / s</td><td>5.821716e+39</td></tr>
    <tr><td>jet_L_SSC</td><td>jet Lum.</td><td>erg / s</td><td>6.571304e+36</td></tr>
    <tr><td>jet_L_EC_Disk</td><td>jet Lum.</td><td>erg / s</td><td>0.000000e+00</td></tr>
    <tr><td>jet_L_EC_BLR</td><td>jet Lum.</td><td>erg / s</td><td>0.000000e+00</td></tr>
    <tr><td>jet_L_EC_DT</td><td>jet Lum.</td><td>erg / s</td><td>0.000000e+00</td></tr>
    <tr><td>jet_L_EC_Corona</td><td>jet Lum.</td><td>erg / s</td><td>0.000000e+00</td></tr>
    <tr><td>jet_L_EC_Star</td><td>jet Lum.</td><td>erg / s</td><td>0.000000e+00</td></tr>
    <tr><td>jet_L_EC_CMB</td><td>jet Lum.</td><td>erg / s</td><td>0.000000e+00</td></tr>
    <tr><td>jet_L_pp_gamma</td><td>jet Lum.</td><td>erg / s</td><td>0.000000e+00</td></tr>
    <tr><td>jet_L_rad</td><td>jet Lum.</td><td>erg / s</td><td>5.828287e+39</td></tr>
    <tr><td>jet_L_kin</td><td>jet Lum.</td><td>erg / s</td><td>8.843972e+44</td></tr>
    <tr><td>jet_L_tot</td><td>jet Lum.</td><td>erg / s</td><td>8.867433e+44</td></tr>
    <tr><td>jet_L_e</td><td>jet Lum.</td><td>erg / s</td><td>2.150896e+41</td></tr>
    <tr><td>jet_L_B</td><td>jet Lum.</td><td>erg / s</td><td>2.340254e+42</td></tr>
    <tr><td>jet_L_p_cold</td><td>jet Lum.</td><td>erg / s</td><td>8.841821e+44</td></tr>
    <tr><td>NH_cold_to_rel_e</td><td>cold_p_to_rel_e_ratio</td><td></td><td>1.000000e+00</td></tr>
    </table><style>table.dataTable {clear: both; width: auto !important; margin: 0 !important;}
    .dataTables_info, .dataTables_length, .dataTables_filter, .dataTables_paginate{
    display: inline-block; margin-right: 1em; }
    .paginate_button { margin-right: 5px; }
    </style>
    <script>
    
    var astropy_sort_num = function(a, b) {
        var a_num = parseFloat(a);
        var b_num = parseFloat(b);
    
        if (isNaN(a_num) && isNaN(b_num))
            return ((a < b) ? -1 : ((a > b) ? 1 : 0));
        else if (!isNaN(a_num) && !isNaN(b_num))
            return ((a_num < b_num) ? -1 : ((a_num > b_num) ? 1 : 0));
        else
            return isNaN(a_num) ? -1 : 1;
    }
    
    require.config({paths: {
        datatables: 'https://cdn.datatables.net/1.10.12/js/jquery.dataTables.min'
    }});
    require(["datatables"], function(){
        console.log("$('#table5172659792-485184').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table5172659792-485184').dataTable({
            order: [],
            pageLength: 100,
            lengthMenu: [[10, 25, 50, 100, 500, 1000, -1], [10, 25, 50, 100, 500, 1000, 'All']],
            pagingType: "full_numbers",
            columnDefs: [{targets: [3], type: "optionalnum"}]
        });
    });
    </script>



As in the case of standard jet model, we can rescale ``L_inj`` according
to the desired energy density of the emitters

.. code:: ipython3

    print(f'pre L_inj val:{my_jet.parameters.L_inj.val}')
    my_jet.set_N_from_U_emitters(1E-3)
    my_jet.eval()
    print(f'post L_inj val:{my_jet.parameters.L_inj.val}')



.. parsed-literal::

    pre L_inj val:5e+38
    post L_inj val:1.3538375276420134e+40


Or according to the desired energy density of the emitters

.. code:: ipython3

    print(f'pre L_inj val:{my_jet.parameters.L_inj.val}')
    my_jet.set_N_from_nuFnu(nu_obs=1E14,nuFnu_obs=1E-12)
    my_jet.eval()
    print(f'post L_inj val:{my_jet.parameters.L_inj.val}')



.. parsed-literal::

    pre L_inj val:1.3538375276420134e+40
    post L_inj val:5.895413484370994e+39


The SED at the equilibrium:

.. code:: ipython3

    my_jet.plot_model()




.. parsed-literal::

    <jetset.plot_sedfit.PlotSED at 0x13450b1a0>




.. image:: Jet_example_leptonic_eq_files/Jet_example_leptonic_eq_19_1.png


You can easily plot both the injected distribution and the equilibrium
one

.. code:: ipython3

    my_jet.eval()
    my_jet.emitters_distribution.plot()
    print(f'gamma for cooling equilibrium: {my_jet.emitters_distribution.gamma_cooling_eq}')



.. parsed-literal::

    gamma for cooling equilibrium: 231979.40174924445



.. image:: Jet_example_leptonic_eq_files/Jet_example_leptonic_eq_21_1.png


.. code:: ipython3

    f=pl_fit(my_jet,x_eq=my_jet.emitters_distribution.gamma_cooling_eq)



.. image:: Jet_example_leptonic_eq_files/Jet_example_leptonic_eq_22_0.png


.. code:: ipython3

    my_jet.parameters.B.val=.5
    my_jet.eval()
    my_jet.emitters_distribution.plot()
    print(f'gamma for cooling equilibrium: {my_jet.emitters_distribution.gamma_cooling_eq}')



.. parsed-literal::

    gamma for cooling equilibrium: 9279.17606996978



.. image:: Jet_example_leptonic_eq_files/Jet_example_leptonic_eq_23_1.png


.. code:: ipython3

    f=pl_fit(my_jet,x_eq=my_jet.emitters_distribution.gamma_cooling_eq)



.. image:: Jet_example_leptonic_eq_files/Jet_example_leptonic_eq_24_0.png


By increasing the escape time, electrons below the injection energy have
time to cool before leaving the radiative region,

.. code:: ipython3

    my_jet.parameters.B.val=.5
    my_jet.parameters.T_esc_e_primaries.val=10
    my_jet.eval()
    p=my_jet.emitters_distribution.plot()
    p.setlim(y_min=1E-15)
    print(f'gamma for cooling equilibrium: {my_jet.emitters_distribution.gamma_cooling_eq}')
    f=pl_fit(my_jet,x_eq=my_jet.emitters_distribution.gamma_cooling_eq,y_lim=-15)



.. parsed-literal::

    gamma for cooling equilibrium: 927.9176069969781



.. image:: Jet_example_leptonic_eq_files/Jet_example_leptonic_eq_26_1.png



.. image:: Jet_example_leptonic_eq_files/Jet_example_leptonic_eq_26_2.png


Computational speed
~~~~~~~~~~~~~~~~~~~

.. code:: ipython3

    %timeit my_jet.eval()



.. parsed-literal::

    13.5 ms ± 38.7 μs per loop (mean ± std. dev. of 7 runs, 100 loops each)


.. code:: ipython3

    j=Jet()
    
    %timeit j.eval()



.. parsed-literal::

    1.55 ms ± 26 μs per loop (mean ± std. dev. of 7 runs, 1,000 loops each)


The overhead time for equilibrium computation, compared to a standard
leptonic model, is a factor ~ 8, but still capable of ~ 80 evaluations
per second (Apple Silicon M4 pro with C threads).

Setting up the model for EC
---------------------------

.. code:: ipython3

    from jetset.jet_model import Jet
    from jetset.jet_emitters_factory import InjEmittersFactory
    
    q_inj = InjEmittersFactory().create_inj_emitters('pl')
    
    my_jet=Jet(emitters_distribution=q_inj)
    my_jet.parameters.p.val=2.2
    my_jet.parameters.z_cosm.val=3.080000e-02
    my_jet.parameters.B.val=.3
    my_jet.parameters.p.val=2.3
    
    my_jet.parameters.L_inj.val=1E40
    my_jet.parameters.gmin.val=1
    my_jet.parameters.gmax.val=3E4
    
    my_jet.parameters.beam_obj.val=25


.. code:: ipython3

    my_jet.add_EC_component(['EC_DT','EC_BLR'],disk_type='MultiBB')
    my_jet.set_EC_dependencies()
    my_jet.make_conical_jet()
    my_jet.parameters.R_H.val=1E18


.. parsed-literal::

    adding par: L_Disk to  R_BLR_in
    ==> par R_BLR_in is depending on ['L_Disk'] according to expr:   R_BLR_in =
    3E17*(L_Disk/1E46)**0.5
    adding par: R_BLR_in to  R_BLR_out
    ==> par R_BLR_out is depending on ['R_BLR_in'] according to expr:   R_BLR_out =
    R_BLR_in*1.1
    adding par: L_Disk to  R_DT
    ==> par R_DT is depending on ['L_Disk'] according to expr:   R_DT =
    2E19*(L_Disk/1E46)**0.5
    adding par: R_H to  R
    adding par: theta_open to  R
    ==> par R is depending on ['R_H', 'theta_open'] according to expr:   R =
    np.tan(np.radians(theta_open))*R_H
    setting R_H to 5.715026151380672e+16


.. code:: ipython3

    my_jet.parameters



.. raw:: html

    <i>Table length=23</i>
    <table id="table5196582112-377833" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>par type</th><th>units</th><th>val</th><th>phys. bound. min</th><th>phys. bound. max</th><th>log</th><th>frozen</th></tr></thead>
    <tr><td>jet_leptonic</td><td>*R(D,theta_open)</td><td>region_size</td><td>cm</td><td>8.748866e+16</td><td>1.000000e+03</td><td>1.000000e+30</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>R_H(M)</td><td>region_position</td><td>cm</td><td>1.000000e+18</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>magnetic_field</td><td>gauss</td><td>3.000000e-01</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>cold_p_to_rel_e_ratio</td><td></td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>beaming</td><td></td><td>2.500000e+01</td><td>1.000000e-04</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>redshift</td><td></td><td>3.080000e-02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmin</td><td>low-energy-cut-off</td><td>lorentz-factor*</td><td>1.000000e+00</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>high-energy-cut-off</td><td>lorentz-factor*</td><td>3.000000e+04</td><td>1.000000e+00</td><td>1.000000e+15</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>p</td><td>LE_spectral_slope</td><td></td><td>2.300000e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>T_esc_e_primaries</td><td>escape_time</td><td>R / c</td><td>1.000000e+00</td><td>1.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>L_inj</td><td>L_inj</td><td>erg / s</td><td>1.000000e+40</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>T_DT</td><td>DT</td><td>K</td><td>1.000000e+02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>*R_DT(D,L_Disk)</td><td>DT</td><td>cm</td><td>6.324555e+18</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>tau_DT</td><td>DT</td><td></td><td>1.000000e-01</td><td>0.000000e+00</td><td>1.000000e+00</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R_inner_Sw</td><td>Disk</td><td>Sw. radii*</td><td>3.000000e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R_ext_Sw</td><td>Disk</td><td>Sw. radii*</td><td>5.000000e+02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>accr_eff</td><td>Disk</td><td></td><td>8.000000e-02</td><td>6.000000e-02</td><td>1.000000e-01</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>M_BH</td><td>Disk</td><td>M_sun*</td><td>1.000000e+09</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>tau_BLR</td><td>BLR</td><td></td><td>1.000000e-01</td><td>0.000000e+00</td><td>1.000000e+00</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>*R_BLR_in(D,L_Disk)</td><td>BLR</td><td>cm</td><td>9.486833e+16</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>*R_BLR_out(D,R_BLR_in)</td><td>BLR</td><td>cm</td><td>1.043552e+17</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>L_Disk(M)</td><td>Disk</td><td>erg / s</td><td>1.000000e+45</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>theta_open(M)</td><td>user_defined</td><td>deg</td><td>5.000000e+00</td><td>1.000000e+00</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
    </table><style>table.dataTable {clear: both; width: auto !important; margin: 0 !important;}
    .dataTables_info, .dataTables_length, .dataTables_filter, .dataTables_paginate{
    display: inline-block; margin-right: 1em; }
    .paginate_button { margin-right: 5px; }
    </style>
    <script>
    
    var astropy_sort_num = function(a, b) {
        var a_num = parseFloat(a);
        var b_num = parseFloat(b);
    
        if (isNaN(a_num) && isNaN(b_num))
            return ((a < b) ? -1 : ((a > b) ? 1 : 0));
        else if (!isNaN(a_num) && !isNaN(b_num))
            return ((a_num < b_num) ? -1 : ((a_num > b_num) ? 1 : 0));
        else
            return isNaN(a_num) ? -1 : 1;
    }
    
    require.config({paths: {
        datatables: 'https://cdn.datatables.net/1.10.12/js/jquery.dataTables.min'
    }});
    require(["datatables"], function(){
        console.log("$('#table5196582112-377833').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table5196582112-377833').dataTable({
            order: [],
            pageLength: 100,
            lengthMenu: [[10, 25, 50, 100, 500, 1000, -1], [10, 25, 50, 100, 500, 1000, 'All']],
            pagingType: "full_numbers",
            columnDefs: [{targets: [4, 5, 6], type: "optionalnum"}]
        });
    });
    </script>





.. parsed-literal::

    None



.. code:: ipython3

    my_jet.eval()
    p=my_jet.emitters_distribution.plot()
    p.setlim(y_min=1E-15)
    print(f'gamma for cooling equilibrium: {my_jet.emitters_distribution.gamma_cooling_eq}')
    f=pl_fit(my_jet,x_eq=my_jet.emitters_distribution.gamma_cooling_eq)


.. parsed-literal::

    gamma for cooling equilibrium: 869.8102277115541



.. image:: Jet_example_leptonic_eq_files/Jet_example_leptonic_eq_35_1.png



.. image:: Jet_example_leptonic_eq_files/Jet_example_leptonic_eq_35_2.png


.. code:: ipython3

    my_jet.plot_model()




.. parsed-literal::

    <jetset.plot_sedfit.PlotSED at 0x144041d60>




.. image:: Jet_example_leptonic_eq_files/Jet_example_leptonic_eq_36_1.png


.. code:: ipython3

    from jetset.jet_model import Jet
    from jetset.jet_emitters_factory import InjEmittersFactory
    
    q_inj = InjEmittersFactory().create_inj_emitters('pl')
    q_inj.parameters.gmax.val=3E4
    my_jet=Jet(emitters_distribution=q_inj)
    my_jet.parameters.p.val=2.2
    my_jet.parameters.z_cosm.val=3.080000e-02
    my_jet.parameters.B.val=.3
    my_jet.parameters.p.val=2.3
    
    my_jet.parameters.L_inj.val=1E40
    my_jet.parameters.gmin.val=1
    my_jet.parameters.gmax.val=3E4
    
    my_jet.parameters.beam_obj.val=25
    
    
    my_jet.make_conical_jet()
    my_jet.parameters.R_H.val=1E18


.. parsed-literal::

    adding par: R_H to  R
    adding par: theta_open to  R
    ==> par R is depending on ['R_H', 'theta_open'] according to expr:   R =
    np.tan(np.radians(theta_open))*R_H
    setting R_H to 5.715026151380672e+16


.. code:: ipython3

    my_jet.eval()
    p=my_jet.emitters_distribution.plot()
    f=pl_fit(my_jet,x_eq=my_jet.emitters_distribution.gamma_cooling_eq)
    print(f'gamma for cooling equilibrium: {my_jet.emitters_distribution.gamma_cooling_eq}')




.. image:: Jet_example_leptonic_eq_files/Jet_example_leptonic_eq_38_0.png



.. image:: Jet_example_leptonic_eq_files/Jet_example_leptonic_eq_38_1.png


.. parsed-literal::

    gamma for cooling equilibrium: 2946.1518835079464


.. code:: ipython3

    my_jet.plot_model()




.. parsed-literal::

    <jetset.plot_sedfit.PlotSED at 0x14463ce60>




.. image:: Jet_example_leptonic_eq_files/Jet_example_leptonic_eq_39_1.png


.. code:: ipython3

    my_jet.save_model('test.pkl')

.. code:: ipython3

    my_jet_new=Jet.load_model('test.pkl')

.. code:: ipython3

    my_jet_new


.. parsed-literal::

    
    --------------------------------------------------------------------------------
    model description: 
    --------------------------------------------------------------------------------
    type: Jet
    name: jet_leptonic  
    geometry: spherical  
    
    electrons distribution:
     type: pl_eq_carrier  
     gamma energy grid size:  201
     gmin grid : 1.000000e+00
     gmax grid : 6.000000e+04
     normalization:  False
     log-values:  False
     ratio of cold protons to relativistic electrons: 1.000000e+00
    
    radiative fields:
     seed photons grid size:  100
     IC emission grid size:  100
     source emissivity lower bound :  1.000000e-120
     spectral components:
       name:Sum, state: on
       name:Sum, hidden: False
       name:Sync, state: self-abs
       name:Sync, hidden: False
       name:SSC, state: on
       name:SSC, hidden: False
    external fields transformation method: blob
    
    SED info:
     nu grid size jetkernel: 1000
     nu size: 500
     nu mix (Hz): 1.000000e+06
     nu max (Hz): 1.000000e+30
    
    flux plot lower bound   :  1.000000e-30
    
    --------------------------------------------------------------------------------



.. raw:: html

    <i>Table length=12</i>
    <table id="table13714841504-291114" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>par type</th><th>units</th><th>val</th><th>phys. bound. min</th><th>phys. bound. max</th><th>log</th><th>frozen</th></tr></thead>
    <tr><td>jet_leptonic</td><td>gmin</td><td>low-energy-cut-off</td><td>lorentz-factor*</td><td>1.000000e+00</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>high-energy-cut-off</td><td>lorentz-factor*</td><td>3.000000e+04</td><td>1.000000e+00</td><td>1.000000e+15</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>p</td><td>LE_spectral_slope</td><td></td><td>2.300000e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>T_esc_e_primaries</td><td>escape_time</td><td>R / c</td><td>1.000000e+00</td><td>1.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>L_inj</td><td>L_inj</td><td>erg / s</td><td>1.000000e+40</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>*R(D,theta_open)</td><td>region_size</td><td>cm</td><td>8.748866e+16</td><td>1.000000e+03</td><td>1.000000e+30</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>R_H(M)</td><td>region_position</td><td>cm</td><td>1.000000e+18</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>magnetic_field</td><td>gauss</td><td>3.000000e-01</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>cold_p_to_rel_e_ratio</td><td></td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>beaming</td><td></td><td>2.500000e+01</td><td>1.000000e-04</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>redshift</td><td></td><td>3.080000e-02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>theta_open(M)</td><td>user_defined</td><td>deg</td><td>5.000000e+00</td><td>1.000000e+00</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
    </table><style>table.dataTable {clear: both; width: auto !important; margin: 0 !important;}
    .dataTables_info, .dataTables_length, .dataTables_filter, .dataTables_paginate{
    display: inline-block; margin-right: 1em; }
    .paginate_button { margin-right: 5px; }
    </style>
    <script>
    
    var astropy_sort_num = function(a, b) {
        var a_num = parseFloat(a);
        var b_num = parseFloat(b);
    
        if (isNaN(a_num) && isNaN(b_num))
            return ((a < b) ? -1 : ((a > b) ? 1 : 0));
        else if (!isNaN(a_num) && !isNaN(b_num))
            return ((a_num < b_num) ? -1 : ((a_num > b_num) ? 1 : 0));
        else
            return isNaN(a_num) ? -1 : 1;
    }
    
    require.config({paths: {
        datatables: 'https://cdn.datatables.net/1.10.12/js/jquery.dataTables.min'
    }});
    require(["datatables"], function(){
        console.log("$('#table13714841504-291114').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table13714841504-291114').dataTable({
            order: [],
            pageLength: 100,
            lengthMenu: [[10, 25, 50, 100, 500, 1000, -1], [10, 25, 50, 100, 500, 1000, 'All']],
            pagingType: "full_numbers",
            columnDefs: [{targets: [4, 5, 6], type: "optionalnum"}]
        });
    });
    </script>



.. parsed-literal::

    --------------------------------------------------------------------------------




.. parsed-literal::

    None



.. code:: ipython3

    my_jet_new.parameters.T_esc_e_primaries.val=2
    my_jet_new.eval()
    my_jet_new.plot_model()
    p=my_jet_new.emitters_distribution.plot()
    f=pl_fit(my_jet_new,x_eq=my_jet.emitters_distribution.gamma_cooling_eq)
    print(f'gamma for cooling equilibrium: {my_jet_new.emitters_distribution.gamma_cooling_eq}')




.. image:: Jet_example_leptonic_eq_files/Jet_example_leptonic_eq_43_0.png



.. image:: Jet_example_leptonic_eq_files/Jet_example_leptonic_eq_43_1.png



.. image:: Jet_example_leptonic_eq_files/Jet_example_leptonic_eq_43_2.png


.. parsed-literal::

    gamma for cooling equilibrium: 1473.0759417539732


