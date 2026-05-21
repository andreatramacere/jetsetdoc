.. _model_fitting_2:

Model fitting 2: SSC + galaxy template
======================================

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

    data=Data.from_file(test_SEDs[3])


.. code:: ipython3

    %matplotlib inline
    sed_data=ObsData(data_table=data)
    sed_data.group_data(bin_width=0.2)
    
    sed_data.add_systematics(0.1,[10.**6,10.**29])
    p=sed_data.plot_sed()


.. parsed-literal::

    ================================================================================
    
    ***  binning data  ***
    ---> N bins= 88
    ---> bin_width= 0.2
    ================================================================================
    



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_8_1.png


.. code:: ipython3

    sed_data.save('Mrk_501.pkl')

Phenomenological model constraining
-----------------------------------

see the :ref:`phenom_constr` user guide for further information about loading data 

Spectral indices
~~~~~~~~~~~~~~~~

.. code:: ipython3

    from jetset.sed_shaper import  SEDShape
    my_shape=SEDShape(sed_data)
    my_shape.eval_indices(silent=True)
    p=my_shape.plot_indices()
    p.setlim(y_min=1E-15,y_max=1E-6)


.. parsed-literal::

    ================================================================================
    
    *** evaluating spectral indices for data ***
    ================================================================================
    



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_13_1.png


Sed shaper
~~~~~~~~~~

.. code:: ipython3

    mm,best_fit=my_shape.sync_fit(check_host_gal_template=True,
      Ep_start=None,
      minimizer='lsb',
      silent=True,
      fit_range=[10. , 21.])


.. parsed-literal::

    ================================================================================
    
    *** Log-Polynomial fitting of the synchrotron component ***
    ---> first blind fit run,  fit range: [10.0, 21.0]
    ---> class:  HSP
    
    ---> class:  HSP
    
    



.. raw:: html

    <i>Table length=6</i>
    <table id="table5404441968-114568" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>val</th><th>bestfit val</th><th>err +</th><th>err -</th><th>start val</th><th>fit range min</th><th>fit range max</th><th>frozen</th></tr></thead>
    <tr><td>LogCubic</td><td>b</td><td>-5.589204e-02</td><td>-5.589204e-02</td><td>6.234641e-03</td><td>--</td><td>-5.237376e-02</td><td>-1.000000e+01</td><td>0.000000e+00</td><td>False</td></tr>
    <tr><td>LogCubic</td><td>c</td><td>-3.292704e-04</td><td>-3.292704e-04</td><td>8.968206e-04</td><td>--</td><td>7.639964e-03</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td></tr>
    <tr><td>LogCubic</td><td>Ep</td><td>1.698160e+01</td><td>1.698160e+01</td><td>8.583094e-02</td><td>--</td><td>1.556163e+01</td><td>0.000000e+00</td><td>3.000000e+01</td><td>False</td></tr>
    <tr><td>LogCubic</td><td>Sp</td><td>-1.030620e+01</td><td>-1.030620e+01</td><td>1.607997e-02</td><td>--</td><td>-1.019011e+01</td><td>-3.000000e+01</td><td>0.000000e+00</td><td>False</td></tr>
    <tr><td>host_galaxy</td><td>nuFnu_p_host</td><td>-9.972939e+00</td><td>-9.972939e+00</td><td>3.177593e-02</td><td>--</td><td>-1.019011e+01</td><td>-1.219011e+01</td><td>-8.190114e+00</td><td>False</td></tr>
    <tr><td>host_galaxy</td><td>nu_scale</td><td>-2.290459e-03</td><td>-2.290459e-03</td><td>1.811408e-03</td><td>--</td><td>0.000000e+00</td><td>-2.908697e-03</td><td>2.908697e-03</td><td>False</td></tr>
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
        console.log("$('#table5404441968-114568').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table5404441968-114568').dataTable({
            order: [],
            pageLength: 100,
            lengthMenu: [[10, 25, 50, 100, 500, 1000, -1], [10, 25, 50, 100, 500, 1000, 'All']],
            pagingType: "full_numbers",
            columnDefs: [{targets: [2, 3, 4, 5, 6, 7, 8], type: "optionalnum"}]
        });
    });
    </script>



.. parsed-literal::

    ---> sync       nu_p=+1.698160e+01 (err=+8.583094e-02)  nuFnu_p=-1.030620e+01 (err=+1.607997e-02) curv.=-5.589204e-02 (err=+6.234641e-03)
    ================================================================================
    


.. code:: ipython3

    my_shape.IC_fit(fit_range=[23., 29.],minimizer='minuit',silent=True)
    p=my_shape.plot_shape_fit()
    p.setlim(y_min=1E-15)


.. parsed-literal::

    ================================================================================
    
    *** Log-Polynomial fitting of the IC component ***
    ---> fit range: [23.0, 29.0]
    ---> LogCubic fit
    
    



.. raw:: html

    <i>Table length=4</i>
    <table id="table5407544848-342516" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>val</th><th>bestfit val</th><th>err +</th><th>err -</th><th>start val</th><th>fit range min</th><th>fit range max</th><th>frozen</th></tr></thead>
    <tr><td>LogCubic</td><td>b</td><td>-1.353504e-01</td><td>-1.353504e-01</td><td>3.124219e-02</td><td>--</td><td>-1.000000e+00</td><td>-1.000000e+01</td><td>0.000000e+00</td><td>False</td></tr>
    <tr><td>LogCubic</td><td>c</td><td>-3.471531e-02</td><td>-3.471531e-02</td><td>2.003374e-02</td><td>--</td><td>-1.000000e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td></tr>
    <tr><td>LogCubic</td><td>Ep</td><td>2.545984e+01</td><td>2.545984e+01</td><td>2.139886e-01</td><td>--</td><td>2.550130e+01</td><td>0.000000e+00</td><td>3.000000e+01</td><td>False</td></tr>
    <tr><td>LogCubic</td><td>Sp</td><td>-1.057347e+01</td><td>-1.057347e+01</td><td>4.194508e-02</td><td>--</td><td>-1.000000e+01</td><td>-3.000000e+01</td><td>0.000000e+00</td><td>False</td></tr>
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
        console.log("$('#table5407544848-342516').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table5407544848-342516').dataTable({
            order: [],
            pageLength: 100,
            lengthMenu: [[10, 25, 50, 100, 500, 1000, -1], [10, 25, 50, 100, 500, 1000, 'All']],
            pagingType: "full_numbers",
            columnDefs: [{targets: [2, 3, 4, 5, 6, 7, 8], type: "optionalnum"}]
        });
    });
    </script>



.. parsed-literal::

    ---> IC         nu_p=+2.545984e+01 (err=+2.139886e-01)  nuFnu_p=-1.057347e+01 (err=+4.194508e-02) curv.=-1.353504e-01 (err=+3.124219e-02)
    ================================================================================
    



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_16_3.png


Model constraining
~~~~~~~~~~~~~~~~~~

In this step we are not fitting the model, we are just obtaining the
phenomenological ``pre_fit`` model, that will be fitted in using minuit
ore least-square bound, as shown below

.. code:: ipython3

    from jetset.obs_constrain import ObsConstrain
    from jetset.model_manager import  FitModel
    from jetset.minimizer import fit_SED
    sed_obspar=ObsConstrain(beaming=25,
                            B_range=[0.001,0.1],
                            distr_e='lppl',
                            t_var_sec=3*86400,
                            nu_cut_IR=1E11,
                            SEDShape=my_shape)
    
    
    prefit_jet=sed_obspar.constrain_SSC_model(electron_distribution_log_values=False,silent=True)
    prefit_jet.save_model('prefit_jet_gal_templ.pkl')


.. parsed-literal::

    ================================================================================
    
    ***  constrains parameters from observable ***
    


.. parsed-literal::

    /Users/orion/miniforge3/envs/jetset/lib/python3.12/site-packages/jetset/obs_constrain.py:1514: RankWarning: Polyfit may be poorly conditioned
      p=polyfit(nu_p_IC_model_log,B_grid_log,2)



.. raw:: html

    <i>Table length=12</i>
    <table id="table6164478768-471249" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>par type</th><th>units</th><th>val</th><th>phys. bound. min</th><th>phys. bound. max</th><th>log</th><th>frozen</th></tr></thead>
    <tr><td>jet_leptonic</td><td>R</td><td>region_size</td><td>cm</td><td>1.208522e+16</td><td>1.000000e+03</td><td>1.000000e+30</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R_H</td><td>region_position</td><td>cm</td><td>1.000000e+17</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>magnetic_field</td><td>gauss</td><td>5.050000e-02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>cold_p_to_rel_e_ratio</td><td></td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>beaming</td><td></td><td>2.500000e+01</td><td>1.000000e-04</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>redshift</td><td></td><td>3.360000e-02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmin</td><td>low-energy-cut-off</td><td>lorentz-factor*</td><td>1.487509e+02</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>high-energy-cut-off</td><td>lorentz-factor*</td><td>2.121873e+06</td><td>1.000000e+00</td><td>1.000000e+15</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>N</td><td>emitters_density</td><td>1 / cm3</td><td>2.415254e+01</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gamma0_log_parab</td><td>turn-over-energy</td><td>lorentz-factor*</td><td>6.673207e+03</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>s</td><td>LE_spectral_slope</td><td></td><td>2.251647e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>r</td><td>spectral_curvature</td><td></td><td>2.794602e-01</td><td>-1.500000e+01</td><td>1.500000e+01</td><td>False</td><td>False</td></tr>
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
        console.log("$('#table6164478768-471249').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table6164478768-471249').dataTable({
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

    pl=prefit_jet.plot_model(sed_data=sed_data)
    pl.add_residual_plot(prefit_jet,sed_data)
    pl.setlim(y_min=1E-14,x_min=1E7,x_max=1E29)



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_20_0.png


Model fitting procedure
-----------------------

.. note::
    Please, read the introduction and the caveats :ref:`for the frequentist model fitting <frequentist_model_fitting>` to understand the frequentist fitting workflow
    see the :ref:`composite_models` user guide for further information about the implementation of :class:`.FitModel`, in particular for parameter setting

Model fitting with LSB
~~~~~~~~~~~~~~~~~~~~~~

.. code:: ipython3

    from jetset.model_manager import  FitModel
    from jetset.jet_model import Jet
    
    jet=Jet.load_model('prefit_jet_gal_templ.pkl')
    jet.set_gamma_grid_size(200)

.. code:: ipython3

    fit_model=FitModel( jet=jet, name='SSC-best-fit-lsb',template=my_shape.host_gal) 
    fit_model.show_model()


.. parsed-literal::

    
    --------------------------------------------------------------------------------
    Composite model description
    --------------------------------------------------------------------------------
    name: SSC-best-fit-lsb  
    type: composite_model  
    components models:
     -model name: jet_leptonic model type: jet
     -model name: host_galaxy model type: template
    
    --------------------------------------------------------------------------------
    individual component description
    
    --------------------------------------------------------------------------------
    model description: 
    --------------------------------------------------------------------------------
    type: Jet
    name: jet_leptonic  
    geometry: spherical  
    
    electrons distribution:
     type: lppl  
     gamma energy grid size:  201
     gmin grid : 1.487509e+02
     gmax grid : 2.121873e+06
     normalization:  True
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
    <table id="table5425472784-605885" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>par type</th><th>units</th><th>val</th><th>phys. bound. min</th><th>phys. bound. max</th><th>log</th><th>frozen</th></tr></thead>
    <tr><td>jet_leptonic</td><td>gmin</td><td>low-energy-cut-off</td><td>lorentz-factor*</td><td>1.487509e+02</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>high-energy-cut-off</td><td>lorentz-factor*</td><td>2.121873e+06</td><td>1.000000e+00</td><td>1.000000e+15</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>N</td><td>emitters_density</td><td>1 / cm3</td><td>2.415254e+01</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gamma0_log_parab</td><td>turn-over-energy</td><td>lorentz-factor*</td><td>6.673207e+03</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>s</td><td>LE_spectral_slope</td><td></td><td>2.251647e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>r</td><td>spectral_curvature</td><td></td><td>2.794602e-01</td><td>-1.500000e+01</td><td>1.500000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R</td><td>region_size</td><td>cm</td><td>1.208522e+16</td><td>1.000000e+03</td><td>1.000000e+30</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R_H</td><td>region_position</td><td>cm</td><td>1.000000e+17</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>magnetic_field</td><td>gauss</td><td>5.050000e-02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>cold_p_to_rel_e_ratio</td><td></td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>beaming</td><td></td><td>2.500000e+01</td><td>1.000000e-04</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>redshift</td><td></td><td>3.360000e-02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
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
        console.log("$('#table5425472784-605885').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table5425472784-605885').dataTable({
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
    
    --------------------------------------------------------------------------------
    model description
    --------------------------------------------------------------------------------
    name: host_galaxy  
    type: template  
    
    --------------------------------------------------------------------------------



.. raw:: html

    <i>Table length=2</i>
    <table id="table12987666400-808815" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>par type</th><th>units</th><th>val</th><th>phys. bound. min</th><th>phys. bound. max</th><th>log</th><th>frozen</th></tr></thead>
    <tr><td>host_galaxy</td><td>nuFnu_p_host</td><td>nuFnu-scale</td><td>erg / (s cm2)</td><td>-9.972939e+00</td><td>-2.000000e+01</td><td>2.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>host_galaxy</td><td>nu_scale</td><td>nu-scale</td><td>Hz</td><td>-2.290459e-03</td><td>-2.000000e+00</td><td>2.000000e+00</td><td>False</td><td>False</td></tr>
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
        console.log("$('#table12987666400-808815').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table12987666400-808815').dataTable({
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
    --------------------------------------------------------------------------------


.. note::
   Since the `jet_leptonic to model` has to be summed to the `host_galaxy` model, we do not need to define the functional form for the composite model, because
   the default compostion is the sum of all the components
   (see the :ref:`composite_models` user guide for further information about the new implementation of `FitModel`, in particular for parameter setting).
   Anyhow, we show here the definition of the model composition  for purpose of clarity

.. code:: ipython3

    fit_model.composite_expr='jet_leptonic + host_galaxy'

.. code:: ipython3

    fit_model.freeze('jet_leptonic','z_cosm')
    fit_model.freeze('jet_leptonic','R_H')
    fit_model.jet_leptonic.parameters.N.fit_range=[1E-5, 1E5]
    fit_model.jet_leptonic.parameters.B.fit_range=[1E-3, 1]
    
    fit_model.jet_leptonic.parameters.beam_obj.fit_range=[5., 50.]
    fit_model.jet_leptonic.parameters.R.fit_range=[1E15,1E17]
    fit_model.jet_leptonic.parameters.gmax.fit_range=[1E4,1E8]
    fit_model.host_galaxy.parameters.nuFnu_p_host.frozen=False
    fit_model.host_galaxy.parameters.nu_scale.frozen=True

.. code:: ipython3

    from jetset.minimizer import fit_SED,ModelMinimizer
    
    model_minimizer_lsb=ModelMinimizer('lsb')
    best_fit_lsb=model_minimizer_lsb.fit(fit_model,sed_data,1E11,1E29,fitname='SSC-best-fit-lsb',repeat=1)


.. parsed-literal::

    filtering data in fit range = [1.000000e+11,1.000000e+29]
    data length 31
    ================================================================================
    
    *** start fit process ***
    ----- 



.. parsed-literal::

    0it [00:00, ?it/s]


.. parsed-literal::

    - best chisq=4.28400e+01
    
    -------------------------------------------------------------------------
    Fit report
    
    Model: SSC-best-fit-lsb



.. raw:: html

    <i>Table length=14</i>
    <table id="table6163791168-628116" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>par type</th><th>units</th><th>val</th><th>phys. bound. min</th><th>phys. bound. max</th><th>log</th><th>frozen</th></tr></thead>
    <tr><td>jet_leptonic</td><td>gmin</td><td>low-energy-cut-off</td><td>lorentz-factor*</td><td>1.172236e+02</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>high-energy-cut-off</td><td>lorentz-factor*</td><td>2.121872e+06</td><td>1.000000e+00</td><td>1.000000e+15</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>N</td><td>emitters_density</td><td>1 / cm3</td><td>3.419590e+01</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gamma0_log_parab</td><td>turn-over-energy</td><td>lorentz-factor*</td><td>8.709021e+03</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>s</td><td>LE_spectral_slope</td><td></td><td>2.219322e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>r</td><td>spectral_curvature</td><td></td><td>2.635538e-01</td><td>-1.500000e+01</td><td>1.500000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R</td><td>region_size</td><td>cm</td><td>1.208522e+16</td><td>1.000000e+03</td><td>1.000000e+30</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R_H</td><td>region_position</td><td>cm</td><td>1.000000e+17</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>magnetic_field</td><td>gauss</td><td>3.781473e-02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>cold_p_to_rel_e_ratio</td><td></td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>beaming</td><td></td><td>2.518995e+01</td><td>1.000000e-04</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>redshift</td><td></td><td>3.360000e-02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>host_galaxy</td><td>nuFnu_p_host</td><td>nuFnu-scale</td><td>erg / (s cm2)</td><td>-9.938929e+00</td><td>-2.000000e+01</td><td>2.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>host_galaxy</td><td>nu_scale</td><td>nu-scale</td><td>Hz</td><td>-2.290459e-03</td><td>-2.000000e+00</td><td>2.000000e+00</td><td>False</td><td>True</td></tr>
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
        console.log("$('#table6163791168-628116').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table6163791168-628116').dataTable({
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
    calls=371
    mesg=



.. parsed-literal::

    '`ftol` termination condition is satisfied.'


.. parsed-literal::

    dof=21
    chisq=42.839982, chisq/red=2.039999 null hypothesis sig=0.003295
    
    best fit pars



.. raw:: html

    <i>Table length=14</i>
    <table id="table5425475136-960605" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>val</th><th>bestfit val</th><th>err +</th><th>err -</th><th>start val</th><th>fit range min</th><th>fit range max</th><th>frozen</th></tr></thead>
    <tr><td>jet_leptonic</td><td>gmin</td><td>1.172236e+02</td><td>1.172236e+02</td><td>2.853563e+02</td><td>--</td><td>1.487509e+02</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>2.121872e+06</td><td>2.121872e+06</td><td>2.926648e+06</td><td>--</td><td>2.121873e+06</td><td>1.000000e+04</td><td>1.000000e+08</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>N</td><td>3.419590e+01</td><td>3.419590e+01</td><td>2.318074e+02</td><td>--</td><td>2.415254e+01</td><td>1.000000e-05</td><td>1.000000e+05</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gamma0_log_parab</td><td>8.709021e+03</td><td>8.709021e+03</td><td>1.367815e+04</td><td>--</td><td>6.673207e+03</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>s</td><td>2.219322e+00</td><td>2.219322e+00</td><td>1.015618e-01</td><td>--</td><td>2.251647e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>r</td><td>2.635538e-01</td><td>2.635538e-01</td><td>6.585091e-02</td><td>--</td><td>2.794602e-01</td><td>-1.500000e+01</td><td>1.500000e+01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R</td><td>1.208522e+16</td><td>1.208522e+16</td><td>4.645662e+16</td><td>--</td><td>1.208522e+16</td><td>1.000000e+15</td><td>1.000000e+17</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R_H</td><td>1.000000e+17</td><td>--</td><td>--</td><td>--</td><td>1.000000e+17</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>3.781473e-02</td><td>3.781473e-02</td><td>6.252418e-02</td><td>--</td><td>5.050000e-02</td><td>1.000000e-03</td><td>1.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>1.000000e+00</td><td>--</td><td>--</td><td>--</td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>2.518995e+01</td><td>2.518995e+01</td><td>3.198242e+01</td><td>--</td><td>2.500000e+01</td><td>5.000000e+00</td><td>5.000000e+01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>3.360000e-02</td><td>--</td><td>--</td><td>--</td><td>3.360000e-02</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>host_galaxy</td><td>nuFnu_p_host</td><td>-9.938929e+00</td><td>-9.938929e+00</td><td>3.018550e-02</td><td>--</td><td>-9.972939e+00</td><td>-1.219011e+01</td><td>-8.190114e+00</td><td>False</td></tr>
    <tr><td>host_galaxy</td><td>nu_scale</td><td>-2.290459e-03</td><td>--</td><td>--</td><td>--</td><td>-2.290459e-03</td><td>-2.908697e-03</td><td>2.908697e-03</td><td>True</td></tr>
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
        console.log("$('#table5425475136-960605').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table5425475136-960605').dataTable({
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

    best_fit_lsb.save_report('SSC-best-fit-lsb.pkl')
    model_minimizer_lsb.save_model('model_minimizer_lsb.pkl')
    fit_model.save_model('fit_model_lsb.pkl')

.. code:: ipython3

    %matplotlib inline
    fit_model.set_nu_grid(1E6,1E30,200)
    fit_model.eval()
    p2=fit_model.plot_model(sed_data=sed_data)
    p2.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_31_0.png


Model fitting with Minuit
~~~~~~~~~~~~~~~~~~~~~~~~~

To run the ``minuit`` minimizer we will use the best-fit results from
``lsb`` to set the boundaries for our parameters.

.. code:: ipython3

    fit_model.freeze('jet_leptonic','z_cosm')
    fit_model.freeze('jet_leptonic','R_H')
    fit_model.jet_leptonic.parameters.beam_obj.fit_range=[5., 50.]
    fit_model.jet_leptonic.parameters.R.fit_range=[10**15.5,10**17.5]
    fit_model.host_galaxy.parameters.nuFnu_p_host.frozen=False
    fit_model.host_galaxy.parameters.nu_scale.frozen=True
    fit_model.jet_leptonic.parameters.gmin.fit_range=[10,1000]
    fit_model.jet_leptonic.parameters.gmax.fit_range=[5E5,1E8]
    fit_model.jet_leptonic.parameters.gamma0_log_parab.fit_range=[1E3,5E5]
    


.. code:: ipython3

    model_minimizer_minuit=ModelMinimizer('minuit')
    best_fit_minuit=model_minimizer_minuit.fit(fit_model,sed_data,1E11,1E29,fitname='SSC-best-fit-minuit',repeat=3)


.. parsed-literal::

    filtering data in fit range = [1.000000e+11,1.000000e+29]
    data length 31
    ================================================================================
    
    *** start fit process ***
    ----- 
    fit run: 0



.. parsed-literal::

    0it [00:00, ?it/s]


.. parsed-literal::

    - best chisq=1.37582e+01
    
    fit run: 1
    - old chisq=1.37582e+01



.. parsed-literal::

    0it [00:00, ?it/s]


.. parsed-literal::

    - best chisq=1.36471e+01
    
    fit run: 2
    - old chisq=1.36471e+01



.. parsed-literal::

    0it [00:00, ?it/s]


.. parsed-literal::

    - best chisq=1.36411e+01
    
    -------------------------------------------------------------------------
    Fit report
    
    Model: SSC-best-fit-minuit



.. raw:: html

    <i>Table length=14</i>
    <table id="table5408009408-67002" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>par type</th><th>units</th><th>val</th><th>phys. bound. min</th><th>phys. bound. max</th><th>log</th><th>frozen</th></tr></thead>
    <tr><td>jet_leptonic</td><td>gmin</td><td>low-energy-cut-off</td><td>lorentz-factor*</td><td>9.033414e+01</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>high-energy-cut-off</td><td>lorentz-factor*</td><td>2.489652e+06</td><td>1.000000e+00</td><td>1.000000e+15</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>N</td><td>emitters_density</td><td>1 / cm3</td><td>7.466404e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gamma0_log_parab</td><td>turn-over-energy</td><td>lorentz-factor*</td><td>1.025100e+03</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>s</td><td>LE_spectral_slope</td><td></td><td>1.992998e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>r</td><td>spectral_curvature</td><td></td><td>1.830227e-01</td><td>-1.500000e+01</td><td>1.500000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R</td><td>region_size</td><td>cm</td><td>3.192345e+16</td><td>1.000000e+03</td><td>1.000000e+30</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R_H</td><td>region_position</td><td>cm</td><td>1.000000e+17</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>magnetic_field</td><td>gauss</td><td>9.197306e-03</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>cold_p_to_rel_e_ratio</td><td></td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>beaming</td><td></td><td>3.428535e+01</td><td>1.000000e-04</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>redshift</td><td></td><td>3.360000e-02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>host_galaxy</td><td>nuFnu_p_host</td><td>nuFnu-scale</td><td>erg / (s cm2)</td><td>-9.966824e+00</td><td>-2.000000e+01</td><td>2.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>host_galaxy</td><td>nu_scale</td><td>nu-scale</td><td>Hz</td><td>-2.290459e-03</td><td>-2.000000e+00</td><td>2.000000e+00</td><td>False</td><td>True</td></tr>
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
        console.log("$('#table5408009408-67002').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table5408009408-67002').dataTable({
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
    calls=5060
    mesg=



.. raw:: html

    <table>
        <tr>
            <th colspan="2" style="text-align:center" title="Minimizer"> Migrad </th>
        </tr>
        <tr>
            <td style="text-align:left" title="Minimum value of function"> FCN = 13.64 </td>
            <td style="text-align:center" title="Total number of function and (optional) gradient evaluations"> Nfcn = 5060 </td>
        </tr>
        <tr>
            <td style="text-align:left" title="Estimated distance to minimum and goal"> EDM = 0.247 (Goal: 0.0002) </td>
            <td style="text-align:center" title="Total run time of algorithms"> time = 6.8 sec </td>
        </tr>
        <tr>
            <td style="text-align:center;background-color:#c15ef7;color:black"> INVALID Minimum </td>
            <td style="text-align:center;background-color:#c15ef7;color:black"> ABOVE EDM threshold (goal x 10) </td>
        </tr>
        <tr>
            <td style="text-align:center;background-color:#FFF79A;color:black"> SOME parameters at limit </td>
            <td style="text-align:center;background-color:#92CCA6;color:black"> Below call limit </td>
        </tr>
        <tr>
            <td style="text-align:center;background-color:#FFF79A;color:black"> Hesse ok </td>
            <td style="text-align:center;background-color:#FFF79A;color:black"> Covariance FORCED pos. def. </td>
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
            <td> 90 </td>
            <td> 7 </td>
            <td>  </td>
            <td>  </td>
            <td> 10 </td>
            <td> 1E+03 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 1 </th>
            <td> par_1 </td>
            <td> 2.49e6 </td>
            <td> 0.05e6 </td>
            <td>  </td>
            <td>  </td>
            <td> 5E+05 </td>
            <td> 1E+08 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 2 </th>
            <td> par_2 </td>
            <td> 7.47 </td>
            <td> 0.22 </td>
            <td>  </td>
            <td>  </td>
            <td> 1E-05 </td>
            <td> 1E+05 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 3 </th>
            <td> par_3 </td>
            <td> 1.03e3 </td>
            <td> 0.06e3 </td>
            <td>  </td>
            <td>  </td>
            <td> 1E+03 </td>
            <td> 5E+05 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 4 </th>
            <td> par_4 </td>
            <td> 1.993 </td>
            <td> 0.013 </td>
            <td>  </td>
            <td>  </td>
            <td> -10 </td>
            <td> 10 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 5 </th>
            <td> par_5 </td>
            <td> 0.183 </td>
            <td> 0.006 </td>
            <td>  </td>
            <td>  </td>
            <td> -15 </td>
            <td> 15 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 6 </th>
            <td> par_6 </td>
            <td> 31.9e15 </td>
            <td> 0.8e15 </td>
            <td>  </td>
            <td>  </td>
            <td> 3.16E+15 </td>
            <td> 3.16E+17 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 7 </th>
            <td> par_7 </td>
            <td> 9.2e-3 </td>
            <td> 0.4e-3 </td>
            <td>  </td>
            <td>  </td>
            <td> 0.001 </td>
            <td> 1 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 8 </th>
            <td> par_8 </td>
            <td> 34.3 </td>
            <td> 0.4 </td>
            <td>  </td>
            <td>  </td>
            <td> 5 </td>
            <td> 50 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 9 </th>
            <td> par_9 </td>
            <td> -9.967 </td>
            <td> 0.022 </td>
            <td>  </td>
            <td>  </td>
            <td> -12.2 </td>
            <td> -8.19 </td>
            <td>  </td>
        </tr>
    </table>


.. parsed-literal::

    dof=21
    chisq=13.641061, chisq/red=0.649574 null hypothesis sig=0.884518
    
    best fit pars



.. raw:: html

    <i>Table length=14</i>
    <table id="table5425471872-57478" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>val</th><th>bestfit val</th><th>err +</th><th>err -</th><th>start val</th><th>fit range min</th><th>fit range max</th><th>frozen</th></tr></thead>
    <tr><td>jet_leptonic</td><td>gmin</td><td>9.033414e+01</td><td>9.033414e+01</td><td>7.338552e+00</td><td>--</td><td>1.172236e+02</td><td>1.000000e+01</td><td>1.000000e+03</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>2.489652e+06</td><td>2.489652e+06</td><td>4.525832e+04</td><td>--</td><td>2.121872e+06</td><td>5.000000e+05</td><td>1.000000e+08</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>N</td><td>7.466404e+00</td><td>7.466404e+00</td><td>2.226567e-01</td><td>--</td><td>3.419590e+01</td><td>1.000000e-05</td><td>1.000000e+05</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gamma0_log_parab</td><td>1.025100e+03</td><td>1.025100e+03</td><td>5.880384e+01</td><td>--</td><td>8.709021e+03</td><td>1.000000e+03</td><td>5.000000e+05</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>s</td><td>1.992998e+00</td><td>1.992998e+00</td><td>1.316877e-02</td><td>--</td><td>2.219322e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>r</td><td>1.830227e-01</td><td>1.830227e-01</td><td>6.017357e-03</td><td>--</td><td>2.635538e-01</td><td>-1.500000e+01</td><td>1.500000e+01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R</td><td>3.192345e+16</td><td>3.192345e+16</td><td>7.958194e+14</td><td>--</td><td>1.208522e+16</td><td>3.162278e+15</td><td>3.162278e+17</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R_H</td><td>1.000000e+17</td><td>--</td><td>--</td><td>--</td><td>1.000000e+17</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>9.197306e-03</td><td>9.197306e-03</td><td>3.999634e-04</td><td>--</td><td>3.781473e-02</td><td>1.000000e-03</td><td>1.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>1.000000e+00</td><td>--</td><td>--</td><td>--</td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>3.428535e+01</td><td>3.428535e+01</td><td>4.137938e-01</td><td>--</td><td>2.518995e+01</td><td>5.000000e+00</td><td>5.000000e+01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>3.360000e-02</td><td>--</td><td>--</td><td>--</td><td>3.360000e-02</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>host_galaxy</td><td>nuFnu_p_host</td><td>-9.966824e+00</td><td>-9.966824e+00</td><td>2.196069e-02</td><td>--</td><td>-9.938929e+00</td><td>-1.219011e+01</td><td>-8.190114e+00</td><td>False</td></tr>
    <tr><td>host_galaxy</td><td>nu_scale</td><td>-2.290459e-03</td><td>--</td><td>--</td><td>--</td><td>-2.290459e-03</td><td>-2.908697e-03</td><td>2.908697e-03</td><td>True</td></tr>
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
        console.log("$('#table5425471872-57478').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table5425471872-57478').dataTable({
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
    


for further information regardin minuit please refer to
https://iminuit.readthedocs.io/en/stable/

.. code:: ipython3

    %matplotlib inline
    fit_model.set_nu_grid(1E6,1E30,200)
    fit_model.eval()
    p2=fit_model.plot_model(sed_data=sed_data)
    p2.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_37_0.png


.. code:: ipython3

    best_fit_minuit.save_report('SSC-best-fit-minuit.pkl')
    model_minimizer_minuit.save_model('model_minimizer_minuit.pkl')
    fit_model.save_model('fit_model_minuit.pkl')

Model fitting with a bkn pl
~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: ipython3

    from jetset.obs_constrain import ObsConstrain
    from jetset.model_manager import  FitModel
    from jetset.minimizer import fit_SED
    sed_obspar=ObsConstrain(beaming=25,
                            B_range=[0.001,0.1],
                            distr_e='bkn',
                            t_var_sec=3*86400,
                            nu_cut_IR=1E11,
                            SEDShape=my_shape)
    
    
    prefit_jet=sed_obspar.constrain_SSC_model(electron_distribution_log_values=False,silent=True)
    prefit_jet.save_model('prefit_jet_bkn_gal_templ.pkl')


.. parsed-literal::

    ================================================================================
    
    ***  constrains parameters from observable ***
    



.. raw:: html

    <i>Table length=12</i>
    <table id="table5422882336-9810" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>par type</th><th>units</th><th>val</th><th>phys. bound. min</th><th>phys. bound. max</th><th>log</th><th>frozen</th></tr></thead>
    <tr><td>jet_leptonic</td><td>R</td><td>region_size</td><td>cm</td><td>1.156554e+16</td><td>1.000000e+03</td><td>1.000000e+30</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R_H</td><td>region_position</td><td>cm</td><td>1.000000e+17</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>magnetic_field</td><td>gauss</td><td>3.158426e-02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>cold_p_to_rel_e_ratio</td><td></td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>beaming</td><td></td><td>2.500000e+01</td><td>1.000000e-04</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>redshift</td><td></td><td>3.360000e-02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmin</td><td>low-energy-cut-off</td><td>lorentz-factor*</td><td>1.880919e+02</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>high-energy-cut-off</td><td>lorentz-factor*</td><td>2.683055e+06</td><td>1.000000e+00</td><td>1.000000e+15</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>N</td><td>emitters_density</td><td>1 / cm3</td><td>1.695657e+01</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gamma_break</td><td>turn-over-energy</td><td>lorentz-factor*</td><td>1.841500e+05</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>p</td><td>LE_spectral_slope</td><td></td><td>2.251647e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
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
        console.log("$('#table5422882336-9810').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table5422882336-9810').dataTable({
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

    pl=prefit_jet.plot_model(sed_data=sed_data)
    pl.add_residual_plot(prefit_jet,sed_data)
    pl.setlim(y_min=1E-14,x_min=1E7,x_max=1E29)



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_41_0.png


.. code:: ipython3

    jet_minuit_bkn=Jet.load_model('prefit_jet_bkn_gal_templ.pkl')
    jet_minuit_bkn.set_gamma_grid_size(200)
    
    fit_model_bkn=FitModel( jet=jet_minuit_bkn, name='SSC-best-fit-bkn-lsb',template=my_shape.host_gal) 
    
    
    fit_model_bkn.freeze('jet_leptonic','z_cosm')
    fit_model_bkn.freeze('jet_leptonic','R_H')
    fit_model_bkn.jet_leptonic.parameters.beam_obj.fit_range=[5,50]
    fit_model_bkn.jet_leptonic.parameters.R.fit_range=[10**15.5,10**17.5]
    fit_model_bkn.jet_leptonic.parameters.gmax.fit_range=[1E4,1E8]
    fit_model_bkn.host_galaxy.parameters.nuFnu_p_host.frozen=False
    fit_model_bkn.host_galaxy.parameters.nu_scale.frozen=True
    


.. code:: ipython3

    model_minimizer_lsb_bkn=ModelMinimizer('lsb')
    best_fit_lsb_bkn=model_minimizer_lsb_bkn.fit(fit_model_bkn,sed_data,1E11,1E29,fitname='SSC-best-fit-lsb')



.. parsed-literal::

    filtering data in fit range = [1.000000e+11,1.000000e+29]
    data length 31
    ================================================================================
    
    *** start fit process ***
    ----- 



.. parsed-literal::

    0it [00:00, ?it/s]


.. parsed-literal::

    - best chisq=1.51898e+01
    
    -------------------------------------------------------------------------
    Fit report
    
    Model: SSC-best-fit-lsb



.. raw:: html

    <i>Table length=14</i>
    <table id="table13150598016-714754" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>par type</th><th>units</th><th>val</th><th>phys. bound. min</th><th>phys. bound. max</th><th>log</th><th>frozen</th></tr></thead>
    <tr><td>jet_leptonic</td><td>gmin</td><td>low-energy-cut-off</td><td>lorentz-factor*</td><td>3.293600e+02</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>high-energy-cut-off</td><td>lorentz-factor*</td><td>1.624709e+06</td><td>1.000000e+00</td><td>1.000000e+15</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>N</td><td>emitters_density</td><td>1 / cm3</td><td>1.011791e+01</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gamma_break</td><td>turn-over-energy</td><td>lorentz-factor*</td><td>6.552381e+04</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>p</td><td>LE_spectral_slope</td><td></td><td>2.401467e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>p_1</td><td>HE_spectral_slope</td><td></td><td>2.970469e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R</td><td>region_size</td><td>cm</td><td>1.157161e+16</td><td>1.000000e+03</td><td>1.000000e+30</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R_H</td><td>region_position</td><td>cm</td><td>1.000000e+17</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>magnetic_field</td><td>gauss</td><td>1.298148e-02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>cold_p_to_rel_e_ratio</td><td></td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>beaming</td><td></td><td>4.999999e+01</td><td>1.000000e-04</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>redshift</td><td></td><td>3.360000e-02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>host_galaxy</td><td>nuFnu_p_host</td><td>nuFnu-scale</td><td>erg / (s cm2)</td><td>-9.956499e+00</td><td>-2.000000e+01</td><td>2.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>host_galaxy</td><td>nu_scale</td><td>nu-scale</td><td>Hz</td><td>-2.290459e-03</td><td>-2.000000e+00</td><td>2.000000e+00</td><td>False</td><td>True</td></tr>
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
        console.log("$('#table13150598016-714754').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table13150598016-714754').dataTable({
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
    calls=791
    mesg=



.. parsed-literal::

    '`ftol` termination condition is satisfied.'


.. parsed-literal::

    dof=21
    chisq=15.189772, chisq/red=0.723322 null hypothesis sig=0.813311
    
    best fit pars



.. raw:: html

    <i>Table length=14</i>
    <table id="table13147706800-409643" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>val</th><th>bestfit val</th><th>err +</th><th>err -</th><th>start val</th><th>fit range min</th><th>fit range max</th><th>frozen</th></tr></thead>
    <tr><td>jet_leptonic</td><td>gmin</td><td>3.293600e+02</td><td>3.293600e+02</td><td>3.982487e+02</td><td>--</td><td>1.880919e+02</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>1.624709e+06</td><td>1.624709e+06</td><td>1.284121e+06</td><td>--</td><td>2.683055e+06</td><td>1.000000e+04</td><td>1.000000e+08</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>N</td><td>1.011791e+01</td><td>1.011791e+01</td><td>3.495911e+01</td><td>--</td><td>1.695657e+01</td><td>0.000000e+00</td><td>--</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gamma_break</td><td>6.552381e+04</td><td>6.552381e+04</td><td>6.771892e+04</td><td>--</td><td>1.841500e+05</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>p</td><td>2.401467e+00</td><td>2.401467e+00</td><td>1.661881e-01</td><td>--</td><td>2.251647e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>p_1</td><td>2.970469e+00</td><td>2.970469e+00</td><td>5.302329e-02</td><td>--</td><td>3.500000e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R</td><td>1.157161e+16</td><td>1.157161e+16</td><td>2.443102e+16</td><td>--</td><td>1.156554e+16</td><td>3.162278e+15</td><td>3.162278e+17</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R_H</td><td>1.000000e+17</td><td>--</td><td>--</td><td>--</td><td>1.000000e+17</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>1.298148e-02</td><td>1.298148e-02</td><td>1.111767e-02</td><td>--</td><td>3.158426e-02</td><td>0.000000e+00</td><td>--</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>1.000000e+00</td><td>--</td><td>--</td><td>--</td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>4.999999e+01</td><td>4.999999e+01</td><td>3.604964e+01</td><td>--</td><td>2.500000e+01</td><td>5.000000e+00</td><td>5.000000e+01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>3.360000e-02</td><td>--</td><td>--</td><td>--</td><td>3.360000e-02</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>host_galaxy</td><td>nuFnu_p_host</td><td>-9.956499e+00</td><td>-9.956499e+00</td><td>2.085526e-02</td><td>--</td><td>-9.966824e+00</td><td>-1.219011e+01</td><td>-8.190114e+00</td><td>False</td></tr>
    <tr><td>host_galaxy</td><td>nu_scale</td><td>-2.290459e-03</td><td>--</td><td>--</td><td>--</td><td>-2.290459e-03</td><td>-2.908697e-03</td><td>2.908697e-03</td><td>True</td></tr>
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
        console.log("$('#table13147706800-409643').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table13147706800-409643').dataTable({
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
    fit_model_bkn.set_nu_grid(1E6,1E30,200)
    fit_model_bkn.eval()
    p2=fit_model_bkn.plot_model(sed_data=sed_data)
    p2.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_44_0.png


.. code:: ipython3

    
    fit_model_bkn.jet_leptonic.parameters.beam_obj.fit_range=[5,50]
    fit_model_bkn.jet_leptonic.parameters.R.fit_range=[10**15.5,10**17.5]
    fit_model_bkn.host_galaxy.parameters.nuFnu_p_host.frozen=False
    fit_model_bkn.host_galaxy.parameters.nu_scale.frozen=True
    fit_model_bkn.jet_leptonic.parameters.gmin.fit_range=[10,1000]
    fit_model_bkn.jet_leptonic.parameters.gmax.fit_range=[5E5,1E8]
    fit_model_bkn.jet_leptonic.parameters.gamma_break.fit_range=[1E3,1E6]
    fit_model_bkn.jet_leptonic.parameters.p.fit_range=[1,3]
    fit_model_bkn.jet_leptonic.parameters.p_1.fit_range=[2,5]
    
    
    model_minimizer_minuit_bkn=ModelMinimizer('minuit')
    best_fit_minuit_bkn=model_minimizer_minuit.fit(fit_model_bkn,sed_data,1E11,1E29,fitname='SSC-best-fit-minuit-bkn',repeat=3)


.. parsed-literal::

    filtering data in fit range = [1.000000e+11,1.000000e+29]
    data length 31
    ================================================================================
    
    *** start fit process ***
    ----- 
    fit run: 0



.. parsed-literal::

    0it [00:00, ?it/s]


.. parsed-literal::

    - best chisq=1.48733e+01
    
    fit run: 1
    - old chisq=1.48733e+01



.. parsed-literal::

    0it [00:00, ?it/s]


.. parsed-literal::

    - best chisq=1.48728e+01
    
    fit run: 2
    - old chisq=1.48728e+01



.. parsed-literal::

    0it [00:00, ?it/s]


.. parsed-literal::

    - best chisq=1.48685e+01
    
    -------------------------------------------------------------------------
    Fit report
    
    Model: SSC-best-fit-minuit-bkn



.. raw:: html

    <i>Table length=14</i>
    <table id="table13145324720-592995" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>par type</th><th>units</th><th>val</th><th>phys. bound. min</th><th>phys. bound. max</th><th>log</th><th>frozen</th></tr></thead>
    <tr><td>jet_leptonic</td><td>gmin</td><td>low-energy-cut-off</td><td>lorentz-factor*</td><td>4.114511e+02</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>high-energy-cut-off</td><td>lorentz-factor*</td><td>1.785561e+06</td><td>1.000000e+00</td><td>1.000000e+15</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>N</td><td>emitters_density</td><td>1 / cm3</td><td>6.558950e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gamma_break</td><td>turn-over-energy</td><td>lorentz-factor*</td><td>7.916658e+04</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>p</td><td>LE_spectral_slope</td><td></td><td>2.446983e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>p_1</td><td>HE_spectral_slope</td><td></td><td>2.977385e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R</td><td>region_size</td><td>cm</td><td>1.414889e+16</td><td>1.000000e+03</td><td>1.000000e+30</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R_H</td><td>region_position</td><td>cm</td><td>1.000000e+17</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>magnetic_field</td><td>gauss</td><td>1.078050e-02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>cold_p_to_rel_e_ratio</td><td></td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>beaming</td><td></td><td>5.000000e+01</td><td>1.000000e-04</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>redshift</td><td></td><td>3.360000e-02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>host_galaxy</td><td>nuFnu_p_host</td><td>nuFnu-scale</td><td>erg / (s cm2)</td><td>-9.960352e+00</td><td>-2.000000e+01</td><td>2.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>host_galaxy</td><td>nu_scale</td><td>nu-scale</td><td>Hz</td><td>-2.290459e-03</td><td>-2.000000e+00</td><td>2.000000e+00</td><td>False</td><td>True</td></tr>
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
        console.log("$('#table13145324720-592995').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table13145324720-592995').dataTable({
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
    calls=3787
    mesg=



.. raw:: html

    <table>
        <tr>
            <th colspan="2" style="text-align:center" title="Minimizer"> Migrad </th>
        </tr>
        <tr>
            <td style="text-align:left" title="Minimum value of function"> FCN = 14.87 </td>
            <td style="text-align:center" title="Total number of function and (optional) gradient evaluations"> Nfcn = 3787 </td>
        </tr>
        <tr>
            <td style="text-align:left" title="Estimated distance to minimum and goal"> EDM = 2.42 (Goal: 0.0002) </td>
            <td style="text-align:center" title="Total run time of algorithms"> time = 5.2 sec </td>
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
            <td> 411.45 </td>
            <td> 0.09 </td>
            <td>  </td>
            <td>  </td>
            <td> 10 </td>
            <td> 1E+03 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 1 </th>
            <td> par_1 </td>
            <td> 1.7856e6 </td>
            <td> 0.0007e6 </td>
            <td>  </td>
            <td>  </td>
            <td> 5E+05 </td>
            <td> 1E+08 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 2 </th>
            <td> par_2 </td>
            <td> 6.55895 </td>
            <td> 0.00015 </td>
            <td>  </td>
            <td>  </td>
            <td> 0 </td>
            <td>  </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 3 </th>
            <td> par_3 </td>
            <td> 79.17e3 </td>
            <td> 0.04e3 </td>
            <td>  </td>
            <td>  </td>
            <td> 1E+03 </td>
            <td> 1E+06 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 4 </th>
            <td> par_4 </td>
            <td> 2.446983 </td>
            <td> 0.000030 </td>
            <td>  </td>
            <td>  </td>
            <td> 1 </td>
            <td> 3 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 5 </th>
            <td> par_5 </td>
            <td> 2.9774 </td>
            <td> 0.0023 </td>
            <td>  </td>
            <td>  </td>
            <td> 2 </td>
            <td> 5 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 6 </th>
            <td> par_6 </td>
            <td> 14.149e15 </td>
            <td> 0.004e15 </td>
            <td>  </td>
            <td>  </td>
            <td> 3.16E+15 </td>
            <td> 3.16E+17 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 7 </th>
            <td> par_7 </td>
            <td> 10.7805e-3 </td>
            <td> 0.0022e-3 </td>
            <td>  </td>
            <td>  </td>
            <td> 0 </td>
            <td>  </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 8 </th>
            <td> par_8 </td>
            <td> 49.9999994 </td>
            <td> 0.0000008 </td>
            <td>  </td>
            <td>  </td>
            <td> 5 </td>
            <td> 50 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 9 </th>
            <td> par_9 </td>
            <td> -9.96035 </td>
            <td> 0.00030 </td>
            <td>  </td>
            <td>  </td>
            <td> -12.2 </td>
            <td> -8.19 </td>
            <td>  </td>
        </tr>
    </table>


.. parsed-literal::

    dof=21
    chisq=14.868480, chisq/red=0.708023 null hypothesis sig=0.829493
    
    best fit pars



.. raw:: html

    <i>Table length=14</i>
    <table id="table5423085424-16967" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>val</th><th>bestfit val</th><th>err +</th><th>err -</th><th>start val</th><th>fit range min</th><th>fit range max</th><th>frozen</th></tr></thead>
    <tr><td>jet_leptonic</td><td>gmin</td><td>4.114511e+02</td><td>4.114511e+02</td><td>8.548231e-02</td><td>--</td><td>3.293600e+02</td><td>1.000000e+01</td><td>1.000000e+03</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>1.785561e+06</td><td>1.785561e+06</td><td>7.421492e+02</td><td>--</td><td>1.624709e+06</td><td>5.000000e+05</td><td>1.000000e+08</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>N</td><td>6.558950e+00</td><td>6.558950e+00</td><td>1.528734e-04</td><td>--</td><td>1.011791e+01</td><td>0.000000e+00</td><td>--</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gamma_break</td><td>7.916658e+04</td><td>7.916658e+04</td><td>4.037127e+01</td><td>--</td><td>6.552381e+04</td><td>1.000000e+03</td><td>1.000000e+06</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>p</td><td>2.446983e+00</td><td>2.446983e+00</td><td>2.960642e-05</td><td>--</td><td>2.401467e+00</td><td>1.000000e+00</td><td>3.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>p_1</td><td>2.977385e+00</td><td>2.977385e+00</td><td>2.352622e-03</td><td>--</td><td>2.970469e+00</td><td>2.000000e+00</td><td>5.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R</td><td>1.414889e+16</td><td>1.414889e+16</td><td>3.930474e+12</td><td>--</td><td>1.157161e+16</td><td>3.162278e+15</td><td>3.162278e+17</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R_H</td><td>1.000000e+17</td><td>--</td><td>--</td><td>--</td><td>1.000000e+17</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>1.078050e-02</td><td>1.078050e-02</td><td>2.166851e-06</td><td>--</td><td>1.298148e-02</td><td>0.000000e+00</td><td>--</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>1.000000e+00</td><td>--</td><td>--</td><td>--</td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>5.000000e+01</td><td>5.000000e+01</td><td>7.699022e-07</td><td>--</td><td>4.999999e+01</td><td>5.000000e+00</td><td>5.000000e+01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>3.360000e-02</td><td>--</td><td>--</td><td>--</td><td>3.360000e-02</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>host_galaxy</td><td>nuFnu_p_host</td><td>-9.960352e+00</td><td>-9.960352e+00</td><td>2.976574e-04</td><td>--</td><td>-9.956499e+00</td><td>-1.219011e+01</td><td>-8.190114e+00</td><td>False</td></tr>
    <tr><td>host_galaxy</td><td>nu_scale</td><td>-2.290459e-03</td><td>--</td><td>--</td><td>--</td><td>-2.290459e-03</td><td>-2.908697e-03</td><td>2.908697e-03</td><td>True</td></tr>
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
        console.log("$('#table5423085424-16967').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table5423085424-16967').dataTable({
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
    fit_model_bkn.set_nu_grid(1E6,1E30,200)
    fit_model_bkn.eval()
    p2=fit_model_bkn.plot_model(sed_data=sed_data)
    p2.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_46_0.png


.. code:: ipython3

    %matplotlib inline
    from jetset.plot_sedfit import PlotSED
    fit_model_bkn.set_nu_grid(1E6,1E30,200)
    fit_model_bkn.eval()
    fit_model.set_nu_grid(1E6,1E30,200)
    fit_model.eval()
    
    p2=PlotSED()
    p2.add_data_plot(sed_data,fit_range=[ 1E11, 1E29])
    p2.add_model_plot(fit_model,color='black')
    p2.add_residual_plot(fit_model,sed_data,fit_range=[ 1E11, 1E29],color='black')
    p2.add_model_plot(fit_model_bkn,color='red')
    p2.add_residual_plot(fit_model_bkn,sed_data,fit_range=[ 1E11, 1E29],color='red')
    p2.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_47_0.png


MCMC sampling
-------------

.. note::
    Please, read the introduction and the caveats :ref:`for the Bayesian model fitting <bayesian_model_fitting>` to understand the MCMC sampler workflow.


Creating, setting, and running the sampler
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: ipython3

    from jetset.mcmc import McmcSampler
    from jetset.minimizer import ModelMinimizer


.. code:: ipython3

    model_minimizer_lsb = ModelMinimizer.load_model('model_minimizer_minuit.pkl')
    
    mcmc=McmcSampler(model_minimizer_lsb)

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

    <div><i>Table length=14</i>
    <table id="table13796314720" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>val</th><th>log</th><th>bestfit mcmc</th><th>q16</th><th>q50</th><th>q84</th><th>mcmc bound min</th><th>mcmc bound max</th><th>frozen</th></tr></thead>
    <thead><tr><th>str12</th><th>str16</th><th>float64</th><th>bool</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>bool</th></tr></thead>
    <tr><td>jet_leptonic</td><td>gmin</td><td>9.033414e+01</td><td>False</td><td>9.033414e+01</td><td>--</td><td>--</td><td>--</td><td>1.000000e+01</td><td>1.000000e+03</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>2.489652e+06</td><td>False</td><td>2.489652e+06</td><td>--</td><td>--</td><td>--</td><td>5.000000e+05</td><td>1.000000e+08</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>N</td><td>7.466404e+00</td><td>False</td><td>7.466404e+00</td><td>--</td><td>--</td><td>--</td><td>1.000000e-05</td><td>1.000000e+05</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gamma0_log_parab</td><td>1.025100e+03</td><td>False</td><td>1.025100e+03</td><td>--</td><td>--</td><td>--</td><td>1.000000e+03</td><td>5.000000e+05</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>s</td><td>1.992998e+00</td><td>False</td><td>1.992998e+00</td><td>--</td><td>--</td><td>--</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>r</td><td>1.830227e-01</td><td>False</td><td>1.830227e-01</td><td>--</td><td>--</td><td>--</td><td>-1.500000e+01</td><td>1.500000e+01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R</td><td>3.192345e+16</td><td>False</td><td>3.192345e+16</td><td>--</td><td>--</td><td>--</td><td>3.162278e+15</td><td>3.162278e+17</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>9.197306e-03</td><td>False</td><td>9.197306e-03</td><td>--</td><td>--</td><td>--</td><td>1.000000e-03</td><td>1.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>3.428535e+01</td><td>False</td><td>3.428535e+01</td><td>--</td><td>--</td><td>--</td><td>5.000000e+00</td><td>5.000000e+01</td><td>False</td></tr>
    <tr><td>host_galaxy</td><td>nuFnu_p_host</td><td>-9.966824e+00</td><td>False</td><td>-9.966824e+00</td><td>--</td><td>--</td><td>--</td><td>-1.219011e+01</td><td>-8.190114e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R_H</td><td>1.000000e+17</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>1.000000e+00</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>3.360000e-02</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>host_galaxy</td><td>nu_scale</td><td>-2.290459e-03</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>-2.000000e+00</td><td>2.000000e+00</td><td>True</td></tr>
    </table></div>



.. code:: ipython3

    mcmc.model.jet_leptonic.parameters.freeze_all()
    mcmc.model.host_galaxy.parameters.freeze_all()
    mcmc.model.jet_leptonic.parameters.N.free()
    mcmc.model.jet_leptonic.parameters.B.free()
    mcmc.model.jet_leptonic.parameters.s.free()
    mcmc.model.jet_leptonic.parameters.beam_obj.free()
    mcmc.model.jet_leptonic.parameters.gamma0_log_parab.free()
    mcmc.model.host_galaxy.parameters.nuFnu_p_host.free()


You can set different bounds for all the free parameters with the
instruction below. In case you want to preserve the ``fit_range``
defined in the frequentist minimizer you can pass
``preserve_fit_range=True``

.. note::
    Please, read the introduction and the caveats :ref:`for the Bayesian model fitting <bayesian_model_fitting>` to understand better the bound settings.

.. code:: ipython3

    mcmc.set_bounds(bound=5.0,bound_rel=True)


.. parsed-literal::

    par: N  ref value:  7.466404395214112  mcmc bounds: [1e-05, np.float64(44.79842637128467)]
    par: gamma0_log_parab  ref value:  1025.1004311603217  mcmc bounds: [1000.0, np.float64(6150.602586961931)]
    par: s  ref value:  1.9929979245296892  mcmc bounds: [np.float64(-7.971991698118757), 10]
    par: B  ref value:  0.009197305554840484  mcmc bounds: [0.001, np.float64(0.0551838333290429)]
    par: beam_obj  ref value:  34.285347200656574  mcmc bounds: [5.0, 50.0]
    par: nuFnu_p_host  ref value:  -9.966823825636622  mcmc bounds: [np.float64(-12.190114433378794), np.float64(-8.190114433378794)]


Or, you can set per-parameter bounds as follows, passing the actual
bound for each parameter.

.. code:: ipython3

    mcmc.set_bounds(comp_name='jet_leptonic',par_name='N',par_bounds=[1E-5,10])
    mcmc.set_bounds(comp_name='jet_leptonic',par_name='s',par_bounds=[0,3])
    mcmc.set_bounds(comp_name='jet_leptonic',par_name='beam_obj',par_bounds=[10,50])


.. parsed-literal::

    par: N  ref value:  7.466404395214112  mcmc bounds: [1e-05, 10]
    par: s  ref value:  1.9929979245296892  mcmc bounds: [0, 3]
    par: beam_obj  ref value:  34.285347200656574  mcmc bounds: [10, 50]


.. code:: ipython3

    mcmc.sampler_parameters




.. raw:: html

    <div><i>Table length=14</i>
    <table id="table13792295920" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>val</th><th>log</th><th>bestfit mcmc</th><th>q16</th><th>q50</th><th>q84</th><th>mcmc bound min</th><th>mcmc bound max</th><th>frozen</th></tr></thead>
    <thead><tr><th>str12</th><th>str16</th><th>float64</th><th>bool</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>bool</th></tr></thead>
    <tr><td>jet_leptonic</td><td>N</td><td>7.466404e+00</td><td>False</td><td>7.466404e+00</td><td>--</td><td>--</td><td>--</td><td>1.000000e-05</td><td>1.000000e+01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gamma0_log_parab</td><td>1.025100e+03</td><td>False</td><td>1.025100e+03</td><td>--</td><td>--</td><td>--</td><td>1.000000e+03</td><td>6.150603e+03</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>s</td><td>1.992998e+00</td><td>False</td><td>1.992998e+00</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>3.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>9.197306e-03</td><td>False</td><td>9.197306e-03</td><td>--</td><td>--</td><td>--</td><td>1.000000e-03</td><td>5.518383e-02</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>3.428535e+01</td><td>False</td><td>3.428535e+01</td><td>--</td><td>--</td><td>--</td><td>1.000000e+01</td><td>5.000000e+01</td><td>False</td></tr>
    <tr><td>host_galaxy</td><td>nuFnu_p_host</td><td>-9.966824e+00</td><td>False</td><td>-9.966824e+00</td><td>--</td><td>--</td><td>--</td><td>-1.219011e+01</td><td>-8.190114e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmin</td><td>9.033414e+01</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>1.000000e+01</td><td>1.000000e+03</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>2.489652e+06</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>5.000000e+05</td><td>1.000000e+08</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>r</td><td>1.830227e-01</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>-1.500000e+01</td><td>1.500000e+01</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>R</td><td>3.192345e+16</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>3.162278e+15</td><td>3.162278e+17</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>R_H</td><td>1.000000e+17</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>1.000000e+00</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>3.360000e-02</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>host_galaxy</td><td>nu_scale</td><td>-2.290459e-03</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>-2.000000e+00</td><td>2.000000e+00</td><td>True</td></tr>
    </table></div>



.. code:: ipython3

    mcmc.run_sampler(nwalkers=20, burnin=50,steps=500,progress='notebook')


.. parsed-literal::

    mcmc run starting
    



.. parsed-literal::

      0%|          | 0/500 [00:00<?, ?it/s]


.. parsed-literal::

    mcmc run done, with 1 threads took 12.56 seconds
    ----------------------------
    MCMC best fit solution
    comp: jet_leptonic par: N  mcmc best fit val: 8.050261732718415 quantiles(0.16,0.5,0.84): [6.77511338 7.51635318 8.44441749] 
    comp: jet_leptonic par: gamma0_log_parab  mcmc best fit val: 1346.782810675103 quantiles(0.16,0.5,0.84): [1042.26939882 1174.73924909 1504.84958596] 
    comp: jet_leptonic par: s  mcmc best fit val: 2.0348056168174913 quantiles(0.16,0.5,0.84): [1.98657806 2.01090207 2.04948965] 
    comp: jet_leptonic par: B  mcmc best fit val: 0.00943502856509033 quantiles(0.16,0.5,0.84): [0.00785478 0.00930652 0.01040515] 
    comp: jet_leptonic par: beam_obj  mcmc best fit val: 33.6879642878781 quantiles(0.16,0.5,0.84): [31.70783306 34.06849488 37.61874842] 
    comp: host_galaxy par: nuFnu_p_host  mcmc best fit val: -9.960245489412703 quantiles(0.16,0.5,0.84): [-10.00294203  -9.96928318  -9.94034241] 
    ----------------------------


.. code:: ipython3

    print(mcmc.acceptance_fraction)


.. parsed-literal::

    0.4126


post-run tuning of the burnin
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

We plot the chains, before and after, tuning the burnin (using
autocorrelation time of emcee).

.. code:: ipython3

    p=mcmc.plot_chain()



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_67_0.png



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_67_1.png


.. code:: ipython3

    mcmc.tune_burnin(tau_coeff=2)
    p=mcmc.plot_chain()


.. parsed-literal::

    ----------------------------
    MCMC best fit solution
    comp: jet_leptonic par: N  mcmc best fit val: 8.050261732718415 quantiles(0.16,0.5,0.84): [6.77279204 7.61075599 8.57158292] 
    comp: jet_leptonic par: gamma0_log_parab  mcmc best fit val: 1346.782810675103 quantiles(0.16,0.5,0.84): [1063.1515008  1215.6882804  1557.66984062] 
    comp: jet_leptonic par: s  mcmc best fit val: 2.0348056168174913 quantiles(0.16,0.5,0.84): [1.99088485 2.01653235 2.05520383] 
    comp: jet_leptonic par: B  mcmc best fit val: 0.00943502856509033 quantiles(0.16,0.5,0.84): [0.00783274 0.00933356 0.01047243] 
    comp: jet_leptonic par: beam_obj  mcmc best fit val: 33.6879642878781 quantiles(0.16,0.5,0.84): [31.58476942 33.9231705  37.67056056] 
    comp: host_galaxy par: nuFnu_p_host  mcmc best fit val: -9.960245489412703 quantiles(0.16,0.5,0.84): [-10.00273505  -9.96820057  -9.94027067] 
    ----------------------------



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_68_1.png



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_68_2.png


The updated burnin value has increased compared to the initial one, now
the chain samples a parameter space closer to the convergence.

plotting the posterior corner plot
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To have a better rendering on the scatter plot, we redefine the plot
labels

.. code:: ipython3

    mcmc.set_plot_label('N',r'$N$',comp_name='jet_leptonic')
    mcmc.set_plot_label('B',r'$B$',comp_name='jet_leptonic')
    mcmc.set_plot_label('beam_obj',r'$\delta$',comp_name='jet_leptonic')
    mcmc.set_plot_label('s',r'$s$',comp_name='jet_leptonic')
    mcmc.set_plot_label('gamma0_log_parab',r'$\gamma_0$',comp_name='jet_leptonic')
    mcmc.set_plot_label('nuFnu_p_host',r'$\nu F_{\nu_p} {\rm host}$',comp_name='host_galaxy')

the code below lets you tuning the output:

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



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_75_0.png



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_75_1.png


Global corner plot, pass ``per_component=False``

.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 70
    f=mcmc.corner_plot(quantiles=(0.16, 0.5, 0.84),title_kwargs=dict(fontsize=12),title_fmt=".2E",use_math_text=True,per_component=False)



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_77_0.png


plotting the model
~~~~~~~~~~~~~~~~~~

To plot the sampled model range against the mcmc best-fit model

.. code:: ipython3

    
    from jetset.data_loader import ObsData
    mpl.rcParams['figure.dpi'] = 80
    p=mcmc.plot_model(sed_data=sed_data,size=100)
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_80_0.png


To plot the mcmc best-fit model providing quantiles. The size parameter
defines the number of samples to use to extract the quantiles. I am
using a size equal to the number of steps

.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    p=mcmc.plot_model(sed_data=sed_data,size=100,quantiles=[0.05,0.95])
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_82_0.png


If you want to plot also the model components, pass the argument
``plot_components=True``

.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    p=mcmc.plot_model(sed_data=sed_data,size=500,quantiles=[0.05,0.95],plot_components=True)
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_84_0.png


To plot the frequentist best-fit model range,p roviding quantiles,
provide (``plot_mcmc_best_fit_model==False``)

.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 100
    p=mcmc.plot_model(sed_data=sed_data,size=500,quantiles=[0.05,0.95], plot_mcmc_best_fit_model=False,plot_components=True)
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_86_0.png


plotting chains and individual posteriors
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: ipython3

    import matplotlib.pylab as plt
    
    mpl.rcParams['figure.dpi'] = 80
    f=mcmc.plot_chain(par_name='s',log_plot=False)
    plt.tight_layout()



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_88_0.png



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_88_1.png


.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    f=mcmc.plot_chain(log_plot=False)
    plt.tight_layout()



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_89_0.png



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_89_1.png


.. code:: ipython3

    
    f=mcmc.plot_par('beam_obj',figsize=(8,6))
    mpl.rcParams['figure.dpi'] = 80



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_90_0.png


.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    f=mcmc.plot_par('gamma0_log_parab',log_plot=True,figsize=(8,6))



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_91_0.png


Save and reuse MCMC
-------------------

.. code:: ipython3

    mcmc.save('mcmc_sampler.pkl')

.. code:: ipython3

    from jetset.mcmc import McmcSampler
    from jetset.data_loader import ObsData
    
    sed_data=ObsData.load('Mrk_501.pkl')
    ms=McmcSampler.load('mcmc_sampler.pkl')
    
    import matplotlib as mpl


.. code:: ipython3

    ms.sampler_parameters




.. raw:: html

    <div><i>Table length=14</i>
    <table id="table13798601744" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>val</th><th>log</th><th>bestfit mcmc</th><th>q16</th><th>q50</th><th>q84</th><th>mcmc bound min</th><th>mcmc bound max</th><th>frozen</th></tr></thead>
    <thead><tr><th>str12</th><th>str16</th><th>float64</th><th>bool</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>bool</th></tr></thead>
    <tr><td>jet_leptonic</td><td>N</td><td>8.050262e+00</td><td>False</td><td>8.050262e+00</td><td>6.772792e+00</td><td>7.610756e+00</td><td>8.571583e+00</td><td>1.000000e-05</td><td>1.000000e+01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gamma0_log_parab</td><td>1.346783e+03</td><td>False</td><td>1.346783e+03</td><td>1.063152e+03</td><td>1.215688e+03</td><td>1.557670e+03</td><td>1.000000e+03</td><td>6.150603e+03</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>s</td><td>2.034806e+00</td><td>False</td><td>2.034806e+00</td><td>1.990885e+00</td><td>2.016532e+00</td><td>2.055204e+00</td><td>0.000000e+00</td><td>3.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>9.435029e-03</td><td>False</td><td>9.435029e-03</td><td>7.832744e-03</td><td>9.333557e-03</td><td>1.047243e-02</td><td>1.000000e-03</td><td>5.518383e-02</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>3.368796e+01</td><td>False</td><td>3.368796e+01</td><td>3.158477e+01</td><td>3.392317e+01</td><td>3.767056e+01</td><td>1.000000e+01</td><td>5.000000e+01</td><td>False</td></tr>
    <tr><td>host_galaxy</td><td>nuFnu_p_host</td><td>-9.960245e+00</td><td>False</td><td>-9.960245e+00</td><td>-1.000274e+01</td><td>-9.968201e+00</td><td>-9.940271e+00</td><td>-1.219011e+01</td><td>-8.190114e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmin</td><td>9.033414e+01</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>1.000000e+01</td><td>1.000000e+03</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>2.489652e+06</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>5.000000e+05</td><td>1.000000e+08</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>r</td><td>1.830227e-01</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>-1.500000e+01</td><td>1.500000e+01</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>R</td><td>3.192345e+16</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>3.162278e+15</td><td>3.162278e+17</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>R_H</td><td>1.000000e+17</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>1.000000e+00</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>3.360000e-02</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>host_galaxy</td><td>nu_scale</td><td>-2.290459e-03</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>-2.000000e+00</td><td>2.000000e+00</td><td>True</td></tr>
    </table></div>



.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    p=ms.plot_model(sed_data=sed_data,size=500,plot_components=True)
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_96_0.png


.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    p=ms.plot_model(sed_data=sed_data,size=100,quantiles=[0.05,0.95],plot_components=True)
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_97_0.png


.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    p=ms.plot_model(sed_data=sed_data,size=100,quantiles=[0.05,0.95],plot_mcmc_best_fit_model=True)
    p=ms.model.plot_model(plot_obj=p)
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_98_0.png


Per-component corner plot

.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    f=ms.corner_plot(quantiles=(0.16, 0.5, 0.84),title_kwargs=dict(fontsize=12),title_fmt=".2E",use_math_text=True)



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_100_0.png



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_100_1.png


Global corner plot, pass ``per_component=False``

.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    f=ms.corner_plot(quantiles=(0.16, 0.5, 0.84),title_kwargs=dict(fontsize=12),title_fmt=".2E",use_math_text=True,per_component=False)



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_102_0.png


.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    f=ms.plot_par('beam_obj',log_plot=False,figsize=(8,6))



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_103_0.png


.. code:: ipython3

    f=ms.plot_par('B',log_plot=True,figsize=(8,6))



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_104_0.png


.. code:: ipython3

    from matplotlib import pylab as plt
    mpl.rcParams['figure.dpi'] = 80
    f=ms.plot_chain(par_name='s',comp_name='jet_leptonic',log_plot=False)
    plt.tight_layout()



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_105_0.png


.. code:: ipython3

    f=ms.plot_chain(log_plot=False)
    plt.tight_layout()
    mpl.rcParams['figure.dpi'] = 80



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_106_0.png



.. image:: Jet_example_model_fit_wiht_gal_template_files/Jet_example_model_fit_wiht_gal_template_106_1.png

