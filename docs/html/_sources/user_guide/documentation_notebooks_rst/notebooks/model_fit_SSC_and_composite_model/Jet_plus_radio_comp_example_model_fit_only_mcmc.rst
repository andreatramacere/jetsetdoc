.. _model_fitting_ssc_plus_radio_comp_only_mcmc:

Model fitting 2: Only SSC and extended radio jet, MCMC without minimizer step
=============================================================================

.. code:: ipython3

    #import warnings
    #warnings.filterwarnings('ignore')
    
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
    



.. image:: Jet_plus_radio_comp_example_model_fit_only_mcmc_files/Jet_plus_radio_comp_example_model_fit_only_mcmc_8_1.png


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
    



.. image:: Jet_plus_radio_comp_example_model_fit_only_mcmc_files/Jet_plus_radio_comp_example_model_fit_only_mcmc_13_1.png


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
    
    
    


.. parsed-literal::

    WARNING: AstropyDeprecationWarning: 'classic' backend for show_in_notebook() is deprecated as of 6.1. Instead, use the supported backend 'ipydatagrid'. [astropy.table.table]



.. raw:: html

    <i>Table length=4</i>
    <table id="table5624695744-961542" class="table-striped table-bordered table-condensed">
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
        console.log("$('#table5624695744-961542').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table5624695744-961542').dataTable({
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
    
    


.. parsed-literal::

    WARNING: AstropyDeprecationWarning: 'classic' backend for show_in_notebook() is deprecated as of 6.1. Instead, use the supported backend 'ipydatagrid'. [astropy.table.table]



.. raw:: html

    <i>Table length=4</i>
    <table id="table6074111648-549196" class="table-striped table-bordered table-condensed">
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
        console.log("$('#table6074111648-549196').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table6074111648-549196').dataTable({
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
    



.. image:: Jet_plus_radio_comp_example_model_fit_only_mcmc_files/Jet_plus_radio_comp_example_model_fit_only_mcmc_16_4.png


Model constraining
~~~~~~~~~~~~~~~~~~

In this step we are not fitting the model, we are just obtaining the
phenomenological ``pre_fit`` model, that will be fitted in using minuit
ore least-square bound, as shown below

.. code:: ipython3

    from jetset.obs_constrain import ObsConstrain
    from jetset.model_manager import  FitModel
    sed_obspar=ObsConstrain(beaming=25,
                            B_range=[0.001,0.1],
                            distr_e='lppl',
                            t_var_sec=3*86400,
                            nu_cut_IR=1E12,
                            SEDShape=my_shape)
    
    
    prefit_jet=sed_obspar.constrain_SSC_model(electron_distribution_log_values=False,silent=True)
    prefit_jet.save_model('prefit_jet.pkl')


.. parsed-literal::

    ================================================================================
    
    ***  constrains parameters from observable ***
    


.. parsed-literal::

    /Users/orion/miniforge3/envs/jetset/lib/python3.12/site-packages/jetset/obs_constrain.py:1514: RankWarning: Polyfit may be poorly conditioned
      p=polyfit(nu_p_IC_model_log,B_grid_log,2)
    WARNING: AstropyDeprecationWarning: 'classic' backend for show_in_notebook() is deprecated as of 6.1. Instead, use the supported backend 'ipydatagrid'. [astropy.table.table]



.. raw:: html

    <i>Table length=12</i>
    <table id="table6077895632-696826" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>par type</th><th>units</th><th>val</th><th>phys. bound. min</th><th>phys. bound. max</th><th>log</th><th>frozen</th></tr></thead>
    <tr><td>jet_leptonic</td><td>R</td><td>region_size</td><td>cm</td><td>3.452668e+16</td><td>1.000000e+03</td><td>1.000000e+30</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R_H</td><td>region_position</td><td>cm</td><td>1.000000e+17</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>magnetic_field</td><td>gauss</td><td>5.050000e-02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>cold_p_to_rel_e_ratio</td><td></td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>beaming</td><td></td><td>2.500000e+01</td><td>1.000000e-04</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>redshift</td><td></td><td>3.080000e-02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmin</td><td>low-energy-cut-off</td><td>lorentz-factor*</td><td>4.697542e+02</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>high-energy-cut-off</td><td>lorentz-factor*</td><td>1.300733e+06</td><td>1.000000e+00</td><td>1.000000e+15</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>N</td><td>emitters_density</td><td>1 / cm3</td><td>6.119093e-01</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gamma0_log_parab</td><td>turn-over-energy</td><td>lorentz-factor*</td><td>3.290961e+04</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>s</td><td>LE_spectral_slope</td><td></td><td>2.169388e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>r</td><td>spectral_curvature</td><td></td><td>7.818737e-01</td><td>-1.500000e+01</td><td>1.500000e+01</td><td>False</td><td>False</td></tr>
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
        console.log("$('#table6077895632-696826').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table6077895632-696826').dataTable({
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



.. image:: Jet_plus_radio_comp_example_model_fit_only_mcmc_files/Jet_plus_radio_comp_example_model_fit_only_mcmc_20_0.png


Model fitting procedure
-----------------------

.. note::
    Please, read the introduction and the caveats :ref:`for the frequentist model fitting <frequentist_model_fitting>`: to understand the frequentist fitting workflow
    see the :ref:`composite_models` user guide for further information about the implementation of :class:`.FitModel`, in particular for parameter setting

Building the fit model
~~~~~~~~~~~~~~~~~~~~~~

.. code:: ipython3

    from jetset.minimizer import fit_SED,ModelMinimizer
    
    from jetset.model_manager import  FitModel
    from jetset.jet_model import Jet


.. code:: ipython3

    from jetset.jet_radio_component import RadioSpectrum
    radio_spectrum=RadioSpectrum()

if you want to fit the ``prefit_model`` you can load the saved one (this
allows you to save time) ad pass it to the ``FitModel`` class

.. code:: ipython3

    prefit_jet=Jet.load_model('prefit_jet.pkl')
    fit_model=FitModel( jet=prefit_jet, name='SSC-best-fit-lsb',template=None) 



.. parsed-literal::

    /Users/orion/miniforge3/envs/jetset/lib/python3.12/site-packages/jetset/model_manager.py:259: UserWarning: no cosmology defined, using the one from jet FlatLambdaCDM(name="Planck13", H0=67.77 km / (Mpc s), Om0=0.30712, Tcmb0=2.7255 K, Neff=3.046, m_nu=[0.   0.   0.06] eV, Ob0=0.048252)
      warnings.warn(m)


.. code:: ipython3

    fit_model.add_component(radio_spectrum)

OR use the one generated above

.. code:: ipython3

    fit_model.show_model_components()


.. parsed-literal::

    
    --------------------------------------------------------------------------------
    Composite model description
    --------------------------------------------------------------------------------
    name: SSC-best-fit-lsb  
    type: composite_model  
    components models:
     -model name: jet_leptonic model type: jet
     -model name: radio_spectrum model type: radio_spectrum
    
    --------------------------------------------------------------------------------


There are now two components: ``jet_leptonic`` and ``radio_spectrum``

We now set the gamma grid size to 200, ad we set ``composite_expr``,
anyhow, since we have only one component this step could be skipped

.. code:: ipython3

    fit_model.jet_leptonic.set_gamma_grid_size(200)
    fit_model.composite_expr='(jet_leptonic+radio_spectrum)'

.. code:: ipython3

    fit_model.parameters


.. parsed-literal::

    WARNING: AstropyDeprecationWarning: 'classic' backend for show_in_notebook() is deprecated as of 6.1. Instead, use the supported backend 'ipydatagrid'. [astropy.table.table]



.. raw:: html

    <i>Table length=16</i>
    <table id="table13367185184-479420" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>par type</th><th>units</th><th>val</th><th>phys. bound. min</th><th>phys. bound. max</th><th>log</th><th>frozen</th></tr></thead>
    <tr><td>jet_leptonic</td><td>gmin</td><td>low-energy-cut-off</td><td>lorentz-factor*</td><td>4.697542e+02</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>high-energy-cut-off</td><td>lorentz-factor*</td><td>1.300733e+06</td><td>1.000000e+00</td><td>1.000000e+15</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>N</td><td>emitters_density</td><td>1 / cm3</td><td>6.119093e-01</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gamma0_log_parab</td><td>turn-over-energy</td><td>lorentz-factor*</td><td>3.290961e+04</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>s</td><td>LE_spectral_slope</td><td></td><td>2.169388e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>r</td><td>spectral_curvature</td><td></td><td>7.818737e-01</td><td>-1.500000e+01</td><td>1.500000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R</td><td>region_size</td><td>cm</td><td>3.452668e+16</td><td>1.000000e+03</td><td>1.000000e+30</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R_H</td><td>region_position</td><td>cm</td><td>1.000000e+17</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>magnetic_field</td><td>gauss</td><td>5.050000e-02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>cold_p_to_rel_e_ratio</td><td></td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>beaming</td><td></td><td>2.500000e+01</td><td>1.000000e-04</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>redshift</td><td></td><td>3.080000e-02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>radio_spectrum</td><td>alpha_radio</td><td>spectral-slope</td><td></td><td>0.000000e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>radio_spectrum</td><td>nu_ssa</td><td>turn-over freq</td><td>Hz</td><td>1.000000e+09</td><td>1.000000e+06</td><td>1.000000e+12</td><td>False</td><td>False</td></tr>
    <tr><td>radio_spectrum</td><td>nu_cut</td><td></td><td>Hz</td><td>1.000000e+11</td><td>1.000000e+06</td><td>1.000000e+13</td><td>False</td><td>False</td></tr>
    <tr><td>radio_spectrum</td><td>nuFnu_p</td><td>flux-const</td><td>cm2 erg / s</td><td>1.000000e-13</td><td>1.000000e-30</td><td>1.000000e-05</td><td>False</td><td>False</td></tr>
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
        console.log("$('#table13367185184-479420').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table13367185184-479420').dataTable({
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



Freezeing parameters and setting fit_range intervals
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

These methods are alternative and equivalent ways to access a model
component for setting parameters state and values or freezing

a) passing as first argument, of the method, the model component
   ``name`` and as second the ``parameter name``

b) accessing the model component member of the composite model class,
   and accessing the ``parameter`` object member

.. code:: ipython3

    #a
    fit_model.freeze('jet_leptonic','z_cosm')
    
    
    #b
    fit_model.jet_leptonic.parameters.R_H.frozen=True
    
    fit_model.jet_leptonic.parameters.R.fit_range=[10**15.5,10**17.5]
    fit_model.jet_leptonic.parameters.beam_obj.fit_range=[5., 50.]
    fit_model.jet_leptonic.parameters.B.fit_range=[1E-3, 1]
    fit_model.jet_leptonic.parameters.s.fit_range=[1, 3]
    fit_model.jet_leptonic.parameters.r.fit_range=[.1, 2]
    fit_model.jet_leptonic.parameters.gamma0_log_parab.fit_range=[1000, 1E6]
    fit_model.jet_leptonic.parameters.gmin.fit_range=[2, 5E3]
    fit_model.jet_leptonic.parameters.gmax.fit_range=[1E4, 1E7]
    
    
    fit_model.radio_spectrum.parameters.nu_ssa.fit_range=[1E7, 5E10]
    fit_model.radio_spectrum.parameters.alpha_radio.fit_range=[-1, 1]
    
    fit_model.radio_spectrum.parameters.nuFnu_p.fit_range=[1E-14, 1E-12]
    


Building the object
^^^^^^^^^^^^^^^^^^^

**Since the pre-fit model was very close to the data, we degrade the
model in order to provide a more robust benchmark to the fitter, but
this is not required!!!**

.. code:: ipython3

    fit_model.jet_leptonic.parameters.N.val=1
    fit_model.jet_leptonic.parameters.r.val=1.0
    fit_model.jet_leptonic.parameters.beam_obj.val=20
    fit_model.radio_spectrum.parameters.alpha_radio.val=0.1
    
    fit_model.eval()

.. code:: ipython3

    %matplotlib inline
    fit_model.set_nu_grid(1E6,1E30,200)
    fit_model.eval()
    p2=fit_model.plot_model(sed_data=sed_data)
    p2.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_plus_radio_comp_example_model_fit_only_mcmc_files/Jet_plus_radio_comp_example_model_fit_only_mcmc_41_0.png


MCMC sampling
-------------

.. note::
    Please, read the introduction and the caveats :ref:`for the Bayesian model fitting <bayesian_model_fitting>` to understand the MCMC sampler workflow.


creating and setting the sampler
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: ipython3

    from jetset.mcmc import McmcSampler
    from jetset.minimizer import ModelMinimizer


We create a ``ModelMinimizer`` object directly for the MCMC step,
skipping the ``.run()`` method, replaced by the call of the
``prepare_fit`` method.

.. code:: ipython3

    model_minimizer = model_minimizer=ModelMinimizer('mcmc')
    model_minimizer.prepare_fit(fit_model,
                                sed_data,
                                nu_fit_start=1E7,
                                nu_fit_stop=1E29,
                                use_UL=True)
    
    mcmc=McmcSampler(model_minimizer)



.. parsed-literal::

    filtering data in fit range = [1.000000e+07,1.000000e+29]
    data length 41


.. important::
   Starting from v1.4.0, the :class:`.McmcSampler` class does not use anymore ``labels`` and ``use_labels_dict``, to select the parameters used by the sampler. Starting from this version, all the ``free`` parameters will be sampled. To select sampled parameters, please ``free/freeze`` them. Please, read the introduction and the caveats :ref:`for the Bayesian model fitting <bayesian_model_fitting>`

You can inspect the mcmc parameters using:

.. code:: ipython3

    mcmc.sampler_parameters




.. raw:: html

    <div><i>Table length=16</i>
    <table id="table13690448032" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>val</th><th>log</th><th>bestfit mcmc</th><th>q16</th><th>q50</th><th>q84</th><th>mcmc bound min</th><th>mcmc bound max</th><th>frozen</th></tr></thead>
    <thead><tr><th>str14</th><th>str16</th><th>float64</th><th>bool</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>bool</th></tr></thead>
    <tr><td>jet_leptonic</td><td>gmin</td><td>4.697542e+02</td><td>False</td><td>4.697542e+02</td><td>--</td><td>--</td><td>--</td><td>2.000000e+00</td><td>5.000000e+03</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>1.300733e+06</td><td>False</td><td>1.300733e+06</td><td>--</td><td>--</td><td>--</td><td>1.000000e+04</td><td>1.000000e+07</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>N</td><td>1.000000e+00</td><td>False</td><td>1.000000e+00</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gamma0_log_parab</td><td>3.290961e+04</td><td>False</td><td>3.290961e+04</td><td>--</td><td>--</td><td>--</td><td>1.000000e+03</td><td>1.000000e+06</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>s</td><td>2.169388e+00</td><td>False</td><td>2.169388e+00</td><td>--</td><td>--</td><td>--</td><td>1.000000e+00</td><td>3.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>r</td><td>1.000000e+00</td><td>False</td><td>1.000000e+00</td><td>--</td><td>--</td><td>--</td><td>1.000000e-01</td><td>2.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R</td><td>3.452668e+16</td><td>False</td><td>3.452668e+16</td><td>--</td><td>--</td><td>--</td><td>3.162278e+15</td><td>3.162278e+17</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>5.050000e-02</td><td>False</td><td>5.050000e-02</td><td>--</td><td>--</td><td>--</td><td>1.000000e-03</td><td>1.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>2.000000e+01</td><td>False</td><td>2.000000e+01</td><td>--</td><td>--</td><td>--</td><td>5.000000e+00</td><td>5.000000e+01</td><td>False</td></tr>
    <tr><td>radio_spectrum</td><td>alpha_radio</td><td>1.000000e-01</td><td>False</td><td>1.000000e-01</td><td>--</td><td>--</td><td>--</td><td>-1.000000e+00</td><td>1.000000e+00</td><td>False</td></tr>
    <tr><td>radio_spectrum</td><td>nu_ssa</td><td>1.000000e+09</td><td>False</td><td>1.000000e+09</td><td>--</td><td>--</td><td>--</td><td>1.000000e+07</td><td>5.000000e+10</td><td>False</td></tr>
    <tr><td>radio_spectrum</td><td>nu_cut</td><td>1.000000e+11</td><td>False</td><td>1.000000e+11</td><td>--</td><td>--</td><td>--</td><td>1.000000e+06</td><td>1.000000e+13</td><td>False</td></tr>
    <tr><td>radio_spectrum</td><td>nuFnu_p</td><td>1.000000e-13</td><td>False</td><td>1.000000e-13</td><td>--</td><td>--</td><td>--</td><td>1.000000e-14</td><td>1.000000e-12</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R_H</td><td>1.000000e+17</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>1.000000e+00</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>3.080000e-02</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    </table></div>



If you want to exclude or include parameters from the samplers just
freeze/free them

.. code:: ipython3

    mcmc.model.radio_spectrum.parameters.nu_ssa.frozen=True


You can set different bounds for all the free parameters with the
instruction below. In case you want to preserve the ``fit_range``
defined in the frequentist minimizer you can pass
``preserve_fit_range=True``

.. note::
    Please, read the introduction and the caveats :ref:`for the Bayesian model fitting <bayesian_model_fitting>` to understand better the bound settings.

.. code:: ipython3

    mcmc.set_bounds(bound=5.0,bound_rel=True)


.. parsed-literal::

    par: gmin  ref value:  469.75417594471753  mcmc bounds: [2, np.float64(2818.5250556683054)]
    par: gmax  ref value:  1300732.557299546  mcmc bounds: [10000.0, np.float64(7804395.343797276)]
    par: N  ref value:  1  mcmc bounds: [0, np.float64(6.0)]
    par: gamma0_log_parab  ref value:  32909.60632315544  mcmc bounds: [1000, np.float64(197457.6379389327)]
    par: s  ref value:  2.1693884502159513  mcmc bounds: [1, 3]
    par: r  ref value:  1.0  mcmc bounds: [0.1, 2]
    par: R  ref value:  3.4526683973208596e+16  mcmc bounds: [3162277660168379.5, np.float64(2.071601038392516e+17)]
    par: B  ref value:  0.0505  mcmc bounds: [0.001, np.float64(0.303)]
    par: beam_obj  ref value:  20  mcmc bounds: [5.0, 50.0]
    par: alpha_radio  ref value:  0.1  mcmc bounds: [np.float64(-0.4), np.float64(0.6)]
    par: nu_cut  ref value:  100000000000.0  mcmc bounds: [1000000.0, np.float64(600000000000.0)]
    par: nuFnu_p  ref value:  1e-13  mcmc bounds: [1e-14, np.float64(6e-13)]


Or, you can set per-parameter bounds as follows, passing the actual
bound for each parameter.

.. code:: ipython3

    mcmc.set_bounds(comp_name='jet_leptonic',par_name='N',par_bounds=[1E-5,10])
    mcmc.set_bounds(comp_name='radio_spectrum',par_name='alpha_radio',par_bounds=[0,1])
    mcmc.set_bounds(comp_name='radio_spectrum',par_name='nuFnu_p',par_bounds=[3E-14,3E-13])
    mcmc.set_bounds(comp_name='radio_spectrum',par_name='nu_cut',par_bounds=[3E10,3E11])


.. parsed-literal::

    par: N  ref value:  1  mcmc bounds: [1e-05, 10]
    par: alpha_radio  ref value:  0.1  mcmc bounds: [0, 1]
    par: nuFnu_p  ref value:  1e-13  mcmc bounds: [3e-14, 3e-13]
    par: nu_cut  ref value:  100000000000.0  mcmc bounds: [30000000000.0, 300000000000.0]


.. code:: ipython3

    mcmc.sampler_parameters




.. raw:: html

    <div><i>Table length=16</i>
    <table id="table13690439536" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>val</th><th>log</th><th>bestfit mcmc</th><th>q16</th><th>q50</th><th>q84</th><th>mcmc bound min</th><th>mcmc bound max</th><th>frozen</th></tr></thead>
    <thead><tr><th>str14</th><th>str16</th><th>float64</th><th>bool</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>bool</th></tr></thead>
    <tr><td>jet_leptonic</td><td>gmin</td><td>4.697542e+02</td><td>False</td><td>4.697542e+02</td><td>--</td><td>--</td><td>--</td><td>2.000000e+00</td><td>2.818525e+03</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>1.300733e+06</td><td>False</td><td>1.300733e+06</td><td>--</td><td>--</td><td>--</td><td>1.000000e+04</td><td>7.804395e+06</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>N</td><td>1.000000e+00</td><td>False</td><td>1.000000e+00</td><td>--</td><td>--</td><td>--</td><td>1.000000e-05</td><td>1.000000e+01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gamma0_log_parab</td><td>3.290961e+04</td><td>False</td><td>3.290961e+04</td><td>--</td><td>--</td><td>--</td><td>1.000000e+03</td><td>1.974576e+05</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>s</td><td>2.169388e+00</td><td>False</td><td>2.169388e+00</td><td>--</td><td>--</td><td>--</td><td>1.000000e+00</td><td>3.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>r</td><td>1.000000e+00</td><td>False</td><td>1.000000e+00</td><td>--</td><td>--</td><td>--</td><td>1.000000e-01</td><td>2.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R</td><td>3.452668e+16</td><td>False</td><td>3.452668e+16</td><td>--</td><td>--</td><td>--</td><td>3.162278e+15</td><td>2.071601e+17</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>5.050000e-02</td><td>False</td><td>5.050000e-02</td><td>--</td><td>--</td><td>--</td><td>1.000000e-03</td><td>3.030000e-01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>2.000000e+01</td><td>False</td><td>2.000000e+01</td><td>--</td><td>--</td><td>--</td><td>5.000000e+00</td><td>5.000000e+01</td><td>False</td></tr>
    <tr><td>radio_spectrum</td><td>alpha_radio</td><td>1.000000e-01</td><td>False</td><td>1.000000e-01</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>1.000000e+00</td><td>False</td></tr>
    <tr><td>radio_spectrum</td><td>nu_cut</td><td>1.000000e+11</td><td>False</td><td>1.000000e+11</td><td>--</td><td>--</td><td>--</td><td>3.000000e+10</td><td>3.000000e+11</td><td>False</td></tr>
    <tr><td>radio_spectrum</td><td>nuFnu_p</td><td>1.000000e-13</td><td>False</td><td>1.000000e-13</td><td>--</td><td>--</td><td>--</td><td>3.000000e-14</td><td>3.000000e-13</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R_H</td><td>1.000000e+17</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>1.000000e+00</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>3.080000e-02</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>radio_spectrum</td><td>nu_ssa</td><td>1.000000e+09</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>1.000000e+07</td><td>5.000000e+10</td><td>True</td></tr>
    </table></div>



Since we are not starting from a minimized model, we need to check for
the convergence. Let’s increase the ``burnin`` size and the number of
``steps``. Once the fit is done, we check the chains, and we tune the
``burnin`` by using the ``tune_burnin`` method.

.. code:: ipython3

    mcmc.run_sampler(nwalkers=30, burnin=100,steps=2000,progress='notebook')



.. parsed-literal::

    mcmc run starting
    



.. parsed-literal::

      0%|          | 0/2000 [00:00<?, ?it/s]


.. parsed-literal::

    mcmc run done, with 1 threads took 73.85 seconds
    ----------------------------
    MCMC best fit solution
    comp: jet_leptonic par: gmin  mcmc best fit val: 879.3947733172844 quantiles(0.16,0.5,0.84): [497.32304563 624.23623642 793.39244304] 
    comp: jet_leptonic par: gmax  mcmc best fit val: 834686.5369901444 quantiles(0.16,0.5,0.84): [767315.6811157  840162.32768564 947234.32377369] 
    comp: jet_leptonic par: N  mcmc best fit val: 0.4426784340247131 quantiles(0.16,0.5,0.84): [0.34441896 0.4202605  0.4960279 ] 
    comp: jet_leptonic par: gamma0_log_parab  mcmc best fit val: 43516.17951174903 quantiles(0.16,0.5,0.84): [11392.91231535 17450.3681303  29082.74202476] 
    comp: jet_leptonic par: s  mcmc best fit val: 2.2647399551642806 quantiles(0.16,0.5,0.84): [1.95514525 2.04628396 2.16564814] 
    comp: jet_leptonic par: r  mcmc best fit val: 0.6360236696172049 quantiles(0.16,0.5,0.84): [0.39928565 0.50959268 0.64285051] 
    comp: jet_leptonic par: R  mcmc best fit val: 3.0443569072649756e+16 quantiles(0.16,0.5,0.84): [2.96561854e+16 3.43101147e+16 3.89811836e+16] 
    comp: jet_leptonic par: B  mcmc best fit val: 0.046663542681320745 quantiles(0.16,0.5,0.84): [0.04825301 0.05551415 0.0617657 ] 
    comp: jet_leptonic par: beam_obj  mcmc best fit val: 25.8221678350652 quantiles(0.16,0.5,0.84): [20.15610209 22.03092852 24.29758343] 
    comp: radio_spectrum par: alpha_radio  mcmc best fit val: 0.7558569605130444 quantiles(0.16,0.5,0.84): [0.19156683 0.40431332 0.68837549] 
    comp: radio_spectrum par: nu_cut  mcmc best fit val: 95794518424.72719 quantiles(0.16,0.5,0.84): [3.17857610e+10 3.98576176e+10 6.87533053e+10] 
    comp: radio_spectrum par: nuFnu_p  mcmc best fit val: 3.718354182378133e-14 quantiles(0.16,0.5,0.84): [3.83967701e-14 5.06538176e-14 5.70978952e-14] 
    ----------------------------


.. code:: ipython3

    import matplotlib as mpl
    
    mpl.rcParams['figure.dpi'] = 80
    f=mcmc.plot_chain(log_plot=False)
    plt.tight_layout()



.. image:: Jet_plus_radio_comp_example_model_fit_only_mcmc_files/Jet_plus_radio_comp_example_model_fit_only_mcmc_61_0.png



.. image:: Jet_plus_radio_comp_example_model_fit_only_mcmc_files/Jet_plus_radio_comp_example_model_fit_only_mcmc_61_1.png


.. code:: ipython3

    mcmc.tune_burnin()


.. parsed-literal::

    ----------------------------
    MCMC best fit solution
    comp: jet_leptonic par: gmin  mcmc best fit val: 879.3947733172844 quantiles(0.16,0.5,0.84): [523.60501355 642.73951498 803.7214715 ] 
    comp: jet_leptonic par: gmax  mcmc best fit val: 834686.5369901444 quantiles(0.16,0.5,0.84): [765714.80704562 835128.19989871 936037.37543226] 
    comp: jet_leptonic par: N  mcmc best fit val: 0.4426784340247131 quantiles(0.16,0.5,0.84): [0.34054138 0.41896068 0.49766232] 
    comp: jet_leptonic par: gamma0_log_parab  mcmc best fit val: 43516.17951174903 quantiles(0.16,0.5,0.84): [11363.81669396 17681.95187746 29917.67303452] 
    comp: jet_leptonic par: s  mcmc best fit val: 2.2647399551642806 quantiles(0.16,0.5,0.84): [1.96118417 2.06022148 2.17306649] 
    comp: jet_leptonic par: r  mcmc best fit val: 0.6360236696172049 quantiles(0.16,0.5,0.84): [0.39644112 0.50763831 0.64567299] 
    comp: jet_leptonic par: R  mcmc best fit val: 3.0443569072649756e+16 quantiles(0.16,0.5,0.84): [2.93610429e+16 3.39459923e+16 3.89153182e+16] 
    comp: jet_leptonic par: B  mcmc best fit val: 0.046663542681320745 quantiles(0.16,0.5,0.84): [0.04867116 0.0557239  0.06207395] 
    comp: jet_leptonic par: beam_obj  mcmc best fit val: 25.8221678350652 quantiles(0.16,0.5,0.84): [20.19075491 22.04786442 24.36406386] 
    comp: radio_spectrum par: alpha_radio  mcmc best fit val: 0.7558569605130444 quantiles(0.16,0.5,0.84): [0.21024277 0.44995519 0.70169692] 
    comp: radio_spectrum par: nu_cut  mcmc best fit val: 95794518424.72719 quantiles(0.16,0.5,0.84): [3.18660757e+10 4.04190702e+10 7.06919748e+10] 
    comp: radio_spectrum par: nuFnu_p  mcmc best fit val: 3.718354182378133e-14 quantiles(0.16,0.5,0.84): [3.77059427e-14 4.98171813e-14 5.65911934e-14] 
    ----------------------------


.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    f=mcmc.plot_chain(log_plot=False)
    plt.tight_layout()



.. image:: Jet_plus_radio_comp_example_model_fit_only_mcmc_files/Jet_plus_radio_comp_example_model_fit_only_mcmc_63_0.png



.. image:: Jet_plus_radio_comp_example_model_fit_only_mcmc_files/Jet_plus_radio_comp_example_model_fit_only_mcmc_63_1.png


Showing the MCMC parameters. Now MCMC bestfit values are updated to the
best-fit MCMC solution

.. code:: ipython3

    mcmc.sampler_parameters




.. raw:: html

    <div><i>Table length=16</i>
    <table id="table13696969824" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>val</th><th>log</th><th>bestfit mcmc</th><th>q16</th><th>q50</th><th>q84</th><th>mcmc bound min</th><th>mcmc bound max</th><th>frozen</th></tr></thead>
    <thead><tr><th>str14</th><th>str16</th><th>float64</th><th>bool</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>bool</th></tr></thead>
    <tr><td>jet_leptonic</td><td>gmin</td><td>8.793948e+02</td><td>False</td><td>8.793948e+02</td><td>5.236050e+02</td><td>6.427395e+02</td><td>8.037215e+02</td><td>2.000000e+00</td><td>2.818525e+03</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>8.346865e+05</td><td>False</td><td>8.346865e+05</td><td>7.657148e+05</td><td>8.351282e+05</td><td>9.360374e+05</td><td>1.000000e+04</td><td>7.804395e+06</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>N</td><td>4.426784e-01</td><td>False</td><td>4.426784e-01</td><td>3.405414e-01</td><td>4.189607e-01</td><td>4.976623e-01</td><td>1.000000e-05</td><td>1.000000e+01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gamma0_log_parab</td><td>4.351618e+04</td><td>False</td><td>4.351618e+04</td><td>1.136382e+04</td><td>1.768195e+04</td><td>2.991767e+04</td><td>1.000000e+03</td><td>1.974576e+05</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>s</td><td>2.264740e+00</td><td>False</td><td>2.264740e+00</td><td>1.961184e+00</td><td>2.060221e+00</td><td>2.173066e+00</td><td>1.000000e+00</td><td>3.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>r</td><td>6.360237e-01</td><td>False</td><td>6.360237e-01</td><td>3.964411e-01</td><td>5.076383e-01</td><td>6.456730e-01</td><td>1.000000e-01</td><td>2.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R</td><td>3.044357e+16</td><td>False</td><td>3.044357e+16</td><td>2.936104e+16</td><td>3.394599e+16</td><td>3.891532e+16</td><td>3.162278e+15</td><td>2.071601e+17</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>4.666354e-02</td><td>False</td><td>4.666354e-02</td><td>4.867116e-02</td><td>5.572390e-02</td><td>6.207395e-02</td><td>1.000000e-03</td><td>3.030000e-01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>2.582217e+01</td><td>False</td><td>2.582217e+01</td><td>2.019075e+01</td><td>2.204786e+01</td><td>2.436406e+01</td><td>5.000000e+00</td><td>5.000000e+01</td><td>False</td></tr>
    <tr><td>radio_spectrum</td><td>alpha_radio</td><td>7.558570e-01</td><td>False</td><td>7.558570e-01</td><td>2.102428e-01</td><td>4.499552e-01</td><td>7.016969e-01</td><td>0.000000e+00</td><td>1.000000e+00</td><td>False</td></tr>
    <tr><td>radio_spectrum</td><td>nu_cut</td><td>9.579452e+10</td><td>False</td><td>9.579452e+10</td><td>3.186608e+10</td><td>4.041907e+10</td><td>7.069197e+10</td><td>3.000000e+10</td><td>3.000000e+11</td><td>False</td></tr>
    <tr><td>radio_spectrum</td><td>nuFnu_p</td><td>3.718354e-14</td><td>False</td><td>3.718354e-14</td><td>3.770594e-14</td><td>4.981718e-14</td><td>5.659119e-14</td><td>3.000000e-14</td><td>3.000000e-13</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R_H</td><td>1.000000e+17</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>1.000000e+00</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>3.080000e-02</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>radio_spectrum</td><td>nu_ssa</td><td>1.000000e+09</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>1.000000e+07</td><td>5.000000e+10</td><td>True</td></tr>
    </table></div>



plotting the posterior corner plot
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To have a better rendering on the scatter plot, we redefine the plot
labels

.. code:: ipython3

    mcmc.set_plot_label('N',r'$N$',comp_name='jet_leptonic')
    mcmc.set_plot_label('B',r'$B$',comp_name='jet_leptonic')
    mcmc.set_plot_label('gmin',r'$\gamma_{\rm min}$',comp_name='jet_leptonic')
    mcmc.set_plot_label('gmax',r'$\gamma_{\rm max}$',comp_name='jet_leptonic')
    mcmc.set_plot_label('beam_obj',r'$\delta$',comp_name='jet_leptonic')
    mcmc.set_plot_label('s',r'$s$',comp_name='jet_leptonic')
    mcmc.set_plot_label('r',r'$r$',comp_name='jet_leptonic')
    mcmc.set_plot_label('gamma0_log_parab',r'$\gamma_0$',comp_name='jet_leptonic')
    mcmc.set_plot_label('alpha_radio',r'$\alpha_{\rm Radio}$',comp_name='radio_spectrum')
    mcmc.set_plot_label('nu_cut',r'$\nu_{\rm cut}$',comp_name='radio_spectrum')
    mcmc.set_plot_label('nuFnu_p',r'$\nu F_{\nu}^{p}$',comp_name='radio_spectrum')

the code below lets you tuning the output

1) mpl.rcParams[‘figure.dpi’] if you increase it you get a better
   definition
2) title_fmt=“.2E” this is the format for python, 2 significant digits,
   scientific notation
3) title_kwargs=dict(fontsize=12) you can change the fontsize

Per-component corner plot

.. code:: ipython3

    
    mpl.rcParams['figure.dpi'] = 80
    f=mcmc.corner_plot(quantiles=(0.16, 0.5, 0.84),title_kwargs=dict(fontsize=12),title_fmt=".2E",use_math_text=True)



.. image:: Jet_plus_radio_comp_example_model_fit_only_mcmc_files/Jet_plus_radio_comp_example_model_fit_only_mcmc_71_0.png



.. image:: Jet_plus_radio_comp_example_model_fit_only_mcmc_files/Jet_plus_radio_comp_example_model_fit_only_mcmc_71_1.png


Global corner plot, pass ``per_component=False``

.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 70
    f=mcmc.corner_plot(quantiles=(0.16, 0.5, 0.84),title_kwargs=dict(fontsize=12),title_fmt=".2E",use_math_text=True,per_component=False)



.. image:: Jet_plus_radio_comp_example_model_fit_only_mcmc_files/Jet_plus_radio_comp_example_model_fit_only_mcmc_73_0.png


.. code:: ipython3

    print(mcmc.acceptance_fraction)


.. parsed-literal::

    0.24696666666666667


plotting the model
~~~~~~~~~~~~~~~~~~

To plot the sampled model range against the input mcmc best-fit model

.. code:: ipython3

    
    from jetset.data_loader import ObsData
    sed_data=ObsData.load('Mrk_401.pkl')
    mpl.rcParams['figure.dpi'] = 80
    p=mcmc.plot_model(sed_data=sed_data,size=100)
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_plus_radio_comp_example_model_fit_only_mcmc_files/Jet_plus_radio_comp_example_model_fit_only_mcmc_77_0.png


To plot the mcmc best-fit model providing quantiles. The size parameter
defines the number of samples to use to extract the quantiles. I am
using a size equal to the number of steps

.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    p=mcmc.plot_model(sed_data=sed_data,size=100,quantiles=[0.05,0.95])
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_plus_radio_comp_example_model_fit_only_mcmc_files/Jet_plus_radio_comp_example_model_fit_only_mcmc_79_0.png


If you want to plot also the model components, pass the argument
``plot_components=True``

.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    p=mcmc.plot_model(sed_data=sed_data,size=500,quantiles=[0.05,0.95],plot_components=True)
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_plus_radio_comp_example_model_fit_only_mcmc_files/Jet_plus_radio_comp_example_model_fit_only_mcmc_81_0.png


To plot the frequentist best-fit model range,p roviding quantiles,
provide (``plot_mcmc_best_fit_model==False``)

.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 100
    p=mcmc.plot_model(sed_data=sed_data,size=500,quantiles=[0.05,0.95], plot_mcmc_best_fit_model=False,plot_components=True)
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_plus_radio_comp_example_model_fit_only_mcmc_files/Jet_plus_radio_comp_example_model_fit_only_mcmc_83_0.png


plotting chains and individual posteriors
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: ipython3

    import matplotlib.pylab as plt
    
    mpl.rcParams['figure.dpi'] = 80
    f=mcmc.plot_chain(par_name='s',log_plot=False)
    plt.tight_layout()



.. image:: Jet_plus_radio_comp_example_model_fit_only_mcmc_files/Jet_plus_radio_comp_example_model_fit_only_mcmc_85_0.png



.. image:: Jet_plus_radio_comp_example_model_fit_only_mcmc_files/Jet_plus_radio_comp_example_model_fit_only_mcmc_85_1.png


.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    f=mcmc.plot_chain(log_plot=False)
    plt.tight_layout()



.. image:: Jet_plus_radio_comp_example_model_fit_only_mcmc_files/Jet_plus_radio_comp_example_model_fit_only_mcmc_86_0.png



.. image:: Jet_plus_radio_comp_example_model_fit_only_mcmc_files/Jet_plus_radio_comp_example_model_fit_only_mcmc_86_1.png


.. code:: ipython3

    
    f=mcmc.plot_par('beam_obj',figsize=(8,6))
    mpl.rcParams['figure.dpi'] = 80



.. image:: Jet_plus_radio_comp_example_model_fit_only_mcmc_files/Jet_plus_radio_comp_example_model_fit_only_mcmc_87_0.png


.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    f=mcmc.plot_par('gamma0_log_parab',log_plot=True,figsize=(8,6))



.. image:: Jet_plus_radio_comp_example_model_fit_only_mcmc_files/Jet_plus_radio_comp_example_model_fit_only_mcmc_88_0.png


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

    <div><i>Table length=16</i>
    <table id="table13824402224" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>val</th><th>log</th><th>bestfit mcmc</th><th>q16</th><th>q50</th><th>q84</th><th>mcmc bound min</th><th>mcmc bound max</th><th>frozen</th></tr></thead>
    <thead><tr><th>str14</th><th>str16</th><th>float64</th><th>bool</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>bool</th></tr></thead>
    <tr><td>jet_leptonic</td><td>gmin</td><td>8.793948e+02</td><td>False</td><td>8.793948e+02</td><td>5.236050e+02</td><td>6.427395e+02</td><td>8.037215e+02</td><td>2.000000e+00</td><td>2.818525e+03</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>8.346865e+05</td><td>False</td><td>8.346865e+05</td><td>7.657148e+05</td><td>8.351282e+05</td><td>9.360374e+05</td><td>1.000000e+04</td><td>7.804395e+06</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>N</td><td>4.426784e-01</td><td>False</td><td>4.426784e-01</td><td>3.405414e-01</td><td>4.189607e-01</td><td>4.976623e-01</td><td>1.000000e-05</td><td>1.000000e+01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gamma0_log_parab</td><td>4.351618e+04</td><td>False</td><td>4.351618e+04</td><td>1.136382e+04</td><td>1.768195e+04</td><td>2.991767e+04</td><td>1.000000e+03</td><td>1.974576e+05</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>s</td><td>2.264740e+00</td><td>False</td><td>2.264740e+00</td><td>1.961184e+00</td><td>2.060221e+00</td><td>2.173066e+00</td><td>1.000000e+00</td><td>3.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>r</td><td>6.360237e-01</td><td>False</td><td>6.360237e-01</td><td>3.964411e-01</td><td>5.076383e-01</td><td>6.456730e-01</td><td>1.000000e-01</td><td>2.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R</td><td>3.044357e+16</td><td>False</td><td>3.044357e+16</td><td>2.936104e+16</td><td>3.394599e+16</td><td>3.891532e+16</td><td>3.162278e+15</td><td>2.071601e+17</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>4.666354e-02</td><td>False</td><td>4.666354e-02</td><td>4.867116e-02</td><td>5.572390e-02</td><td>6.207395e-02</td><td>1.000000e-03</td><td>3.030000e-01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>2.582217e+01</td><td>False</td><td>2.582217e+01</td><td>2.019075e+01</td><td>2.204786e+01</td><td>2.436406e+01</td><td>5.000000e+00</td><td>5.000000e+01</td><td>False</td></tr>
    <tr><td>radio_spectrum</td><td>alpha_radio</td><td>7.558570e-01</td><td>False</td><td>7.558570e-01</td><td>2.102428e-01</td><td>4.499552e-01</td><td>7.016969e-01</td><td>0.000000e+00</td><td>1.000000e+00</td><td>False</td></tr>
    <tr><td>radio_spectrum</td><td>nu_cut</td><td>9.579452e+10</td><td>False</td><td>9.579452e+10</td><td>3.186608e+10</td><td>4.041907e+10</td><td>7.069197e+10</td><td>3.000000e+10</td><td>3.000000e+11</td><td>False</td></tr>
    <tr><td>radio_spectrum</td><td>nuFnu_p</td><td>3.718354e-14</td><td>False</td><td>3.718354e-14</td><td>3.770594e-14</td><td>4.981718e-14</td><td>5.659119e-14</td><td>3.000000e-14</td><td>3.000000e-13</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R_H</td><td>1.000000e+17</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>1.000000e+00</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>3.080000e-02</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>radio_spectrum</td><td>nu_ssa</td><td>1.000000e+09</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>1.000000e+07</td><td>5.000000e+10</td><td>True</td></tr>
    </table></div>



.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    p=ms.plot_model(sed_data=sed_data,size=500,plot_components=True)
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_plus_radio_comp_example_model_fit_only_mcmc_files/Jet_plus_radio_comp_example_model_fit_only_mcmc_93_0.png


.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    p=ms.plot_model(sed_data=sed_data,size=100,quantiles=[0.05,0.95],plot_components=True)
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_plus_radio_comp_example_model_fit_only_mcmc_files/Jet_plus_radio_comp_example_model_fit_only_mcmc_94_0.png


.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    p=ms.plot_model(sed_data=sed_data,size=100,quantiles=[0.05,0.95],plot_mcmc_best_fit_model=True)
    p=ms.model.plot_model(plot_obj=p)
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_plus_radio_comp_example_model_fit_only_mcmc_files/Jet_plus_radio_comp_example_model_fit_only_mcmc_95_0.png


Per-component corner plot

.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    f=ms.corner_plot(quantiles=(0.16, 0.5, 0.84),title_kwargs=dict(fontsize=12),title_fmt=".2E",use_math_text=True)



.. image:: Jet_plus_radio_comp_example_model_fit_only_mcmc_files/Jet_plus_radio_comp_example_model_fit_only_mcmc_97_0.png



.. image:: Jet_plus_radio_comp_example_model_fit_only_mcmc_files/Jet_plus_radio_comp_example_model_fit_only_mcmc_97_1.png


Global corner plot, pass ``per_component=False``

.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    f=ms.corner_plot(quantiles=(0.16, 0.5, 0.84),title_kwargs=dict(fontsize=12),title_fmt=".2E",use_math_text=True,per_component=False)



.. image:: Jet_plus_radio_comp_example_model_fit_only_mcmc_files/Jet_plus_radio_comp_example_model_fit_only_mcmc_99_0.png


.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    f=ms.plot_par('beam_obj',log_plot=False,figsize=(8,6))



.. image:: Jet_plus_radio_comp_example_model_fit_only_mcmc_files/Jet_plus_radio_comp_example_model_fit_only_mcmc_100_0.png


.. code:: ipython3

    f=ms.plot_par('B',log_plot=True,figsize=(8,6))



.. image:: Jet_plus_radio_comp_example_model_fit_only_mcmc_files/Jet_plus_radio_comp_example_model_fit_only_mcmc_101_0.png


.. code:: ipython3

    from matplotlib import pylab as plt
    mpl.rcParams['figure.dpi'] = 80
    f=ms.plot_chain(par_name='s',comp_name='jet_leptonic',log_plot=False)
    plt.tight_layout()



.. image:: Jet_plus_radio_comp_example_model_fit_only_mcmc_files/Jet_plus_radio_comp_example_model_fit_only_mcmc_102_0.png


.. code:: ipython3

    f=ms.plot_chain(log_plot=False)
    plt.tight_layout()
    mpl.rcParams['figure.dpi'] = 80



.. image:: Jet_plus_radio_comp_example_model_fit_only_mcmc_files/Jet_plus_radio_comp_example_model_fit_only_mcmc_103_0.png



.. image:: Jet_plus_radio_comp_example_model_fit_only_mcmc_files/Jet_plus_radio_comp_example_model_fit_only_mcmc_103_1.png

