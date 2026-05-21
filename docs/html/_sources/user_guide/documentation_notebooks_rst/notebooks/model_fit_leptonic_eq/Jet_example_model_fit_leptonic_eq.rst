.. _model_fitting_lept_eq_SSC:

Model fitting: Leptonic equilibrium SSC
=======================================

.. code:: ipython3

    import warnings
    warnings.filterwarnings('ignore')
    
    import matplotlib.pylab as plt
    import jetset
    from jetset.test_data_helper import  test_SEDs
    from jetset.data_loader import ObsData,Data
    from jetset.plot_sedfit import PlotSED
    from jetset.test_data_helper import  test_SEDs

.. code:: ipython3

    print(jetset.__version__)


.. parsed-literal::

    1.4.0rc3


.. code:: ipython3

    test_SEDs




.. parsed-literal::

    ['/Users/orion/miniforge3/envs/jetset/lib/python3.12/site-packages/jetset/test_data/SEDs_data/SED_3C345.ecsv',
     '/Users/orion/miniforge3/envs/jetset/lib/python3.12/site-packages/jetset/test_data/SEDs_data/SED_MW_Mrk421_EBL_DEABS.ecsv',
     '/Users/orion/miniforge3/envs/jetset/lib/python3.12/site-packages/jetset/test_data/SEDs_data/SED_MW_Mrk501_EBL_ABS.ecsv',
     '/Users/orion/miniforge3/envs/jetset/lib/python3.12/site-packages/jetset/test_data/SEDs_data/SED_MW_Mrk501_EBL_DEABS.ecsv']



Loading data
------------

see the :ref:`data_format` user guide for further information about loading data 

.. code:: ipython3

    print(test_SEDs[1])
    data=Data.from_file(test_SEDs[1])



.. parsed-literal::

    /Users/orion/miniforge3/envs/jetset/lib/python3.12/site-packages/jetset/test_data/SEDs_data/SED_MW_Mrk421_EBL_DEABS.ecsv


.. code:: ipython3

    %matplotlib inline
    sed_data=ObsData(data_table=data)
    sed_data.group_data(bin_width=0.2)
    
    sed_data.add_systematics(0.1,[10.**6,10.**29])
    p=sed_data.plot_sed()
    #p.setlim(y_min=1E-15,x_min=1E7,x_max=1E29)


.. parsed-literal::

    ================================================================================
    
    ***  binning data  ***
    ---> N bins= 88
    ---> bin_width= 0.2
    ================================================================================
    



.. image:: Jet_example_model_fit_leptonic_eq_files/Jet_example_model_fit_leptonic_eq_8_1.png


.. code:: ipython3

    sed_data.save('Mrk_401.pkl')

phenomenological model constraining
-----------------------------------

see the :ref:`phenom_constr` user guide for further information about phenomenological constraining 

spectral indices
~~~~~~~~~~~~~~~~

.. code:: ipython3

    from jetset.sed_shaper import  SEDShape
    my_shape=SEDShape(sed_data)
    my_shape.eval_indices(minimizer='lsb',silent=True)
    p=my_shape.plot_indices()
    p.setlim(y_min=1E-15,y_max=5E-8)


.. parsed-literal::

    ================================================================================
    
    *** evaluating spectral indices for data ***
    ================================================================================
    



.. image:: Jet_example_model_fit_leptonic_eq_files/Jet_example_model_fit_leptonic_eq_13_1.png


sed shaper
~~~~~~~~~~

.. code:: ipython3

    mm,best_fit=my_shape.sync_fit(check_host_gal_template=False,
                      Ep_start=None,
                      minimizer='lsb',
                      silent=True,
                      fit_range=[10.,21.])


.. parsed-literal::

    ================================================================================
    
    *** Log-Polynomial fitting of the synchrotron component ***
    ---> first blind fit run,  fit range: [10.0, 21.0]
    ---> class:  HSP
    
    
    



.. raw:: html

    <i>Table length=4</i>
    <table id="table5225243344-180810" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>val</th><th>bestfit val</th><th>err +</th><th>err -</th><th>start val</th><th>fit range min</th><th>fit range max</th><th>frozen</th></tr></thead>
    <tr><td>LogCubic</td><td>b</td><td>-1.563747e-01</td><td>-1.563747e-01</td><td>5.975434e-03</td><td>--</td><td>-1.000000e+00</td><td>-1.000000e+01</td><td>0.000000e+00</td><td>False</td></tr>
    <tr><td>LogCubic</td><td>c</td><td>-1.052802e-02</td><td>-1.052802e-02</td><td>8.781942e-04</td><td>--</td><td>-1.000000e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td></tr>
    <tr><td>LogCubic</td><td>Ep</td><td>1.675324e+01</td><td>1.675324e+01</td><td>2.396636e-02</td><td>--</td><td>1.670206e+01</td><td>0.000000e+00</td><td>3.000000e+01</td><td>False</td></tr>
    <tr><td>LogCubic</td><td>Sp</td><td>-9.494365e+00</td><td>-9.494365e+00</td><td>1.704982e-02</td><td>--</td><td>-1.000000e+01</td><td>-3.000000e+01</td><td>0.000000e+00</td><td>False</td></tr>
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
        console.log("$('#table5225243344-180810').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table5225243344-180810').dataTable({
            order: [],
            pageLength: 100,
            lengthMenu: [[10, 25, 50, 100, 500, 1000, -1], [10, 25, 50, 100, 500, 1000, 'All']],
            pagingType: "full_numbers",
            columnDefs: [{targets: [2, 3, 4, 5, 6, 7, 8], type: "optionalnum"}]
        });
    });
    </script>



.. parsed-literal::

    ---> sync       nu_p=+1.675324e+01 (err=+2.396636e-02)  nuFnu_p=-9.494365e+00 (err=+1.704982e-02) curv.=-1.563747e-01 (err=+5.975434e-03)
    ================================================================================
    


.. code:: ipython3

    my_shape.IC_fit(fit_range=[23.,29.],minimizer='minuit',silent=True)
    p=my_shape.plot_shape_fit()
    p.setlim(y_min=1E-15,y_max=5E-8)


.. parsed-literal::

    ================================================================================
    
    *** Log-Polynomial fitting of the IC component ***
    ---> fit range: [23.0, 29.0]
    ---> LogCubic fit
    
    



.. raw:: html

    <i>Table length=4</i>
    <table id="table5228028096-694304" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>val</th><th>bestfit val</th><th>err +</th><th>err -</th><th>start val</th><th>fit range min</th><th>fit range max</th><th>frozen</th></tr></thead>
    <tr><td>LogCubic</td><td>b</td><td>-2.274590e-01</td><td>-2.274590e-01</td><td>3.262166e-02</td><td>--</td><td>-1.000000e+00</td><td>-1.000000e+01</td><td>0.000000e+00</td><td>False</td></tr>
    <tr><td>LogCubic</td><td>c</td><td>-6.259967e-02</td><td>-6.259967e-02</td><td>1.629408e-02</td><td>--</td><td>-1.000000e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td></tr>
    <tr><td>LogCubic</td><td>Ep</td><td>2.527207e+01</td><td>2.527207e+01</td><td>8.149547e-02</td><td>--</td><td>2.528644e+01</td><td>0.000000e+00</td><td>3.000000e+01</td><td>False</td></tr>
    <tr><td>LogCubic</td><td>Sp</td><td>-1.014119e+01</td><td>-1.014119e+01</td><td>2.734754e-02</td><td>--</td><td>-1.000000e+01</td><td>-3.000000e+01</td><td>0.000000e+00</td><td>False</td></tr>
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
        console.log("$('#table5228028096-694304').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table5228028096-694304').dataTable({
            order: [],
            pageLength: 100,
            lengthMenu: [[10, 25, 50, 100, 500, 1000, -1], [10, 25, 50, 100, 500, 1000, 'All']],
            pagingType: "full_numbers",
            columnDefs: [{targets: [2, 3, 4, 5, 6, 7, 8], type: "optionalnum"}]
        });
    });
    </script>



.. parsed-literal::

    ---> IC         nu_p=+2.527207e+01 (err=+8.149547e-02)  nuFnu_p=-1.014119e+01 (err=+2.734754e-02) curv.=-2.274590e-01 (err=+3.262166e-02)
    ================================================================================
    



.. image:: Jet_example_model_fit_leptonic_eq_files/Jet_example_model_fit_leptonic_eq_16_3.png


Model constraining
~~~~~~~~~~~~~~~~~~

In this step we are not fitting the model, we are just obtaining the
phenomenological ``pre_fit`` model, that will be fitted in using minuit
ore least-square bound, as shown below

.. code:: ipython3

    from jetset.obs_constrain import ObsConstrain
    
    
    sed_obspar=ObsConstrain(beaming=25,
                            B_range=[0.001,0.1],
                            t_var_sec=3*86400,
                            nu_cut_IR=1E12,
                            distr_e='bkn',
                            SEDShape=my_shape)
    
    
    prefit_jet=sed_obspar.constrain_SSC_model(electron_distribution_log_values=False,silent=True)
    prefit_jet.save_model('prefit_jet.pkl')


.. parsed-literal::

    ================================================================================
    
    ***  constrains parameters from observable ***
    



.. raw:: html

    <i>Table length=12</i>
    <table id="table5448494560-680184" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>par type</th><th>units</th><th>val</th><th>phys. bound. min</th><th>phys. bound. max</th><th>log</th><th>frozen</th></tr></thead>
    <tr><td>jet_leptonic</td><td>R</td><td>region_size</td><td>cm</td><td>2.219270e+16</td><td>1.000000e+03</td><td>1.000000e+30</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R_H</td><td>region_position</td><td>cm</td><td>1.000000e+17</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>magnetic_field</td><td>gauss</td><td>6.618390e-02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>cold_p_to_rel_e_ratio</td><td></td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>beaming</td><td></td><td>2.500000e+01</td><td>1.000000e-04</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>redshift</td><td></td><td>3.080000e-02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmin</td><td>low-energy-cut-off</td><td>lorentz-factor*</td><td>4.103365e+02</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>high-energy-cut-off</td><td>lorentz-factor*</td><td>1.136207e+06</td><td>1.000000e+00</td><td>1.000000e+15</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>N</td><td>emitters_density</td><td>1 / cm3</td><td>1.238758e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gamma_break</td><td>turn-over-energy</td><td>lorentz-factor*</td><td>9.766963e+04</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>p</td><td>LE_spectral_slope</td><td></td><td>2.169388e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>p_1</td><td>HE_spectral_slope</td><td></td><td>3.500000e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
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
        console.log("$('#table5448494560-680184').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table5448494560-680184').dataTable({
            order: [],
            pageLength: 100,
            lengthMenu: [[10, 25, 50, 100, 500, 1000, -1], [10, 25, 50, 100, 500, 1000, 'All']],
            pagingType: "full_numbers",
            columnDefs: [{targets: [4, 5, 6], type: "optionalnum"}]
        });
    });
    </script>



.. parsed-literal::

    
    ================================================================================
    


.. code:: ipython3

    prefit_jet.eval()
    pl=prefit_jet.plot_model(sed_data=sed_data)
    pl.add_residual_plot(prefit_jet,sed_data)
    pl.setlim(y_min=1E-15,x_min=1E7,x_max=1E29)



.. image:: Jet_example_model_fit_leptonic_eq_files/Jet_example_model_fit_leptonic_eq_20_0.png


Enabling the leptonic equilibrium
---------------------------------

We change the emitters distribution for the ``prefit_jet`` model. We
just preserve the original values of ``gmin`` and ``gmax``

.. code:: ipython3

    from jetset.jet_emitters_factory import InjEmittersFactory
    
    gmin=prefit_jet.parameters.gmin.val
    gmax=prefit_jet.parameters.gmax.val
    
    q_inj = InjEmittersFactory().create_inj_emitters('pl')
    prefit_jet.set_emitters_distribution(q_inj)
    prefit_jet.parameters.gmin.val=gmin
    prefit_jet.parameters.gmax.val=gmax
    


We change the name of the jet model (this is not necessary)

.. code:: ipython3

    prefit_jet.name='leptonic_eq'

We renormalize the injected Luminosity in order to get a flux level
consistent with the data. We can use the usual method
``set_N_from_nuFnu``, it will work also for ``L_inj``

.. code:: ipython3

    prefit_jet.set_N_from_nuFnu(nu_obs=1E15,nuFnu_obs=1E-10)

.. code:: ipython3

    prefit_jet.eval()
    prefit_jet.plot_model(sed_data=sed_data)




.. parsed-literal::

    <jetset.plot_sedfit.PlotSED at 0x137744a10>




.. image:: Jet_example_model_fit_leptonic_eq_files/Jet_example_model_fit_leptonic_eq_28_1.png


.. code:: ipython3

    prefit_jet.emitters_distribution.plot()




.. parsed-literal::

    <jetset.plot_sedfit.PlotPdistr at 0x155251370>




.. image:: Jet_example_model_fit_leptonic_eq_files/Jet_example_model_fit_leptonic_eq_29_1.png


.. code:: ipython3

    prefit_jet.parameters



.. raw:: html

    <i>Table length=11</i>
    <table id="table5448494560-675168" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>par type</th><th>units</th><th>val</th><th>phys. bound. min</th><th>phys. bound. max</th><th>log</th><th>frozen</th></tr></thead>
    <tr><td>leptonic_eq</td><td>R</td><td>region_size</td><td>cm</td><td>2.219270e+16</td><td>1.000000e+03</td><td>1.000000e+30</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>R_H</td><td>region_position</td><td>cm</td><td>1.000000e+17</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>B</td><td>magnetic_field</td><td>gauss</td><td>6.618390e-02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>NH_cold_to_rel_e</td><td>cold_p_to_rel_e_ratio</td><td></td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>beam_obj</td><td>beaming</td><td></td><td>2.500000e+01</td><td>1.000000e-04</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>z_cosm</td><td>redshift</td><td></td><td>3.080000e-02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>gmin</td><td>low-energy-cut-off</td><td>lorentz-factor*</td><td>4.103365e+02</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>gmax</td><td>high-energy-cut-off</td><td>lorentz-factor*</td><td>1.136207e+06</td><td>1.000000e+00</td><td>1.000000e+15</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>p</td><td>LE_spectral_slope</td><td></td><td>2.000000e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>T_esc_e_primaries</td><td>escape_time</td><td>R / c</td><td>1.000000e+00</td><td>1.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>L_inj</td><td>L_inj</td><td>erg / s</td><td>1.323503e+41</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
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
        console.log("$('#table5448494560-675168').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table5448494560-675168').dataTable({
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



Model fitting procedure
-----------------------

.. note::
    Please, read the introduction and the caveats :ref:`for the frequentist model fitting <frequentist_model_fitting>`: to understand the frequentist fitting workflow
    see the :ref:`composite_models` user guide for further information about the implementation of :class:`.FitModel`, in particular for parameter setting

Model fitting with Minuit
~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: ipython3

    from jetset.minimizer import ModelMinimizer
    
    from jetset.model_manager import  FitModel


We use the ``prefit_jet`` model to build the ``FitModel``

.. code:: ipython3

    fit_model=FitModel( jet=prefit_jet, name='SSC-best-fit',template=None) 

.. code:: ipython3

    fit_model.show_model_components()


.. parsed-literal::

    
    --------------------------------------------------------------------------------
    Composite model description
    --------------------------------------------------------------------------------
    name: SSC-best-fit  
    type: composite_model  
    components models:
     -model name: leptonic_eq model type: jet
    
    --------------------------------------------------------------------------------


There is only one component, whit name ``leptonic_eq``, that refers to
the ``prefit_jet`` model component (renamed to ``leptonic_eq``).

We now set the gamma grid size to 200, ad we set ``composite_expr``,
anyhow, since we have only one component this step could be skipped

.. code:: ipython3

    fit_model.leptonic_eq.set_gamma_grid_size(200)
    fit_model.composite_expr='leptonic_eq'

Freezeing parameters and setting fit_range intervals
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: ipython3

    
    fit_model.freeze('leptonic_eq','z_cosm')
    fit_model.freeze('leptonic_eq','R_H')
    fit_model.freeze('leptonic_eq','T_esc_e_primaries')
    
    fit_model.leptonic_eq.parameters.R.fit_range=[10**15.5,10**17.5]
    fit_model.leptonic_eq.parameters.beam_obj.fit_range=[5., 50.]
    fit_model.leptonic_eq.parameters.gmax.fit_range=[1E5, 5E6]
    fit_model.leptonic_eq.parameters.gmin.fit_range=[10, 1000]
    fit_model.leptonic_eq.parameters.T_esc_e_primaries.val=1
    fit_model.leptonic_eq.parameters.L_inj.fit_range=[1E38, 1E43]
    


.. code:: ipython3

    fit_model.parameters



.. raw:: html

    <i>Table length=11</i>
    <table id="table5724691520-657806" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>par type</th><th>units</th><th>val</th><th>phys. bound. min</th><th>phys. bound. max</th><th>log</th><th>frozen</th></tr></thead>
    <tr><td>leptonic_eq</td><td>R</td><td>region_size</td><td>cm</td><td>2.219270e+16</td><td>1.000000e+03</td><td>1.000000e+30</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>R_H</td><td>region_position</td><td>cm</td><td>1.000000e+17</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>B</td><td>magnetic_field</td><td>gauss</td><td>6.618390e-02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>NH_cold_to_rel_e</td><td>cold_p_to_rel_e_ratio</td><td></td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>beam_obj</td><td>beaming</td><td></td><td>2.500000e+01</td><td>1.000000e-04</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>z_cosm</td><td>redshift</td><td></td><td>3.080000e-02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>gmin</td><td>low-energy-cut-off</td><td>lorentz-factor*</td><td>4.103365e+02</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>gmax</td><td>high-energy-cut-off</td><td>lorentz-factor*</td><td>1.136207e+06</td><td>1.000000e+00</td><td>1.000000e+15</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>p</td><td>LE_spectral_slope</td><td></td><td>2.000000e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>T_esc_e_primaries</td><td>escape_time</td><td>R / c</td><td>1.000000e+00</td><td>1.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>L_inj</td><td>L_inj</td><td>erg / s</td><td>1.323503e+41</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
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
        console.log("$('#table5724691520-657806').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table5724691520-657806').dataTable({
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



Building the ModelMinimizer object
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: ipython3

    model_minimizer_minuit=ModelMinimizer('minuit')
    best_fit_minuit=model_minimizer_minuit.fit(fit_model,
                                               sed_data,
                                               1E11,
                                               1E29,
                                               fitname='SSC-best-fit-minuit',
                                               max_ev=10000,
                                               repeat=1)


.. parsed-literal::

    filtering data in fit range = [1.000000e+11,1.000000e+29]
    data length 34
    ================================================================================
    
    *** start fit process ***
    ----- 



.. parsed-literal::

    0it [00:00, ?it/s]


.. parsed-literal::

    - best chisq=4.02541e+01
    
    -------------------------------------------------------------------------
    Fit report
    
    Model: SSC-best-fit-minuit



.. raw:: html

    <i>Table length=11</i>
    <table id="table5724691520-182218" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>par type</th><th>units</th><th>val</th><th>phys. bound. min</th><th>phys. bound. max</th><th>log</th><th>frozen</th></tr></thead>
    <tr><td>leptonic_eq</td><td>R</td><td>region_size</td><td>cm</td><td>2.293605e+16</td><td>1.000000e+03</td><td>1.000000e+30</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>R_H</td><td>region_position</td><td>cm</td><td>1.000000e+17</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>B</td><td>magnetic_field</td><td>gauss</td><td>9.999970e-02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>NH_cold_to_rel_e</td><td>cold_p_to_rel_e_ratio</td><td></td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>beam_obj</td><td>beaming</td><td></td><td>1.988105e+01</td><td>1.000000e-04</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>z_cosm</td><td>redshift</td><td></td><td>3.080000e-02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>gmin</td><td>low-energy-cut-off</td><td>lorentz-factor*</td><td>4.820682e+02</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>gmax</td><td>high-energy-cut-off</td><td>lorentz-factor*</td><td>7.989524e+05</td><td>1.000000e+00</td><td>1.000000e+15</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>p</td><td>LE_spectral_slope</td><td></td><td>2.099530e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>T_esc_e_primaries</td><td>escape_time</td><td>R / c</td><td>1.000000e+00</td><td>1.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>L_inj</td><td>L_inj</td><td>erg / s</td><td>1.594854e+41</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
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
        console.log("$('#table5724691520-182218').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table5724691520-182218').dataTable({
            order: [],
            pageLength: 100,
            lengthMenu: [[10, 25, 50, 100, 500, 1000, -1], [10, 25, 50, 100, 500, 1000, 'All']],
            pagingType: "full_numbers",
            columnDefs: [{targets: [4, 5, 6], type: "optionalnum"}]
        });
    });
    </script>



.. parsed-literal::

    
    converged=True
    calls=3001
    mesg=



.. raw:: html

    <table>
        <tr>
            <th colspan="2" style="text-align:center" title="Minimizer"> Migrad </th>
        </tr>
        <tr>
            <td style="text-align:left" title="Minimum value of function"> FCN = 40.25 </td>
            <td style="text-align:center" title="Total number of function and (optional) gradient evaluations"> Nfcn = 3001 </td>
        </tr>
        <tr>
            <td style="text-align:left" title="Estimated distance to minimum and goal"> EDM = 28.2 (Goal: 0.0002) </td>
            <td style="text-align:center" title="Total run time of algorithms"> time = 23.3 sec </td>
        </tr>
        <tr>
            <td style="text-align:center;background-color:#c15ef7;color:black"> INVALID Minimum </td>
            <td style="text-align:center;background-color:#c15ef7;color:black"> ABOVE EDM threshold (goal x 10) </td>
        </tr>
        <tr>
            <td style="text-align:center;background-color:#92CCA6;color:black"> No parameters at limit </td>
            <td style="text-align:center;background-color:#92CCA6;color:black"> Below call limit </td>
        </tr>
        <tr>
            <td style="text-align:center;background-color:#FFF79A;color:black"> Hesse ok </td>
            <td style="text-align:center;background-color:#FFF79A;color:black"> Covariance APPROXIMATE </td>
        </tr>
    </table><table>
        <tr>
            <td></td>
            <th title="Variable name"> Name </th>
            <th title="Value of parameter"> Value </th>
            <th title="Hesse error"> Hesse Error </th>
            <th title="Minos lower error"> Minos Error- </th>
            <th title="Minos upper error"> Minos Error+ </th>
            <th title="Lower limit of the parameter"> Limit- </th>
            <th title="Upper limit of the parameter"> Limit+ </th>
            <th title="Is the parameter fixed in the fit"> Fixed </th>
        </tr>
        <tr>
            <th> 0 </th>
            <td> par_0 </td>
            <td> 22.9e15 </td>
            <td> 1.9e15 </td>
            <td>  </td>
            <td>  </td>
            <td> 3.16E+15 </td>
            <td> 3.16E+17 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 1 </th>
            <td> par_1 </td>
            <td> 0.100 </td>
            <td> 0.005 </td>
            <td>  </td>
            <td>  </td>
            <td> 0 </td>
            <td>  </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 2 </th>
            <td> par_2 </td>
            <td> 19.9 </td>
            <td> 0.4 </td>
            <td>  </td>
            <td>  </td>
            <td> 5 </td>
            <td> 50 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 3 </th>
            <td> par_3 </td>
            <td> 482.0682 </td>
            <td> 0.0009 </td>
            <td>  </td>
            <td>  </td>
            <td> 10 </td>
            <td> 1E+03 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 4 </th>
            <td> par_4 </td>
            <td> 0.799e6 </td>
            <td> 0.020e6 </td>
            <td>  </td>
            <td>  </td>
            <td> 1E+05 </td>
            <td> 5E+06 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 5 </th>
            <td> par_5 </td>
            <td> 2.100 </td>
            <td> 0.013 </td>
            <td>  </td>
            <td>  </td>
            <td> -10 </td>
            <td> 10 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 6 </th>
            <td> par_6 </td>
            <td> 0.159e42 </td>
            <td> 0.010e42 </td>
            <td>  </td>
            <td>  </td>
            <td> 1E+38 </td>
            <td> 1E+43 </td>
            <td>  </td>
        </tr>
    </table>


.. parsed-literal::

    dof=27
    chisq=40.254144, chisq/red=1.490894 null hypothesis sig=0.048498
    
    best fit pars



.. raw:: html

    <i>Table length=11</i>
    <table id="table5224293168-59176" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>val</th><th>bestfit val</th><th>err +</th><th>err -</th><th>start val</th><th>fit range min</th><th>fit range max</th><th>frozen</th></tr></thead>
    <tr><td>leptonic_eq</td><td>R</td><td>2.293605e+16</td><td>2.293605e+16</td><td>1.877852e+15</td><td>--</td><td>2.219270e+16</td><td>3.162278e+15</td><td>3.162278e+17</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>R_H</td><td>1.000000e+17</td><td>--</td><td>--</td><td>--</td><td>1.000000e+17</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>B</td><td>9.999970e-02</td><td>9.999970e-02</td><td>4.621741e-03</td><td>--</td><td>6.618390e-02</td><td>0.000000e+00</td><td>--</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>NH_cold_to_rel_e</td><td>1.000000e+00</td><td>--</td><td>--</td><td>--</td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>beam_obj</td><td>1.988105e+01</td><td>1.988105e+01</td><td>4.479375e-01</td><td>--</td><td>2.500000e+01</td><td>5.000000e+00</td><td>5.000000e+01</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>z_cosm</td><td>3.080000e-02</td><td>--</td><td>--</td><td>--</td><td>3.080000e-02</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>gmin</td><td>4.820682e+02</td><td>4.820682e+02</td><td>8.911954e-04</td><td>--</td><td>4.103365e+02</td><td>1.000000e+01</td><td>1.000000e+03</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>gmax</td><td>7.989524e+05</td><td>7.989524e+05</td><td>2.016861e+04</td><td>--</td><td>1.136207e+06</td><td>1.000000e+05</td><td>5.000000e+06</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>p</td><td>2.099530e+00</td><td>2.099530e+00</td><td>1.336951e-02</td><td>--</td><td>2.000000e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>T_esc_e_primaries</td><td>1.000000e+00</td><td>--</td><td>--</td><td>--</td><td>1.000000e+00</td><td>1.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>L_inj</td><td>1.594854e+41</td><td>1.594854e+41</td><td>1.050039e+40</td><td>--</td><td>1.323503e+41</td><td>1.000000e+38</td><td>1.000000e+43</td><td>False</td></tr>
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
        console.log("$('#table5224293168-59176').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table5224293168-59176').dataTable({
            order: [],
            pageLength: 100,
            lengthMenu: [[10, 25, 50, 100, 500, 1000, -1], [10, 25, 50, 100, 500, 1000, 'All']],
            pagingType: "full_numbers",
            columnDefs: [{targets: [2, 3, 4, 5, 6, 7, 8], type: "optionalnum"}]
        });
    });
    </script>



.. parsed-literal::

    -------------------------------------------------------------------------
    
    ================================================================================
    


.. code:: ipython3

    p=model_minimizer_minuit.plot_corr_matrix()



.. image:: Jet_example_model_fit_leptonic_eq_files/Jet_example_model_fit_leptonic_eq_46_0.png


.. code:: ipython3

    %matplotlib inline
    fit_model.eval()
    p2=fit_model.plot_model(sed_data=sed_data)
    p2.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_example_model_fit_leptonic_eq_files/Jet_example_model_fit_leptonic_eq_47_0.png


.. code:: ipython3

    fit_model.leptonic_eq.emitters_distribution.plot()





.. parsed-literal::

    <jetset.plot_sedfit.PlotPdistr at 0x15518d370>




.. image:: Jet_example_model_fit_leptonic_eq_files/Jet_example_model_fit_leptonic_eq_48_1.png


saving fit model, model minimizer
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: ipython3

    best_fit_minuit.save_report('SSC-best-fit-minuit.pkl')
    model_minimizer_minuit.save_model('model_minimizer_minuit.pkl')
    fit_model.save_model('fit_model_minuit.pkl')

MCMC sampling
-------------

.. note::
    Please, read the introduction and the caveats :ref:`for the Bayesian model fitting <bayesian_model_fitting>` to understand the MCMC sampler workflow.


creating and setting the sampler
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: ipython3

    from jetset.data_loader import ObsData
    
    sed_data=ObsData.load('Mrk_401.pkl')

.. code:: ipython3

    from jetset.mcmc import McmcSampler
    from jetset.minimizer import ModelMinimizer


.. code:: ipython3

    model_minimizer = ModelMinimizer.load_model('model_minimizer_minuit.pkl')
    
    mcmc=McmcSampler(model_minimizer)


.. note::

   By default, the MCMC sampler will inherit ``nu_fit_start``, ``nu_fit_stop``,
   and ``use_UL`` from the ``model_minimizer`` object. To change these values,
   please read  the introduction and the caveats :ref:`for the Bayesian model fitting <bayesian_model_fitting>`

.. important::
   Starting from v1.4.0, the :class:`.McmcSampler` class does not use anymore ``labels`` and ``use_labels_dict``, to select the parameters used by the sampler. Starting from this version, all the ``free`` parameters will be sampled. To select sampled parameters, please ``free/freeze`` them.


You can inspect the mcmc parameters using:

.. code:: ipython3

    mcmc.sampler_parameters




.. raw:: html

    <div><i>Table length=11</i>
    <table id="table13683443712" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>val</th><th>log</th><th>bestfit mcmc</th><th>q16</th><th>q50</th><th>q84</th><th>mcmc bound min</th><th>mcmc bound max</th><th>frozen</th></tr></thead>
    <thead><tr><th>str11</th><th>str17</th><th>float64</th><th>bool</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>bool</th></tr></thead>
    <tr><td>leptonic_eq</td><td>gmin</td><td>4.820682e+02</td><td>False</td><td>4.820682e+02</td><td>--</td><td>--</td><td>--</td><td>1.000000e+01</td><td>1.000000e+03</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>gmax</td><td>7.989524e+05</td><td>False</td><td>7.989524e+05</td><td>--</td><td>--</td><td>--</td><td>1.000000e+05</td><td>5.000000e+06</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>p</td><td>2.099530e+00</td><td>False</td><td>2.099530e+00</td><td>--</td><td>--</td><td>--</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>L_inj</td><td>1.594854e+41</td><td>False</td><td>1.594854e+41</td><td>--</td><td>--</td><td>--</td><td>1.000000e+38</td><td>1.000000e+43</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>R</td><td>2.293605e+16</td><td>False</td><td>2.293605e+16</td><td>--</td><td>--</td><td>--</td><td>3.162278e+15</td><td>3.162278e+17</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>B</td><td>9.999970e-02</td><td>False</td><td>9.999970e-02</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>beam_obj</td><td>1.988105e+01</td><td>False</td><td>1.988105e+01</td><td>--</td><td>--</td><td>--</td><td>5.000000e+00</td><td>5.000000e+01</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>T_esc_e_primaries</td><td>1.000000e+00</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>1.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>R_H</td><td>1.000000e+17</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>NH_cold_to_rel_e</td><td>1.000000e+00</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>z_cosm</td><td>3.080000e-02</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    </table></div>



If you want to exclude or include parameters from the samplers just
freeze/free them

.. code:: ipython3

    mcmc.model.leptonic_eq.parameters.T_esc_e_primaries.frozen=True


You can set different bounds for all the free parameters with the
instruction below. In case you want to preserve the ``fit_range``
defined in the frequentist minimizer you can pass
``preserve_fit_range=True``

.. note::
    Please, read the introduction and the caveats :ref:`for the Bayesian model fitting <bayesian_model_fitting>` to understand better the bound settings.

.. code:: ipython3

    mcmc.set_bounds(bound=5.0,bound_rel=True)


.. parsed-literal::

    par: gmin  ref value:  482.06815780972534  mcmc bounds: [10, 1000]
    par: gmax  ref value:  798952.4062170115  mcmc bounds: [100000.0, np.float64(4793714.437302069)]
    par: p  ref value:  2.099529853039601  mcmc bounds: [np.float64(-8.398119412158405), 10]
    par: L_inj  ref value:  1.5948541733480243e+41  mcmc bounds: [1e+38, np.float64(9.569125040088146e+41)]
    par: R  ref value:  2.29360478053214e+16  mcmc bounds: [3162277660168379.5, np.float64(1.3761628683192842e+17)]
    par: B  ref value:  0.0999996999824615  mcmc bounds: [0, np.float64(0.599998199894769)]
    par: beam_obj  ref value:  19.881048512008306  mcmc bounds: [5.0, 50.0]


Or, you can set per-parameter bounds as follows, passing the actual
bound for each parameter.

.. code:: ipython3

    mcmc.set_bounds(comp_name='leptonic_eq',par_name='L_inj',par_bounds=[1E37,1E43])
    mcmc.set_bounds(comp_name='leptonic_eq',par_name='gmin',par_bounds=[10,2000])
    



.. parsed-literal::

    par: L_inj  ref value:  1.5948541733480243e+41  mcmc bounds: [1e+38, 1e+43]
    par: gmin  ref value:  482.06815780972534  mcmc bounds: [10, 1000]


.. code:: ipython3

    mcmc.sampler_parameters




.. raw:: html

    <div><i>Table length=11</i>
    <table id="table13695307168" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>val</th><th>log</th><th>bestfit mcmc</th><th>q16</th><th>q50</th><th>q84</th><th>mcmc bound min</th><th>mcmc bound max</th><th>frozen</th></tr></thead>
    <thead><tr><th>str11</th><th>str17</th><th>float64</th><th>bool</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>bool</th></tr></thead>
    <tr><td>leptonic_eq</td><td>gmin</td><td>4.820682e+02</td><td>False</td><td>4.820682e+02</td><td>--</td><td>--</td><td>--</td><td>1.000000e+01</td><td>1.000000e+03</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>gmax</td><td>7.989524e+05</td><td>False</td><td>7.989524e+05</td><td>--</td><td>--</td><td>--</td><td>1.000000e+05</td><td>4.793714e+06</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>p</td><td>2.099530e+00</td><td>False</td><td>2.099530e+00</td><td>--</td><td>--</td><td>--</td><td>-8.398119e+00</td><td>1.000000e+01</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>L_inj</td><td>1.594854e+41</td><td>False</td><td>1.594854e+41</td><td>--</td><td>--</td><td>--</td><td>1.000000e+38</td><td>1.000000e+43</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>R</td><td>2.293605e+16</td><td>False</td><td>2.293605e+16</td><td>--</td><td>--</td><td>--</td><td>3.162278e+15</td><td>1.376163e+17</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>B</td><td>9.999970e-02</td><td>False</td><td>9.999970e-02</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>5.999982e-01</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>beam_obj</td><td>1.988105e+01</td><td>False</td><td>1.988105e+01</td><td>--</td><td>--</td><td>--</td><td>5.000000e+00</td><td>5.000000e+01</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>T_esc_e_primaries</td><td>1.000000e+00</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>1.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>R_H</td><td>1.000000e+17</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>NH_cold_to_rel_e</td><td>1.000000e+00</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>z_cosm</td><td>3.080000e-02</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    </table></div>



.. code:: ipython3

    mcmc.run_sampler(nwalkers=30, burnin=50,steps=500,progress='notebook')



.. parsed-literal::

    mcmc run starting
    



.. parsed-literal::

      0%|          | 0/500 [00:00<?, ?it/s]


.. parsed-literal::

    mcmc run done, with 1 threads took 121.55 seconds
    ----------------------------
    MCMC best fit solution
    comp: leptonic_eq par: gmin  mcmc best fit val: 938.06939050328 quantiles(0.16,0.5,0.84): [549.87841228 738.1979352  873.30583605] 
    comp: leptonic_eq par: gmax  mcmc best fit val: 1096972.3818084246 quantiles(0.16,0.5,0.84): [ 825321.65909323  955360.3692434  1102716.46439814] 
    comp: leptonic_eq par: p  mcmc best fit val: 2.239008026417144 quantiles(0.16,0.5,0.84): [2.10338086 2.17333251 2.25380331] 
    comp: leptonic_eq par: L_inj  mcmc best fit val: 2.0305541578887544e+41 quantiles(0.16,0.5,0.84): [1.44445183e+41 1.78017869e+41 2.40955195e+41] 
    comp: leptonic_eq par: R  mcmc best fit val: 3.796263778809784e+16 quantiles(0.16,0.5,0.84): [2.30660820e+16 2.98273288e+16 4.29937507e+16] 
    comp: leptonic_eq par: B  mcmc best fit val: 0.05165970111094412 quantiles(0.16,0.5,0.84): [0.05231904 0.06454995 0.09123121] 
    comp: leptonic_eq par: beam_obj  mcmc best fit val: 21.28267416705294 quantiles(0.16,0.5,0.84): [18.5137355  20.43306339 23.1641905 ] 
    ----------------------------


Showing the MCMC parameters. Now MCMC bestfit values are updated to the
best-fit MCMC solution

.. code:: ipython3

    mcmc.sampler_parameters




.. raw:: html

    <div><i>Table length=11</i>
    <table id="table13695151728" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>val</th><th>log</th><th>bestfit mcmc</th><th>q16</th><th>q50</th><th>q84</th><th>mcmc bound min</th><th>mcmc bound max</th><th>frozen</th></tr></thead>
    <thead><tr><th>str11</th><th>str17</th><th>float64</th><th>bool</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>bool</th></tr></thead>
    <tr><td>leptonic_eq</td><td>gmin</td><td>9.380694e+02</td><td>False</td><td>9.380694e+02</td><td>5.498784e+02</td><td>7.381979e+02</td><td>8.733058e+02</td><td>1.000000e+01</td><td>1.000000e+03</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>gmax</td><td>1.096972e+06</td><td>False</td><td>1.096972e+06</td><td>8.253217e+05</td><td>9.553604e+05</td><td>1.102716e+06</td><td>1.000000e+05</td><td>4.793714e+06</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>p</td><td>2.239008e+00</td><td>False</td><td>2.239008e+00</td><td>2.103381e+00</td><td>2.173333e+00</td><td>2.253803e+00</td><td>-8.398119e+00</td><td>1.000000e+01</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>L_inj</td><td>2.030554e+41</td><td>False</td><td>2.030554e+41</td><td>1.444452e+41</td><td>1.780179e+41</td><td>2.409552e+41</td><td>1.000000e+38</td><td>1.000000e+43</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>R</td><td>3.796264e+16</td><td>False</td><td>3.796264e+16</td><td>2.306608e+16</td><td>2.982733e+16</td><td>4.299375e+16</td><td>3.162278e+15</td><td>1.376163e+17</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>B</td><td>5.165970e-02</td><td>False</td><td>5.165970e-02</td><td>5.231904e-02</td><td>6.454995e-02</td><td>9.123121e-02</td><td>0.000000e+00</td><td>5.999982e-01</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>beam_obj</td><td>2.128267e+01</td><td>False</td><td>2.128267e+01</td><td>1.851374e+01</td><td>2.043306e+01</td><td>2.316419e+01</td><td>5.000000e+00</td><td>5.000000e+01</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>T_esc_e_primaries</td><td>1.000000e+00</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>1.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>R_H</td><td>1.000000e+17</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>NH_cold_to_rel_e</td><td>1.000000e+00</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>z_cosm</td><td>3.080000e-02</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    </table></div>



post-run tuning of the burnin
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

We plot the chains, before and after, tuning the burnin (using
autocorrelation time of emcee).

.. code:: ipython3

    p=mcmc.plot_chain()



.. image:: Jet_example_model_fit_leptonic_eq_files/Jet_example_model_fit_leptonic_eq_73_0.png


.. code:: ipython3

    mcmc.tune_burnin(tau_coeff=2)
    p=mcmc.plot_chain()


.. parsed-literal::

    ----------------------------
    MCMC best fit solution
    comp: leptonic_eq par: gmin  mcmc best fit val: 938.06939050328 quantiles(0.16,0.5,0.84): [615.79237641 776.23148514 887.62963902] 
    comp: leptonic_eq par: gmax  mcmc best fit val: 1096972.3818084246 quantiles(0.16,0.5,0.84): [ 853194.1715297   989105.17631699 1115716.11258555] 
    comp: leptonic_eq par: p  mcmc best fit val: 2.239008026417144 quantiles(0.16,0.5,0.84): [2.12335832 2.18748821 2.26404793] 
    comp: leptonic_eq par: L_inj  mcmc best fit val: 2.0305541578887544e+41 quantiles(0.16,0.5,0.84): [1.46304671e+41 1.85210419e+41 2.47911716e+41] 
    comp: leptonic_eq par: R  mcmc best fit val: 3.796263778809784e+16 quantiles(0.16,0.5,0.84): [2.44386205e+16 3.22514170e+16 4.47277965e+16] 
    comp: leptonic_eq par: B  mcmc best fit val: 0.05165970111094412 quantiles(0.16,0.5,0.84): [0.05129014 0.0607574  0.08044518] 
    comp: leptonic_eq par: beam_obj  mcmc best fit val: 21.28267416705294 quantiles(0.16,0.5,0.84): [18.45888288 20.77343687 23.39424181] 
    ----------------------------



.. image:: Jet_example_model_fit_leptonic_eq_files/Jet_example_model_fit_leptonic_eq_74_1.png


The updated burnin value has increased compared to the initial one, now
the chain samples a parameter space closer to the convergence.

plotting the posterior corner plot
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To have a better rendering on the scatter plot, we redefine the plot
labels

.. code:: ipython3

    mcmc.set_plot_label('L_inj',r'$L_{inj}$',comp_name='leptonic_eq')
    mcmc.set_plot_label('B',r'$B$',comp_name='leptonic_eq')
    mcmc.set_plot_label('gmin',r'$\gamma_{\rm min}$',comp_name='leptonic_eq')
    mcmc.set_plot_label('gmax',r'$\gamma_{\rm max}$',comp_name='leptonic_eq')
    mcmc.set_plot_label('beam_obj',r'$\delta$',comp_name='leptonic_eq')
    mcmc.set_plot_label('p',r'$p$',comp_name='leptonic_eq')


the code below lets you tuning the output

1) mpl.rcParams[‘figure.dpi’] if you increase it you get a better
   definition
2) title_fmt=“.2E” this is the format for python, 2 significant digits,
   scientific notation
3) title_kwargs=dict(fontsize=12) you can change the fontsize

Per-component corner plot

.. code:: ipython3

    import matplotlib as mpl
    
    mpl.rcParams['figure.dpi'] = 80
    f=mcmc.corner_plot(quantiles=(0.16, 0.5, 0.84),title_kwargs=dict(fontsize=12),title_fmt=".2E",use_math_text=True)



.. image:: Jet_example_model_fit_leptonic_eq_files/Jet_example_model_fit_leptonic_eq_81_0.png


.. code:: ipython3

    print(mcmc.acceptance_fraction)


.. parsed-literal::

    0.2702


plotting the model
~~~~~~~~~~~~~~~~~~

To plot the sampled model range against the input mcmc best-fit model

.. code:: ipython3

    
    from jetset.data_loader import ObsData
    sed_data=ObsData.load('Mrk_401.pkl')
    mpl.rcParams['figure.dpi'] = 80
    p=mcmc.plot_model(sed_data=sed_data,size=100)
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_example_model_fit_leptonic_eq_files/Jet_example_model_fit_leptonic_eq_85_0.png


To plot the mcmc best-fit model providing quantiles. The size parameter
defines the number of samples to use to extract the quantiles. I am
using a size equal to the number of steps

.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    p=mcmc.plot_model(sed_data=sed_data,size=100,quantiles=[0.05,0.95])
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_example_model_fit_leptonic_eq_files/Jet_example_model_fit_leptonic_eq_87_0.png


If you want to plot also the model components, pass the argument
``plot_components=True``

.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    p=mcmc.plot_model(sed_data=sed_data,size=500,quantiles=[0.05,0.95],plot_components=True)
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_example_model_fit_leptonic_eq_files/Jet_example_model_fit_leptonic_eq_89_0.png


To plot the frequentist best-fit model range,p roviding quantiles,
provide (``plot_mcmc_best_fit_model=False``)

.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 100
    p=mcmc.plot_model(sed_data=sed_data,size=500,quantiles=[0.05,0.95], plot_mcmc_best_fit_model=False,plot_components=True)
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_example_model_fit_leptonic_eq_files/Jet_example_model_fit_leptonic_eq_91_0.png


plotting chains and individual posteriors
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: ipython3

    import matplotlib.pylab as plt
    
    mpl.rcParams['figure.dpi'] = 80
    f=mcmc.plot_chain(par_name='p',log_plot=False)
    plt.tight_layout()



.. image:: Jet_example_model_fit_leptonic_eq_files/Jet_example_model_fit_leptonic_eq_93_0.png


.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    f=mcmc.plot_chain(log_plot=False)
    plt.tight_layout()



.. image:: Jet_example_model_fit_leptonic_eq_files/Jet_example_model_fit_leptonic_eq_94_0.png


.. code:: ipython3

    
    f=mcmc.plot_par('beam_obj',figsize=(8,6))
    mpl.rcParams['figure.dpi'] = 80



.. image:: Jet_example_model_fit_leptonic_eq_files/Jet_example_model_fit_leptonic_eq_95_0.png


.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    f=mcmc.plot_par('p',log_plot=True,figsize=(8,6))



.. image:: Jet_example_model_fit_leptonic_eq_files/Jet_example_model_fit_leptonic_eq_96_0.png


Save and reuse MCMC
-------------------

.. code:: ipython3

    mcmc.save('mcmc_sampler.pkl')

.. code:: ipython3

    from jetset.mcmc import McmcSampler
    from jetset.data_loader import ObsData
    
    sed_data=ObsData.load('Mrk_401.pkl')
    ms=McmcSampler.load('mcmc_sampler.pkl')
    
    import matplotlib as mpl


.. code:: ipython3

    ms.sampler_parameters




.. raw:: html

    <div><i>Table length=11</i>
    <table id="table13763847680" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>val</th><th>log</th><th>bestfit mcmc</th><th>q16</th><th>q50</th><th>q84</th><th>mcmc bound min</th><th>mcmc bound max</th><th>frozen</th></tr></thead>
    <thead><tr><th>str11</th><th>str17</th><th>float64</th><th>bool</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>bool</th></tr></thead>
    <tr><td>leptonic_eq</td><td>gmin</td><td>9.380694e+02</td><td>False</td><td>9.380694e+02</td><td>6.157924e+02</td><td>7.762315e+02</td><td>8.876296e+02</td><td>1.000000e+01</td><td>1.000000e+03</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>gmax</td><td>1.096972e+06</td><td>False</td><td>1.096972e+06</td><td>8.531942e+05</td><td>9.891052e+05</td><td>1.115716e+06</td><td>1.000000e+05</td><td>4.793714e+06</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>p</td><td>2.239008e+00</td><td>False</td><td>2.239008e+00</td><td>2.123358e+00</td><td>2.187488e+00</td><td>2.264048e+00</td><td>-8.398119e+00</td><td>1.000000e+01</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>L_inj</td><td>2.030554e+41</td><td>False</td><td>2.030554e+41</td><td>1.463047e+41</td><td>1.852104e+41</td><td>2.479117e+41</td><td>1.000000e+38</td><td>1.000000e+43</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>R</td><td>3.796264e+16</td><td>False</td><td>3.796264e+16</td><td>2.443862e+16</td><td>3.225142e+16</td><td>4.472780e+16</td><td>3.162278e+15</td><td>1.376163e+17</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>B</td><td>5.165970e-02</td><td>False</td><td>5.165970e-02</td><td>5.129014e-02</td><td>6.075740e-02</td><td>8.044518e-02</td><td>0.000000e+00</td><td>5.999982e-01</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>beam_obj</td><td>2.128267e+01</td><td>False</td><td>2.128267e+01</td><td>1.845888e+01</td><td>2.077344e+01</td><td>2.339424e+01</td><td>5.000000e+00</td><td>5.000000e+01</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>T_esc_e_primaries</td><td>1.000000e+00</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>1.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>R_H</td><td>1.000000e+17</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>NH_cold_to_rel_e</td><td>1.000000e+00</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>z_cosm</td><td>3.080000e-02</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    </table></div>



.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    p=ms.plot_model(sed_data=sed_data,size=500,plot_components=True)
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_example_model_fit_leptonic_eq_files/Jet_example_model_fit_leptonic_eq_101_0.png


.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    p=ms.plot_model(sed_data=sed_data,size=100,quantiles=[0.05,0.95],plot_components=True)
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_example_model_fit_leptonic_eq_files/Jet_example_model_fit_leptonic_eq_102_0.png


.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    p=ms.plot_model(sed_data=sed_data,size=100,quantiles=[0.05,0.95],plot_mcmc_best_fit_model=True)
    p=ms.model.plot_model(plot_obj=p)
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_example_model_fit_leptonic_eq_files/Jet_example_model_fit_leptonic_eq_103_0.png


.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    f=ms.corner_plot(quantiles=(0.16, 0.5, 0.84),title_kwargs=dict(fontsize=12),title_fmt=".2E",use_math_text=True)



.. image:: Jet_example_model_fit_leptonic_eq_files/Jet_example_model_fit_leptonic_eq_104_0.png


.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    f=ms.plot_par('beam_obj',log_plot=False,figsize=(8,6))



.. image:: Jet_example_model_fit_leptonic_eq_files/Jet_example_model_fit_leptonic_eq_105_0.png


.. code:: ipython3

    f=ms.plot_par('B',log_plot=True,figsize=(8,6))



.. image:: Jet_example_model_fit_leptonic_eq_files/Jet_example_model_fit_leptonic_eq_106_0.png


.. code:: ipython3

    from matplotlib import pylab as plt
    mpl.rcParams['figure.dpi'] = 80
    f=ms.plot_chain(par_name='p',comp_name='leptonic_eq',log_plot=False)
    plt.tight_layout()



.. image:: Jet_example_model_fit_leptonic_eq_files/Jet_example_model_fit_leptonic_eq_107_0.png


.. code:: ipython3

    f=ms.plot_chain(log_plot=False)
    plt.tight_layout()
    mpl.rcParams['figure.dpi'] = 80



.. image:: Jet_example_model_fit_leptonic_eq_files/Jet_example_model_fit_leptonic_eq_108_0.png

