.. _model_fitting_ssc_plus_radio_comp:

Model fitting 1: Only SSC and extended radio jet
================================================

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
    



.. image:: Jet_plus_radio_comp_example_model_fit_files/Jet_plus_radio_comp_example_model_fit_8_1.png


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
    



.. image:: Jet_plus_radio_comp_example_model_fit_files/Jet_plus_radio_comp_example_model_fit_13_1.png


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
    <table id="table6040870864-683501" class="table-striped table-bordered table-condensed">
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
        console.log("$('#table6040870864-683501').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table6040870864-683501').dataTable({
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
    <table id="table6043074384-345711" class="table-striped table-bordered table-condensed">
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
        console.log("$('#table6043074384-345711').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table6043074384-345711').dataTable({
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
    



.. image:: Jet_plus_radio_comp_example_model_fit_files/Jet_plus_radio_comp_example_model_fit_16_4.png


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
    <table id="table6043166880-648578" class="table-striped table-bordered table-condensed">
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
        console.log("$('#table6043166880-648578').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table6043166880-648578').dataTable({
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



.. image:: Jet_plus_radio_comp_example_model_fit_files/Jet_plus_radio_comp_example_model_fit_20_0.png


Model fitting procedure
-----------------------

.. note::
    Please, read the introduction and the caveats :ref:`for the frequentist model fitting <frequentist_model_fitting>`: to understand the frequentist fitting workflow
    see the :ref:`composite_models` user guide for further information about the implementation of :class:`.FitModel`, in particular for parameter setting

Model fitting with Minuit
~~~~~~~~~~~~~~~~~~~~~~~~~

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
    <table id="table6262815536-143461" class="table-striped table-bordered table-condensed">
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
        console.log("$('#table6262815536-143461').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table6262815536-143461').dataTable({
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
    fit_model.radio_spectrum.parameters.nuFnu_p.fit_range=[1E-14, 1E-12]
    


Building the ModelMinimizer object
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: ipython3

    model_minimizer=ModelMinimizer('minuit')


**Since the pre-fit model was very close to the data, we degrade the
model in order to provide a more robust benchmark to the fitter, but
this is not required!!!**

.. code:: ipython3

    fit_model.jet_leptonic.parameters.N.val=1
    fit_model.jet_leptonic.parameters.r.val=1.0
    fit_model.jet_leptonic.parameters.beam_obj.val=20
    fit_model.eval()

.. code:: ipython3

    %matplotlib inline
    fit_model.set_nu_grid(1E6,1E30,200)
    fit_model.eval()
    p2=fit_model.plot_model(sed_data=sed_data)
    p2.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_plus_radio_comp_example_model_fit_files/Jet_plus_radio_comp_example_model_fit_42_0.png


.. code:: ipython3

    best_fit_res=model_minimizer.fit(fit_model,
                                         sed_data,
                                         1E7,
                                         1E29,
                                         fitname='SSC-best-fit-minuit',
                                         repeat=1)


.. parsed-literal::

    filtering data in fit range = [1.000000e+07,1.000000e+29]
    data length 41
    ================================================================================
    
    *** start fit process ***
    ----- 



.. parsed-literal::

    0it [00:00, ?it/s]


.. parsed-literal::

    - best chisq=3.59135e+01
    
    -------------------------------------------------------------------------
    Fit report
    
    Model: SSC-best-fit-minuit


.. parsed-literal::

    WARNING: AstropyDeprecationWarning: 'classic' backend for show_in_notebook() is deprecated as of 6.1. Instead, use the supported backend 'ipydatagrid'. [astropy.table.table]



.. raw:: html

    <i>Table length=16</i>
    <table id="table6066602544-970781" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>par type</th><th>units</th><th>val</th><th>phys. bound. min</th><th>phys. bound. max</th><th>log</th><th>frozen</th></tr></thead>
    <tr><td>jet_leptonic</td><td>gmin</td><td>low-energy-cut-off</td><td>lorentz-factor*</td><td>5.177288e+02</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>high-energy-cut-off</td><td>lorentz-factor*</td><td>1.028115e+06</td><td>1.000000e+00</td><td>1.000000e+15</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>N</td><td>emitters_density</td><td>1 / cm3</td><td>8.707703e-01</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gamma0_log_parab</td><td>turn-over-energy</td><td>lorentz-factor*</td><td>3.174824e+04</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>s</td><td>LE_spectral_slope</td><td></td><td>2.080500e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>r</td><td>spectral_curvature</td><td></td><td>9.041626e-01</td><td>-1.500000e+01</td><td>1.500000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R</td><td>region_size</td><td>cm</td><td>1.963420e+16</td><td>1.000000e+03</td><td>1.000000e+30</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R_H</td><td>region_position</td><td>cm</td><td>1.000000e+17</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>magnetic_field</td><td>gauss</td><td>7.025155e-02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>cold_p_to_rel_e_ratio</td><td></td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>beaming</td><td></td><td>2.720033e+01</td><td>1.000000e-04</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>redshift</td><td></td><td>3.080000e-02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>radio_spectrum</td><td>alpha_radio</td><td>spectral-slope</td><td></td><td>7.611124e-01</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>radio_spectrum</td><td>nu_ssa</td><td>turn-over freq</td><td>Hz</td><td>8.045445e+08</td><td>1.000000e+06</td><td>1.000000e+12</td><td>False</td><td>False</td></tr>
    <tr><td>radio_spectrum</td><td>nu_cut</td><td></td><td>Hz</td><td>1.343106e+11</td><td>1.000000e+06</td><td>1.000000e+13</td><td>False</td><td>False</td></tr>
    <tr><td>radio_spectrum</td><td>nuFnu_p</td><td>flux-const</td><td>cm2 erg / s</td><td>3.465374e-14</td><td>1.000000e-30</td><td>1.000000e-05</td><td>False</td><td>False</td></tr>
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
        console.log("$('#table6066602544-970781').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table6066602544-970781').dataTable({
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
    calls=5375
    mesg=



.. raw:: html

    <table>
        <tr>
            <th colspan="2" style="text-align:center" title="Minimizer"> Migrad </th>
        </tr>
        <tr>
            <td style="text-align:left" title="Minimum value of function"> FCN = 35.91 </td>
            <td style="text-align:center" title="Total number of function and (optional) gradient evaluations"> Nfcn = 5375 </td>
        </tr>
        <tr>
            <td style="text-align:left" title="Estimated distance to minimum and goal"> EDM = 3.65 (Goal: 0.0002) </td>
            <td style="text-align:center" title="Total run time of algorithms"> time = 6.2 sec </td>
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
            <td> 517.728842 </td>
            <td> 0.000010 </td>
            <td>  </td>
            <td>  </td>
            <td> 2 </td>
            <td> 5E+03 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 1 </th>
            <td> par_1 </td>
            <td> 1.028114794e6 </td>
            <td> 0.000000020e6 </td>
            <td>  </td>
            <td>  </td>
            <td> 1E+04 </td>
            <td> 1E+07 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 2 </th>
            <td> par_2 </td>
            <td> 870.770263e-3 </td>
            <td> 0.000005e-3 </td>
            <td>  </td>
            <td>  </td>
            <td> 0 </td>
            <td>  </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 3 </th>
            <td> par_3 </td>
            <td> 31.7482386e3 </td>
            <td> 0.0000011e3 </td>
            <td>  </td>
            <td>  </td>
            <td> 1E+03 </td>
            <td> 1E+06 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 4 </th>
            <td> par_4 </td>
            <td> 2.080499695 </td>
            <td> 0.000000010 </td>
            <td>  </td>
            <td>  </td>
            <td> 1 </td>
            <td> 3 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 5 </th>
            <td> par_5 </td>
            <td> 904.2e-3 </td>
            <td> 0.7e-3 </td>
            <td>  </td>
            <td>  </td>
            <td> 0.1 </td>
            <td> 2 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 6 </th>
            <td> par_6 </td>
            <td> 19.6341980e15 </td>
            <td> 0.0000004e15 </td>
            <td>  </td>
            <td>  </td>
            <td> 3.16E+15 </td>
            <td> 3.16E+17 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 7 </th>
            <td> par_7 </td>
            <td> 70.2515502e-3 </td>
            <td> 0.0000016e-3 </td>
            <td>  </td>
            <td>  </td>
            <td> 0.001 </td>
            <td> 1 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 8 </th>
            <td> par_8 </td>
            <td> 27.20032721 </td>
            <td> 0.00000014 </td>
            <td>  </td>
            <td>  </td>
            <td> 5 </td>
            <td> 50 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 9 </th>
            <td> par_9 </td>
            <td> 761.11239e-3 </td>
            <td> 0.00006e-3 </td>
            <td>  </td>
            <td>  </td>
            <td> -10 </td>
            <td> 10 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 10 </th>
            <td> par_10 </td>
            <td> 804.54451e6 </td>
            <td> 0.00004e6 </td>
            <td>  </td>
            <td>  </td>
            <td> 1E+07 </td>
            <td> 5E+10 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 11 </th>
            <td> par_11 </td>
            <td> 134.310636e9 </td>
            <td> 0.000007e9 </td>
            <td>  </td>
            <td>  </td>
            <td> 1E+06 </td>
            <td> 1E+13 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 12 </th>
            <td> par_12 </td>
            <td> 34.6537365e-15 </td>
            <td> 0.0000010e-15 </td>
            <td>  </td>
            <td>  </td>
            <td> 1E-14 </td>
            <td> 1E-12 </td>
            <td>  </td>
        </tr>
    </table>


.. parsed-literal::

    dof=28
    chisq=35.913541, chisq/red=1.282626 null hypothesis sig=0.144813
    
    best fit pars


.. parsed-literal::

    WARNING: AstropyDeprecationWarning: 'classic' backend for show_in_notebook() is deprecated as of 6.1. Instead, use the supported backend 'ipydatagrid'. [astropy.table.table]



.. raw:: html

    <i>Table length=16</i>
    <table id="table6061287344-198489" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>val</th><th>bestfit val</th><th>err +</th><th>err -</th><th>start val</th><th>fit range min</th><th>fit range max</th><th>frozen</th></tr></thead>
    <tr><td>jet_leptonic</td><td>gmin</td><td>5.177288e+02</td><td>5.177288e+02</td><td>9.811316e-06</td><td>--</td><td>4.697542e+02</td><td>2.000000e+00</td><td>5.000000e+03</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>1.028115e+06</td><td>1.028115e+06</td><td>1.974980e-02</td><td>--</td><td>1.300733e+06</td><td>1.000000e+04</td><td>1.000000e+07</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>N</td><td>8.707703e-01</td><td>8.707703e-01</td><td>5.453789e-09</td><td>--</td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gamma0_log_parab</td><td>3.174824e+04</td><td>3.174824e+04</td><td>1.122985e-03</td><td>--</td><td>3.290961e+04</td><td>1.000000e+03</td><td>1.000000e+06</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>s</td><td>2.080500e+00</td><td>2.080500e+00</td><td>1.009598e-08</td><td>--</td><td>2.169388e+00</td><td>1.000000e+00</td><td>3.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>r</td><td>9.041626e-01</td><td>9.041626e-01</td><td>7.475562e-04</td><td>--</td><td>1.000000e+00</td><td>1.000000e-01</td><td>2.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R</td><td>1.963420e+16</td><td>1.963420e+16</td><td>4.467360e+08</td><td>--</td><td>3.452668e+16</td><td>3.162278e+15</td><td>3.162278e+17</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R_H</td><td>1.000000e+17</td><td>--</td><td>--</td><td>--</td><td>1.000000e+17</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>7.025155e-02</td><td>7.025155e-02</td><td>1.637437e-09</td><td>--</td><td>5.050000e-02</td><td>1.000000e-03</td><td>1.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>1.000000e+00</td><td>--</td><td>--</td><td>--</td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>2.720033e+01</td><td>2.720033e+01</td><td>1.451816e-07</td><td>--</td><td>2.000000e+01</td><td>5.000000e+00</td><td>5.000000e+01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>3.080000e-02</td><td>--</td><td>--</td><td>--</td><td>3.080000e-02</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>radio_spectrum</td><td>alpha_radio</td><td>7.611124e-01</td><td>7.611124e-01</td><td>6.434335e-08</td><td>--</td><td>0.000000e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td></tr>
    <tr><td>radio_spectrum</td><td>nu_ssa</td><td>8.045445e+08</td><td>8.045445e+08</td><td>4.034497e+01</td><td>--</td><td>1.000000e+09</td><td>1.000000e+07</td><td>5.000000e+10</td><td>False</td></tr>
    <tr><td>radio_spectrum</td><td>nu_cut</td><td>1.343106e+11</td><td>1.343106e+11</td><td>7.428226e+03</td><td>--</td><td>1.000000e+11</td><td>1.000000e+06</td><td>1.000000e+13</td><td>False</td></tr>
    <tr><td>radio_spectrum</td><td>nuFnu_p</td><td>3.465374e-14</td><td>3.465374e-14</td><td>9.955215e-22</td><td>--</td><td>1.000000e-13</td><td>1.000000e-14</td><td>1.000000e-12</td><td>False</td></tr>
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
        console.log("$('#table6061287344-198489').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table6061287344-198489').dataTable({
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

    %matplotlib inline
    fit_model.set_nu_grid(1E6,1E30,200)
    fit_model.eval()
    p2=fit_model.plot_model(sed_data=sed_data)
    p2.setlim(y_min=1E-15,x_min=1E6,x_max=2E28)



.. image:: Jet_plus_radio_comp_example_model_fit_files/Jet_plus_radio_comp_example_model_fit_44_0.png


.. code:: ipython3

    p=model_minimizer.plot_corr_matrix()



.. image:: Jet_plus_radio_comp_example_model_fit_files/Jet_plus_radio_comp_example_model_fit_45_0.png


saving fit model, model minimizer
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

We can save all the fit products to be used later.

.. code:: ipython3

    
    best_fit_res.save_report('SSC-best-fit.pkl')
    model_minimizer.save_model('model_minimizer.pkl')
    fit_model.save_model('fit_model.pkl')

saving fit model, model minimizer
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: ipython3

    best_fit.save_report('SSC-best-fit.pkl')
    model_minimizer.save_model('model_minimizer.pkl')
    fit_model.save_model('fit_model.pkl')

You can obtain profile and contours, but this is typically time
consuming. In any case, better results can be achieved using the MCMC
approach (discussed in next section). For further information regarding
minuit please refer to https://iminuit.readthedocs.io

.. code:: ipython3

    #migrad profile

    #access the data
    profile_migrad=model_minimizer_minuit.minimizer.mnprofile('s')

    #make the plot(no need to run the previous command)
    profile_plot_migrad=model_minimizer_minuit.minimizer.draw_mnprofile('s')

.. code:: ipython2

    #migrad contour
    #access the data
    contour_migrad=model_minimizer_minuit.minimizer.contour('beam_obj','B')

    #make the plot(no need to run the previous command)
    contour_plot_migrad=model_minimizer_minuit.minimizer.draw_contour('beam_obj','B')

you can use also minos contour and profile, in this case the
computational time is even longer:

.. code:: ipython3
    
   profile_migrad=model_minimizer_minuit.minimizer.mnprofile('s')
   profile_plot_migrad=model_minimizer_minuit.minimizer.draw_mnprofile('s')
        
   contour_migrad=model_minimizer_minuit.minimizer.mncontour('r','s')
   contour_plot_migrad=model_minimizer_minuit.minimizer.draw_mncontour('r','s')

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

    model_minimizer = ModelMinimizer.load_model('model_minimizer.pkl')
    
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

    <div><i>Table length=16</i>
    <table id="table13721691744" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>val</th><th>log</th><th>bestfit mcmc</th><th>q16</th><th>q50</th><th>q84</th><th>mcmc bound min</th><th>mcmc bound max</th><th>frozen</th></tr></thead>
    <thead><tr><th>str14</th><th>str16</th><th>float64</th><th>bool</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>bool</th></tr></thead>
    <tr><td>jet_leptonic</td><td>gmin</td><td>5.177288e+02</td><td>False</td><td>5.177288e+02</td><td>--</td><td>--</td><td>--</td><td>2.000000e+00</td><td>5.000000e+03</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>1.028115e+06</td><td>False</td><td>1.028115e+06</td><td>--</td><td>--</td><td>--</td><td>1.000000e+04</td><td>1.000000e+07</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>N</td><td>8.707703e-01</td><td>False</td><td>8.707703e-01</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gamma0_log_parab</td><td>3.174824e+04</td><td>False</td><td>3.174824e+04</td><td>--</td><td>--</td><td>--</td><td>1.000000e+03</td><td>1.000000e+06</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>s</td><td>2.080500e+00</td><td>False</td><td>2.080500e+00</td><td>--</td><td>--</td><td>--</td><td>1.000000e+00</td><td>3.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>r</td><td>9.041626e-01</td><td>False</td><td>9.041626e-01</td><td>--</td><td>--</td><td>--</td><td>1.000000e-01</td><td>2.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R</td><td>1.963420e+16</td><td>False</td><td>1.963420e+16</td><td>--</td><td>--</td><td>--</td><td>3.162278e+15</td><td>3.162278e+17</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>7.025155e-02</td><td>False</td><td>7.025155e-02</td><td>--</td><td>--</td><td>--</td><td>1.000000e-03</td><td>1.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>2.720033e+01</td><td>False</td><td>2.720033e+01</td><td>--</td><td>--</td><td>--</td><td>5.000000e+00</td><td>5.000000e+01</td><td>False</td></tr>
    <tr><td>radio_spectrum</td><td>alpha_radio</td><td>7.611124e-01</td><td>False</td><td>7.611124e-01</td><td>--</td><td>--</td><td>--</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td></tr>
    <tr><td>radio_spectrum</td><td>nu_ssa</td><td>8.045445e+08</td><td>False</td><td>8.045445e+08</td><td>--</td><td>--</td><td>--</td><td>1.000000e+07</td><td>5.000000e+10</td><td>False</td></tr>
    <tr><td>radio_spectrum</td><td>nu_cut</td><td>1.343106e+11</td><td>False</td><td>1.343106e+11</td><td>--</td><td>--</td><td>--</td><td>1.000000e+06</td><td>1.000000e+13</td><td>False</td></tr>
    <tr><td>radio_spectrum</td><td>nuFnu_p</td><td>3.465374e-14</td><td>False</td><td>3.465374e-14</td><td>--</td><td>--</td><td>--</td><td>1.000000e-14</td><td>1.000000e-12</td><td>False</td></tr>
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

    par: gmin  ref value:  517.7288418364199  mcmc bounds: [2, np.float64(3106.3730510185187)]
    par: gmax  ref value:  1028114.7935583222  mcmc bounds: [10000.0, np.float64(6168688.761349933)]
    par: N  ref value:  0.8707702630191665  mcmc bounds: [0, np.float64(5.224621578114999)]
    par: gamma0_log_parab  ref value:  31748.238568564553  mcmc bounds: [1000, np.float64(190489.4314113873)]
    par: s  ref value:  2.080499694887764  mcmc bounds: [1, 3]
    par: r  ref value:  0.9041626352751433  mcmc bounds: [0.1, 2]
    par: R  ref value:  1.9634197964983196e+16  mcmc bounds: [3162277660168379.5, np.float64(1.1780518778989918e+17)]
    par: B  ref value:  0.07025155017048565  mcmc bounds: [0.001, np.float64(0.42150930102291395)]
    par: beam_obj  ref value:  27.200327213749574  mcmc bounds: [5.0, 50.0]
    par: alpha_radio  ref value:  0.7611123873671555  mcmc bounds: [np.float64(-3.044449549468622), np.float64(4.566674324202933)]
    par: nu_cut  ref value:  134310636366.82138  mcmc bounds: [1000000.0, np.float64(805863818200.9283)]
    par: nuFnu_p  ref value:  3.465373648125041e-14  mcmc bounds: [1e-14, np.float64(2.0792241888750246e-13)]


Or, you can set per-parameter bounds as follows, passing the actual
bound for each parameter.

.. code:: ipython3

    mcmc.set_bounds(comp_name='jet_leptonic',par_name='N',par_bounds=[1E-5,10])
    mcmc.set_bounds(comp_name='radio_spectrum',par_name='alpha_radio',par_bounds=[0,1])


.. parsed-literal::

    par: N  ref value:  0.8707702630191665  mcmc bounds: [1e-05, 10]
    par: alpha_radio  ref value:  0.7611123873671555  mcmc bounds: [0, 1]


.. code:: ipython3

    mcmc.sampler_parameters




.. raw:: html

    <div><i>Table length=16</i>
    <table id="table13720126336" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>val</th><th>log</th><th>bestfit mcmc</th><th>q16</th><th>q50</th><th>q84</th><th>mcmc bound min</th><th>mcmc bound max</th><th>frozen</th></tr></thead>
    <thead><tr><th>str14</th><th>str16</th><th>float64</th><th>bool</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>bool</th></tr></thead>
    <tr><td>jet_leptonic</td><td>gmin</td><td>5.177288e+02</td><td>False</td><td>5.177288e+02</td><td>--</td><td>--</td><td>--</td><td>2.000000e+00</td><td>3.106373e+03</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>1.028115e+06</td><td>False</td><td>1.028115e+06</td><td>--</td><td>--</td><td>--</td><td>1.000000e+04</td><td>6.168689e+06</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>N</td><td>8.707703e-01</td><td>False</td><td>8.707703e-01</td><td>--</td><td>--</td><td>--</td><td>1.000000e-05</td><td>1.000000e+01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gamma0_log_parab</td><td>3.174824e+04</td><td>False</td><td>3.174824e+04</td><td>--</td><td>--</td><td>--</td><td>1.000000e+03</td><td>1.904894e+05</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>s</td><td>2.080500e+00</td><td>False</td><td>2.080500e+00</td><td>--</td><td>--</td><td>--</td><td>1.000000e+00</td><td>3.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>r</td><td>9.041626e-01</td><td>False</td><td>9.041626e-01</td><td>--</td><td>--</td><td>--</td><td>1.000000e-01</td><td>2.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R</td><td>1.963420e+16</td><td>False</td><td>1.963420e+16</td><td>--</td><td>--</td><td>--</td><td>3.162278e+15</td><td>1.178052e+17</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>7.025155e-02</td><td>False</td><td>7.025155e-02</td><td>--</td><td>--</td><td>--</td><td>1.000000e-03</td><td>4.215093e-01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>2.720033e+01</td><td>False</td><td>2.720033e+01</td><td>--</td><td>--</td><td>--</td><td>5.000000e+00</td><td>5.000000e+01</td><td>False</td></tr>
    <tr><td>radio_spectrum</td><td>alpha_radio</td><td>7.611124e-01</td><td>False</td><td>7.611124e-01</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>1.000000e+00</td><td>False</td></tr>
    <tr><td>radio_spectrum</td><td>nu_cut</td><td>1.343106e+11</td><td>False</td><td>1.343106e+11</td><td>--</td><td>--</td><td>--</td><td>1.000000e+06</td><td>8.058638e+11</td><td>False</td></tr>
    <tr><td>radio_spectrum</td><td>nuFnu_p</td><td>3.465374e-14</td><td>False</td><td>3.465374e-14</td><td>--</td><td>--</td><td>--</td><td>1.000000e-14</td><td>2.079224e-13</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R_H</td><td>1.000000e+17</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>1.000000e+00</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>3.080000e-02</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>radio_spectrum</td><td>nu_ssa</td><td>8.045445e+08</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>1.000000e+07</td><td>5.000000e+10</td><td>True</td></tr>
    </table></div>



.. code:: ipython3

    mcmc.run_sampler(nwalkers=30, burnin=50,steps=500,progress='notebook')



.. parsed-literal::

    mcmc run starting
    



.. parsed-literal::

      0%|          | 0/500 [00:00<?, ?it/s]


.. parsed-literal::

    mcmc run done, with 1 threads took 21.17 seconds
    ----------------------------
    MCMC best fit solution
    comp: jet_leptonic par: gmin  mcmc best fit val: 608.6952938590161 quantiles(0.16,0.5,0.84): [530.04153394 616.37164759 690.27276822] 
    comp: jet_leptonic par: gmax  mcmc best fit val: 687704.0723510535 quantiles(0.16,0.5,0.84): [ 689954.73241665  815414.97785157 1035562.22883781] 
    comp: jet_leptonic par: N  mcmc best fit val: 1.0321855287779675 quantiles(0.16,0.5,0.84): [0.90786971 1.01341023 1.16440082] 
    comp: jet_leptonic par: gamma0_log_parab  mcmc best fit val: 30684.208582133808 quantiles(0.16,0.5,0.84): [28687.13436088 35893.94810044 45104.21280639] 
    comp: jet_leptonic par: s  mcmc best fit val: 2.200210828644822 quantiles(0.16,0.5,0.84): [2.10149939 2.18532929 2.2425017 ] 
    comp: jet_leptonic par: r  mcmc best fit val: 0.6630033333868393 quantiles(0.16,0.5,0.84): [0.72553567 0.88939908 1.02276907] 
    comp: jet_leptonic par: R  mcmc best fit val: 1.7879996254321388e+16 quantiles(0.16,0.5,0.84): [1.57189063e+16 1.72394928e+16 1.93632217e+16] 
    comp: jet_leptonic par: B  mcmc best fit val: 0.062212458281478554 quantiles(0.16,0.5,0.84): [0.06243553 0.0688929  0.07820608] 
    comp: jet_leptonic par: beam_obj  mcmc best fit val: 30.514541981398462 quantiles(0.16,0.5,0.84): [26.96816597 29.52051846 31.92177338] 
    comp: radio_spectrum par: alpha_radio  mcmc best fit val: 0.741321505734655 quantiles(0.16,0.5,0.84): [0.68641675 0.76133356 0.83875434] 
    comp: radio_spectrum par: nu_cut  mcmc best fit val: 121623835587.44136 quantiles(0.16,0.5,0.84): [1.21106214e+11 1.38058987e+11 1.72777083e+11] 
    comp: radio_spectrum par: nuFnu_p  mcmc best fit val: 3.381245081018728e-14 quantiles(0.16,0.5,0.84): [2.79377873e-14 3.45233980e-14 4.47139541e-14] 
    ----------------------------


Showing the MCMC parameters. Now MCMC bestfit values are updated to the
best-fit MCMC solution

.. code:: ipython3

    mcmc.sampler_parameters




.. raw:: html

    <div><i>Table length=16</i>
    <table id="table13720133488" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>val</th><th>log</th><th>bestfit mcmc</th><th>q16</th><th>q50</th><th>q84</th><th>mcmc bound min</th><th>mcmc bound max</th><th>frozen</th></tr></thead>
    <thead><tr><th>str14</th><th>str16</th><th>float64</th><th>bool</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>bool</th></tr></thead>
    <tr><td>jet_leptonic</td><td>gmin</td><td>6.086953e+02</td><td>False</td><td>6.086953e+02</td><td>5.300415e+02</td><td>6.163716e+02</td><td>6.902728e+02</td><td>2.000000e+00</td><td>3.106373e+03</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>6.877041e+05</td><td>False</td><td>6.877041e+05</td><td>6.899547e+05</td><td>8.154150e+05</td><td>1.035562e+06</td><td>1.000000e+04</td><td>6.168689e+06</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>N</td><td>1.032186e+00</td><td>False</td><td>1.032186e+00</td><td>9.078697e-01</td><td>1.013410e+00</td><td>1.164401e+00</td><td>1.000000e-05</td><td>1.000000e+01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gamma0_log_parab</td><td>3.068421e+04</td><td>False</td><td>3.068421e+04</td><td>2.868713e+04</td><td>3.589395e+04</td><td>4.510421e+04</td><td>1.000000e+03</td><td>1.904894e+05</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>s</td><td>2.200211e+00</td><td>False</td><td>2.200211e+00</td><td>2.101499e+00</td><td>2.185329e+00</td><td>2.242502e+00</td><td>1.000000e+00</td><td>3.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>r</td><td>6.630033e-01</td><td>False</td><td>6.630033e-01</td><td>7.255357e-01</td><td>8.893991e-01</td><td>1.022769e+00</td><td>1.000000e-01</td><td>2.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R</td><td>1.788000e+16</td><td>False</td><td>1.788000e+16</td><td>1.571891e+16</td><td>1.723949e+16</td><td>1.936322e+16</td><td>3.162278e+15</td><td>1.178052e+17</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>6.221246e-02</td><td>False</td><td>6.221246e-02</td><td>6.243553e-02</td><td>6.889290e-02</td><td>7.820608e-02</td><td>1.000000e-03</td><td>4.215093e-01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>3.051454e+01</td><td>False</td><td>3.051454e+01</td><td>2.696817e+01</td><td>2.952052e+01</td><td>3.192177e+01</td><td>5.000000e+00</td><td>5.000000e+01</td><td>False</td></tr>
    <tr><td>radio_spectrum</td><td>alpha_radio</td><td>7.413215e-01</td><td>False</td><td>7.413215e-01</td><td>6.864168e-01</td><td>7.613336e-01</td><td>8.387543e-01</td><td>0.000000e+00</td><td>1.000000e+00</td><td>False</td></tr>
    <tr><td>radio_spectrum</td><td>nu_cut</td><td>1.216238e+11</td><td>False</td><td>1.216238e+11</td><td>1.211062e+11</td><td>1.380590e+11</td><td>1.727771e+11</td><td>1.000000e+06</td><td>8.058638e+11</td><td>False</td></tr>
    <tr><td>radio_spectrum</td><td>nuFnu_p</td><td>3.381245e-14</td><td>False</td><td>3.381245e-14</td><td>2.793779e-14</td><td>3.452340e-14</td><td>4.471395e-14</td><td>1.000000e-14</td><td>2.079224e-13</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R_H</td><td>1.000000e+17</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>1.000000e+00</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>3.080000e-02</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>radio_spectrum</td><td>nu_ssa</td><td>8.045445e+08</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>1.000000e+07</td><td>5.000000e+10</td><td>True</td></tr>
    </table></div>



post-run tuning of the burnin
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

We plot the chains, before and after, tuning the burnin (using
autocorrelation time of emcee).

.. code:: ipython3

    p=mcmc.plot_chain()



.. image:: Jet_plus_radio_comp_example_model_fit_files/Jet_plus_radio_comp_example_model_fit_78_0.png



.. image:: Jet_plus_radio_comp_example_model_fit_files/Jet_plus_radio_comp_example_model_fit_78_1.png


.. code:: ipython3

    mcmc.tune_burnin(tau_coeff=2)
    p=mcmc.plot_chain()


.. parsed-literal::

    ----------------------------
    MCMC best fit solution
    comp: jet_leptonic par: gmin  mcmc best fit val: 608.6952938590161 quantiles(0.16,0.5,0.84): [542.00890886 625.66067974 699.39399566] 
    comp: jet_leptonic par: gmax  mcmc best fit val: 687704.0723510535 quantiles(0.16,0.5,0.84): [ 682661.49238495  799600.69190171 1016577.26536672] 
    comp: jet_leptonic par: N  mcmc best fit val: 1.0321855287779675 quantiles(0.16,0.5,0.84): [0.92860264 1.02552288 1.1872618 ] 
    comp: jet_leptonic par: gamma0_log_parab  mcmc best fit val: 30684.208582133808 quantiles(0.16,0.5,0.84): [28315.92243238 36839.61036893 46083.14229224] 
    comp: jet_leptonic par: s  mcmc best fit val: 2.200210828644822 quantiles(0.16,0.5,0.84): [2.11878698 2.19289637 2.24674685] 
    comp: jet_leptonic par: r  mcmc best fit val: 0.6630033333868393 quantiles(0.16,0.5,0.84): [0.70828216 0.88309752 1.03978876] 
    comp: jet_leptonic par: R  mcmc best fit val: 1.7879996254321388e+16 quantiles(0.16,0.5,0.84): [1.56125667e+16 1.70204941e+16 1.90124771e+16] 
    comp: jet_leptonic par: B  mcmc best fit val: 0.062212458281478554 quantiles(0.16,0.5,0.84): [0.06221882 0.06844937 0.07833284] 
    comp: jet_leptonic par: beam_obj  mcmc best fit val: 30.514541981398462 quantiles(0.16,0.5,0.84): [27.10663036 29.83538903 32.07317717] 
    comp: radio_spectrum par: alpha_radio  mcmc best fit val: 0.741321505734655 quantiles(0.16,0.5,0.84): [0.68093835 0.75937016 0.83886986] 
    comp: radio_spectrum par: nu_cut  mcmc best fit val: 121623835587.44136 quantiles(0.16,0.5,0.84): [1.19775812e+11 1.40029226e+11 1.77293730e+11] 
    comp: radio_spectrum par: nuFnu_p  mcmc best fit val: 3.381245081018728e-14 quantiles(0.16,0.5,0.84): [2.78981534e-14 3.46426326e-14 4.56398906e-14] 
    ----------------------------



.. image:: Jet_plus_radio_comp_example_model_fit_files/Jet_plus_radio_comp_example_model_fit_79_1.png



.. image:: Jet_plus_radio_comp_example_model_fit_files/Jet_plus_radio_comp_example_model_fit_79_2.png


The updated burnin value has increased compared to the initial one, now
the chain samples a parameter space closer to the convergence.

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

    import matplotlib as mpl
    
    mpl.rcParams['figure.dpi'] = 80
    f=mcmc.corner_plot(quantiles=(0.16, 0.5, 0.84),title_kwargs=dict(fontsize=12),title_fmt=".2E",use_math_text=True)



.. image:: Jet_plus_radio_comp_example_model_fit_files/Jet_plus_radio_comp_example_model_fit_86_0.png



.. image:: Jet_plus_radio_comp_example_model_fit_files/Jet_plus_radio_comp_example_model_fit_86_1.png


Global corner plot, pass ``per_component=False``

.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 70
    f=mcmc.corner_plot(quantiles=(0.16, 0.5, 0.84),title_kwargs=dict(fontsize=12),title_fmt=".2E",use_math_text=True,per_component=False)



.. image:: Jet_plus_radio_comp_example_model_fit_files/Jet_plus_radio_comp_example_model_fit_88_0.png


.. code:: ipython3

    print(mcmc.acceptance_fraction)


.. parsed-literal::

    0.29259999999999997


plotting the model
~~~~~~~~~~~~~~~~~~

To plot the sampled model range against the mcmc best-fit model


.. code:: ipython3

    
    from jetset.data_loader import ObsData
    import matplotlib as mpl
    sed_data=ObsData.load('Mrk_401.pkl')
    mpl.rcParams['figure.dpi'] = 80
    p=mcmc.plot_model(sed_data=sed_data,size=100)
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_plus_radio_comp_example_model_fit_files/Jet_plus_radio_comp_example_model_fit_93_0.png


To plot the mcmc best-fit model providing quantiles. The size parameter
defines the number of samples to use to extract the quantiles. I am
using a size equal to the number of steps

.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    p=mcmc.plot_model(sed_data=sed_data,size=100,quantiles=[0.05,0.95])
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_plus_radio_comp_example_model_fit_files/Jet_plus_radio_comp_example_model_fit_95_0.png


If you want to plot also the model components, pass the argument
``plot_components=True``

.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    p=mcmc.plot_model(sed_data=sed_data,size=500,quantiles=[0.05,0.95],plot_components=True)
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_plus_radio_comp_example_model_fit_files/Jet_plus_radio_comp_example_model_fit_97_0.png


To plot the frequentist best-fit model range, providing quantiles,
provide (``plot_mcmc_best_fit_model==False``)

.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 100
    p=mcmc.plot_model(sed_data=sed_data,size=500,quantiles=[0.05,0.95], plot_mcmc_best_fit_model=False,plot_components=True)
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_plus_radio_comp_example_model_fit_files/Jet_plus_radio_comp_example_model_fit_99_0.png


plotting chains and individual posteriors
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: ipython3

    import matplotlib.pylab as plt
    
    mpl.rcParams['figure.dpi'] = 80
    f=mcmc.plot_chain(par_name='s',log_plot=False)
    plt.tight_layout()



.. image:: Jet_plus_radio_comp_example_model_fit_files/Jet_plus_radio_comp_example_model_fit_101_0.png



.. image:: Jet_plus_radio_comp_example_model_fit_files/Jet_plus_radio_comp_example_model_fit_101_1.png


.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    f=mcmc.plot_chain(log_plot=False)
    plt.tight_layout()



.. image:: Jet_plus_radio_comp_example_model_fit_files/Jet_plus_radio_comp_example_model_fit_102_0.png



.. image:: Jet_plus_radio_comp_example_model_fit_files/Jet_plus_radio_comp_example_model_fit_102_1.png


.. code:: ipython3

    
    f=mcmc.plot_par('beam_obj',figsize=(8,6))
    mpl.rcParams['figure.dpi'] = 80



.. image:: Jet_plus_radio_comp_example_model_fit_files/Jet_plus_radio_comp_example_model_fit_103_0.png


.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    f=mcmc.plot_par('gamma0_log_parab',log_plot=True,figsize=(8,6))



.. image:: Jet_plus_radio_comp_example_model_fit_files/Jet_plus_radio_comp_example_model_fit_104_0.png


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
    <table id="table6057552272" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>val</th><th>log</th><th>bestfit mcmc</th><th>q16</th><th>q50</th><th>q84</th><th>mcmc bound min</th><th>mcmc bound max</th><th>frozen</th></tr></thead>
    <thead><tr><th>str14</th><th>str16</th><th>float64</th><th>bool</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>bool</th></tr></thead>
    <tr><td>jet_leptonic</td><td>gmin</td><td>6.086953e+02</td><td>False</td><td>6.086953e+02</td><td>5.420089e+02</td><td>6.256607e+02</td><td>6.993940e+02</td><td>2.000000e+00</td><td>3.106373e+03</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>6.877041e+05</td><td>False</td><td>6.877041e+05</td><td>6.826615e+05</td><td>7.996007e+05</td><td>1.016577e+06</td><td>1.000000e+04</td><td>6.168689e+06</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>N</td><td>1.032186e+00</td><td>False</td><td>1.032186e+00</td><td>9.286026e-01</td><td>1.025523e+00</td><td>1.187262e+00</td><td>1.000000e-05</td><td>1.000000e+01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gamma0_log_parab</td><td>3.068421e+04</td><td>False</td><td>3.068421e+04</td><td>2.831592e+04</td><td>3.683961e+04</td><td>4.608314e+04</td><td>1.000000e+03</td><td>1.904894e+05</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>s</td><td>2.200211e+00</td><td>False</td><td>2.200211e+00</td><td>2.118787e+00</td><td>2.192896e+00</td><td>2.246747e+00</td><td>1.000000e+00</td><td>3.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>r</td><td>6.630033e-01</td><td>False</td><td>6.630033e-01</td><td>7.082822e-01</td><td>8.830975e-01</td><td>1.039789e+00</td><td>1.000000e-01</td><td>2.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R</td><td>1.788000e+16</td><td>False</td><td>1.788000e+16</td><td>1.561257e+16</td><td>1.702049e+16</td><td>1.901248e+16</td><td>3.162278e+15</td><td>1.178052e+17</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>6.221246e-02</td><td>False</td><td>6.221246e-02</td><td>6.221882e-02</td><td>6.844937e-02</td><td>7.833284e-02</td><td>1.000000e-03</td><td>4.215093e-01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>3.051454e+01</td><td>False</td><td>3.051454e+01</td><td>2.710663e+01</td><td>2.983539e+01</td><td>3.207318e+01</td><td>5.000000e+00</td><td>5.000000e+01</td><td>False</td></tr>
    <tr><td>radio_spectrum</td><td>alpha_radio</td><td>7.413215e-01</td><td>False</td><td>7.413215e-01</td><td>6.809384e-01</td><td>7.593702e-01</td><td>8.388699e-01</td><td>0.000000e+00</td><td>1.000000e+00</td><td>False</td></tr>
    <tr><td>radio_spectrum</td><td>nu_cut</td><td>1.216238e+11</td><td>False</td><td>1.216238e+11</td><td>1.197758e+11</td><td>1.400292e+11</td><td>1.772937e+11</td><td>1.000000e+06</td><td>8.058638e+11</td><td>False</td></tr>
    <tr><td>radio_spectrum</td><td>nuFnu_p</td><td>3.381245e-14</td><td>False</td><td>3.381245e-14</td><td>2.789815e-14</td><td>3.464263e-14</td><td>4.563989e-14</td><td>1.000000e-14</td><td>2.079224e-13</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R_H</td><td>1.000000e+17</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>1.000000e+00</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>3.080000e-02</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>radio_spectrum</td><td>nu_ssa</td><td>8.045445e+08</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>1.000000e+07</td><td>5.000000e+10</td><td>True</td></tr>
    </table></div>



.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    p=ms.plot_model(sed_data=sed_data,size=500,plot_components=True)
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_plus_radio_comp_example_model_fit_files/Jet_plus_radio_comp_example_model_fit_109_0.png


.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    p=ms.plot_model(sed_data=sed_data,size=100,quantiles=[0.05,0.95],plot_components=True)
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_plus_radio_comp_example_model_fit_files/Jet_plus_radio_comp_example_model_fit_110_0.png


.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    p=ms.plot_model(sed_data=sed_data,size=100,quantiles=[0.05,0.95],plot_mcmc_best_fit_model=True)
    p=ms.model.plot_model(plot_obj=p)
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_plus_radio_comp_example_model_fit_files/Jet_plus_radio_comp_example_model_fit_111_0.png


Per-component corner plot

.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    f=ms.corner_plot(quantiles=(0.16, 0.5, 0.84),title_kwargs=dict(fontsize=12),title_fmt=".2E",use_math_text=True)



.. image:: Jet_plus_radio_comp_example_model_fit_files/Jet_plus_radio_comp_example_model_fit_113_0.png



.. image:: Jet_plus_radio_comp_example_model_fit_files/Jet_plus_radio_comp_example_model_fit_113_1.png


Global corner plot, pass ``per_component=False``

.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    f=ms.corner_plot(quantiles=(0.16, 0.5, 0.84),title_kwargs=dict(fontsize=12),title_fmt=".2E",use_math_text=True,per_component=False)



.. image:: Jet_plus_radio_comp_example_model_fit_files/Jet_plus_radio_comp_example_model_fit_115_0.png


.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    f=ms.plot_par('beam_obj',log_plot=False,figsize=(8,6))



.. image:: Jet_plus_radio_comp_example_model_fit_files/Jet_plus_radio_comp_example_model_fit_116_0.png


.. code:: ipython3

    f=ms.plot_par('B',log_plot=True,figsize=(8,6))



.. image:: Jet_plus_radio_comp_example_model_fit_files/Jet_plus_radio_comp_example_model_fit_117_0.png


.. code:: ipython3

    from matplotlib import pylab as plt
    mpl.rcParams['figure.dpi'] = 80
    f=ms.plot_chain(par_name='s',comp_name='jet_leptonic',log_plot=False)
    plt.tight_layout()



.. image:: Jet_plus_radio_comp_example_model_fit_files/Jet_plus_radio_comp_example_model_fit_118_0.png


.. code:: ipython3

    f=ms.plot_chain(log_plot=False)
    plt.tight_layout()
    mpl.rcParams['figure.dpi'] = 80



.. image:: Jet_plus_radio_comp_example_model_fit_files/Jet_plus_radio_comp_example_model_fit_119_0.png



.. image:: Jet_plus_radio_comp_example_model_fit_files/Jet_plus_radio_comp_example_model_fit_119_1.png

