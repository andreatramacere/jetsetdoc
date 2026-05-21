.. _model_fitting_ssc_only_ultranest_openmpi:

Model fitting 2: Only SSC, MCMC without minimizer step and ultranest backend
============================================================================

.. important::
   To run this notebook, install ``ultranest>=4.0``.

   - pip: ``pip install "ultranest>=4.0" h5py``
   - conda: ``conda install -c conda-forge "ultranest>=4.0" h5py``


.. important::
   To run ultranest via  Open MPI, you have to install
     - mpi4py (pip/mamba install mpi4py)
     - OpenMPI (https://docs.open-mpi.org/en/main/installing-open-mpi/quickstart.html#binary-packages)

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
    
    sed_data.add_systematics(0.1,[10.**6,10.**16])
    p=sed_data.plot_sed()
    #p.setlim(y_min=1E-15,x_min=1E7,x_max=1E29)


.. parsed-literal::

    ================================================================================
    
    ***  binning data  ***
    ---> N bins= 88
    ---> bin_width= 0.2
    ================================================================================
    



.. image:: Jet_example_model_fit_only_ultranest_plain_and_openmpi_files/Jet_example_model_fit_only_ultranest_plain_and_openmpi_10_1.png


.. code:: ipython3

    sed_data.save('Mrk_401.pkl')

creating and setting the sampler
--------------------------------

.. code:: ipython3

    
    from jetset.mcmc_ultranest import UltraNestSampler
    from jetset.minimizer import ModelMinimizer


.. code:: ipython3

    from jetset.jet_model import Jet
    from jetset.model_manager import FitModel
    fit_model=FitModel(jet=Jet(emitters_distribution='plc'),name='plain_plc')


.. parsed-literal::

    /Users/orion/miniforge3/envs/jetset/lib/python3.12/site-packages/jetset/model_manager.py:259: UserWarning: no cosmology defined, using the one from jet FlatLambdaCDM(name="Planck13", H0=67.77 km / (Mpc s), Om0=0.30712, Tcmb0=2.7255 K, Neff=3.046, m_nu=[0.   0.   0.06] eV, Ob0=0.048252)
      warnings.warn(m)


We create a ``ModelMinimizer`` object directly for the MCMC step,
skipping the ``.run()`` method, replaced by the call of the
``prepare_fit`` method.

.. code:: ipython3

    model_minimizer = model_minimizer=ModelMinimizer('mcmc')
    model_minimizer.prepare_fit(fit_model,
                                sed_data,
                                nu_fit_start=1E11,
                                nu_fit_stop=1E29,
                                use_UL=True)
    



.. parsed-literal::

    filtering data in fit range = [1.000000e+11,1.000000e+29]
    data length 34


to speed up the utlranest sampler we make parameters logarithmic:

.. code:: ipython3

    model_minimizer.fit_model.jet_leptonic.parameters.R.make_log()
    model_minimizer.fit_model.jet_leptonic.parameters.B.make_log()
    model_minimizer.fit_model.jet_leptonic.parameters.N.make_log()
    model_minimizer.fit_model.jet_leptonic.parameters.gmax.make_log()
    model_minimizer.fit_model.jet_leptonic.parameters.gmin.make_log()
    model_minimizer.fit_model.jet_leptonic.parameters.gamma_cut.make_log()
    model_minimizer.fit_model.jet_leptonic.parameters.z_cosm.freeze()



.. parsed-literal::

    /Users/orion/miniforge3/envs/jetset/lib/python3.12/site-packages/jetset/model_parameters.py:977: RuntimeWarning: divide by zero encountered in log10
      return np.log10(v)


.. code:: ipython3

    model_minimizer.fit_model.parameters


.. parsed-literal::

    WARNING: AstropyDeprecationWarning: 'classic' backend for show_in_notebook() is deprecated as of 6.1. Instead, use the supported backend 'ipydatagrid'. [astropy.table.table]



.. raw:: html

    <i>Table length=11</i>
    <table id="table13407993152-608502" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>par type</th><th>units</th><th>val</th><th>phys. bound. min</th><th>phys. bound. max</th><th>log</th><th>frozen</th></tr></thead>
    <tr><td>jet_leptonic</td><td>R</td><td>region_size</td><td>cm</td><td>1.569897e+01</td><td>3.000000e+00</td><td>3.000000e+01</td><td>True</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R_H</td><td>region_position</td><td>cm</td><td>1.000000e+17</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>magnetic_field</td><td>gauss</td><td>-1.000000e+00</td><td>-inf</td><td>--</td><td>True</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>cold_p_to_rel_e_ratio</td><td></td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>beaming</td><td></td><td>1.000000e+01</td><td>1.000000e-04</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>redshift</td><td></td><td>1.000000e-01</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>gmin</td><td>low-energy-cut-off</td><td>lorentz-factor*</td><td>3.010300e-01</td><td>0.000000e+00</td><td>9.000000e+00</td><td>True</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>high-energy-cut-off</td><td>lorentz-factor*</td><td>6.000000e+00</td><td>0.000000e+00</td><td>1.500000e+01</td><td>True</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>N</td><td>emitters_density</td><td>1 / cm3</td><td>2.000000e+00</td><td>-inf</td><td>--</td><td>True</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gamma_cut</td><td>turn-over-energy</td><td>lorentz-factor*</td><td>4.000000e+00</td><td>0.000000e+00</td><td>9.000000e+00</td><td>True</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>p</td><td>LE_spectral_slope</td><td></td><td>2.000000e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
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
        console.log("$('#table13407993152-608502').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table13407993152-608502').dataTable({
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

    model_minimizer.fit_model.eval()
    p=model_minimizer.fit_model.plot_model(sed_data=sed_data)



.. image:: Jet_example_model_fit_only_ultranest_plain_and_openmpi_files/Jet_example_model_fit_only_ultranest_plain_and_openmpi_20_0.png


.. code:: ipython3

    
    mcmc=UltraNestSampler(model_minimizer)

.. code:: ipython3

    mcmc.model.parameters


.. parsed-literal::

    WARNING: AstropyDeprecationWarning: 'classic' backend for show_in_notebook() is deprecated as of 6.1. Instead, use the supported backend 'ipydatagrid'. [astropy.table.table]



.. raw:: html

    <i>Table length=11</i>
    <table id="table13417445952-894586" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>par type</th><th>units</th><th>val</th><th>phys. bound. min</th><th>phys. bound. max</th><th>log</th><th>frozen</th></tr></thead>
    <tr><td>jet_leptonic</td><td>gmin</td><td>low-energy-cut-off</td><td>lorentz-factor*</td><td>3.010300e-01</td><td>0.000000e+00</td><td>9.000000e+00</td><td>True</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>high-energy-cut-off</td><td>lorentz-factor*</td><td>6.000000e+00</td><td>0.000000e+00</td><td>1.500000e+01</td><td>True</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>N</td><td>emitters_density</td><td>1 / cm3</td><td>2.000000e+00</td><td>-inf</td><td>--</td><td>True</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gamma_cut</td><td>turn-over-energy</td><td>lorentz-factor*</td><td>4.000000e+00</td><td>0.000000e+00</td><td>9.000000e+00</td><td>True</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>p</td><td>LE_spectral_slope</td><td></td><td>2.000000e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R</td><td>region_size</td><td>cm</td><td>1.569897e+01</td><td>3.000000e+00</td><td>3.000000e+01</td><td>True</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R_H</td><td>region_position</td><td>cm</td><td>1.000000e+17</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>magnetic_field</td><td>gauss</td><td>-1.000000e+00</td><td>-inf</td><td>--</td><td>True</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>cold_p_to_rel_e_ratio</td><td></td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>beaming</td><td></td><td>1.000000e+01</td><td>1.000000e-04</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>redshift</td><td></td><td>1.000000e-01</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
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
        console.log("$('#table13417445952-894586').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table13417445952-894586').dataTable({
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

    mcmc.model.eval()
    mcmc.model.plot_model(sed_data=sed_data)




.. parsed-literal::

    <jetset.plot_sedfit.PlotSED at 0x11fad9be0>




.. image:: Jet_example_model_fit_only_ultranest_plain_and_openmpi_files/Jet_example_model_fit_only_ultranest_plain_and_openmpi_23_1.png


You can inspect the mcmc parameters using:

.. code:: ipython3

    mcmc.sampler_parameters




.. raw:: html

    <div><i>Table length=11</i>
    <table id="table13419434048" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>val</th><th>log</th><th>bestfit mcmc</th><th>q16</th><th>q50</th><th>q84</th><th>mcmc bound min</th><th>mcmc bound max</th><th>frozen</th></tr></thead>
    <thead><tr><th>str12</th><th>str16</th><th>float64</th><th>bool</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>bool</th></tr></thead>
    <tr><td>jet_leptonic</td><td>gmin</td><td>3.010300e-01</td><td>True</td><td>3.010300e-01</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>9.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>6.000000e+00</td><td>True</td><td>6.000000e+00</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>1.500000e+01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>N</td><td>2.000000e+00</td><td>True</td><td>2.000000e+00</td><td>--</td><td>--</td><td>--</td><td>-inf</td><td>--</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gamma_cut</td><td>4.000000e+00</td><td>True</td><td>4.000000e+00</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>9.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>p</td><td>2.000000e+00</td><td>False</td><td>2.000000e+00</td><td>--</td><td>--</td><td>--</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R</td><td>1.569897e+01</td><td>True</td><td>1.569897e+01</td><td>--</td><td>--</td><td>--</td><td>3.000000e+00</td><td>3.000000e+01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>-1.000000e+00</td><td>True</td><td>-1.000000e+00</td><td>--</td><td>--</td><td>--</td><td>-inf</td><td>--</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>1.000000e+01</td><td>False</td><td>1.000000e+01</td><td>--</td><td>--</td><td>--</td><td>1.000000e-04</td><td>--</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R_H</td><td>1.000000e+17</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>1.000000e+00</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>1.000000e-01</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    </table></div>



.. code:: ipython3

    mcmc.model.parameters.best_fit_par_table




.. raw:: html

    <div><i>Table length=11</i>
    <table id="table13420870736" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>val</th><th>bestfit val</th><th>err +</th><th>err -</th><th>start val</th><th>fit range min</th><th>fit range max</th><th>frozen</th></tr></thead>
    <thead><tr><th>str12</th><th>str16</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>bool</th></tr></thead>
    <tr><td>jet_leptonic</td><td>gmin</td><td>3.010300e-01</td><td>--</td><td>--</td><td>--</td><td>3.010300e-01</td><td>0.000000e+00</td><td>9.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>6.000000e+00</td><td>--</td><td>--</td><td>--</td><td>6.000000e+00</td><td>0.000000e+00</td><td>1.500000e+01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>N</td><td>2.000000e+00</td><td>--</td><td>--</td><td>--</td><td>2.000000e+00</td><td>-inf</td><td>--</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gamma_cut</td><td>4.000000e+00</td><td>--</td><td>--</td><td>--</td><td>4.000000e+00</td><td>0.000000e+00</td><td>9.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>p</td><td>2.000000e+00</td><td>--</td><td>--</td><td>--</td><td>2.000000e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R</td><td>1.569897e+01</td><td>--</td><td>--</td><td>--</td><td>1.569897e+01</td><td>3.000000e+00</td><td>3.000000e+01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R_H</td><td>1.000000e+17</td><td>--</td><td>--</td><td>--</td><td>1.000000e+17</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>-1.000000e+00</td><td>--</td><td>--</td><td>--</td><td>-1.000000e+00</td><td>-inf</td><td>--</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>1.000000e+00</td><td>--</td><td>--</td><td>--</td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>1.000000e+01</td><td>--</td><td>--</td><td>--</td><td>1.000000e+01</td><td>1.000000e-04</td><td>--</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>1.000000e-01</td><td>--</td><td>--</td><td>--</td><td>1.000000e-01</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    </table></div>



If you want to exclude or include parameters from the samplers just
freeze/free them


.. code:: ipython3

    mcmc.model.jet_leptonic.parameters.gmax.freeze()
    mcmc.model.jet_leptonic.parameters.gmax.val=7
    
    mcmc.set_bounds(par_name='p',comp_name='jet_leptonic',par_bounds=[1.5,3.5])
    mcmc.set_bounds(par_name='N',comp_name='jet_leptonic',par_bounds=[-3,3])
    mcmc.set_bounds(par_name='R',comp_name='jet_leptonic',par_bounds=[15,17])
    mcmc.set_bounds(par_name='beam_obj',comp_name='jet_leptonic',par_bounds=[10,50])
    
    
    mcmc.set_bounds(par_name='gmin',comp_name='jet_leptonic',par_bounds=[0,3.3])
    mcmc.set_bounds(par_name='gamma_cut',comp_name='jet_leptonic',par_bounds=[3,6])
    mcmc.set_bounds(par_name='B',comp_name='jet_leptonic',par_bounds=[-3,0])
     



.. parsed-literal::

    par: p  ref value:  2.0  mcmc bounds: [1.5, 3.5]
    par: N  ref value:  2.0  mcmc bounds: [np.float64(-3.0), np.float64(3.0)]
    par: R  ref value:  15.698970004336019  mcmc bounds: [np.float64(15.0), np.float64(17.0)]
    par: beam_obj  ref value:  10.0  mcmc bounds: [10, 50]
    par: gmin  ref value:  0.3010299956639812  mcmc bounds: [np.float64(0.0), np.float64(3.3)]
    par: gamma_cut  ref value:  4.0  mcmc bounds: [np.float64(3.0), np.float64(6.0)]
    par: B  ref value:  -1.0  mcmc bounds: [np.float64(-3.0), np.float64(0.0)]


.. code:: ipython3

    mcmc.model.parameters


.. parsed-literal::

    WARNING: AstropyDeprecationWarning: 'classic' backend for show_in_notebook() is deprecated as of 6.1. Instead, use the supported backend 'ipydatagrid'. [astropy.table.table]



.. raw:: html

    <i>Table length=11</i>
    <table id="table13417433328-301360" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>par type</th><th>units</th><th>val</th><th>phys. bound. min</th><th>phys. bound. max</th><th>log</th><th>frozen</th></tr></thead>
    <tr><td>jet_leptonic</td><td>gmin</td><td>low-energy-cut-off</td><td>lorentz-factor*</td><td>3.010300e-01</td><td>0.000000e+00</td><td>9.000000e+00</td><td>True</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>high-energy-cut-off</td><td>lorentz-factor*</td><td>7.000000e+00</td><td>0.000000e+00</td><td>1.500000e+01</td><td>True</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>N</td><td>emitters_density</td><td>1 / cm3</td><td>2.000000e+00</td><td>-inf</td><td>--</td><td>True</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gamma_cut</td><td>turn-over-energy</td><td>lorentz-factor*</td><td>4.000000e+00</td><td>0.000000e+00</td><td>9.000000e+00</td><td>True</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>p</td><td>LE_spectral_slope</td><td></td><td>2.000000e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R</td><td>region_size</td><td>cm</td><td>1.569897e+01</td><td>3.000000e+00</td><td>3.000000e+01</td><td>True</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R_H</td><td>region_position</td><td>cm</td><td>1.000000e+17</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>magnetic_field</td><td>gauss</td><td>-1.000000e+00</td><td>-inf</td><td>--</td><td>True</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>cold_p_to_rel_e_ratio</td><td></td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>beaming</td><td></td><td>1.000000e+01</td><td>1.000000e-04</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>redshift</td><td></td><td>1.000000e-01</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
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
        console.log("$('#table13417433328-301360').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table13417433328-301360').dataTable({
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

    mcmc.model.eval()
    mcmc.model.plot_model(sed_data=sed_data)




.. parsed-literal::

    <jetset.plot_sedfit.PlotSED at 0x31fd0ae40>




.. image:: Jet_example_model_fit_only_ultranest_plain_and_openmpi_files/Jet_example_model_fit_only_ultranest_plain_and_openmpi_31_1.png


.. code:: ipython3

    mcmc.sampler_parameters




.. raw:: html

    <div><i>Table length=11</i>
    <table id="table13440642144" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>val</th><th>log</th><th>bestfit mcmc</th><th>q16</th><th>q50</th><th>q84</th><th>mcmc bound min</th><th>mcmc bound max</th><th>frozen</th></tr></thead>
    <thead><tr><th>str12</th><th>str16</th><th>float64</th><th>bool</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>bool</th></tr></thead>
    <tr><td>jet_leptonic</td><td>gmin</td><td>3.010300e-01</td><td>True</td><td>3.010300e-01</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>3.300000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>N</td><td>2.000000e+00</td><td>True</td><td>2.000000e+00</td><td>--</td><td>--</td><td>--</td><td>-3.000000e+00</td><td>3.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gamma_cut</td><td>4.000000e+00</td><td>True</td><td>4.000000e+00</td><td>--</td><td>--</td><td>--</td><td>3.000000e+00</td><td>6.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>p</td><td>2.000000e+00</td><td>False</td><td>2.000000e+00</td><td>--</td><td>--</td><td>--</td><td>1.500000e+00</td><td>3.500000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R</td><td>1.569897e+01</td><td>True</td><td>1.569897e+01</td><td>--</td><td>--</td><td>--</td><td>1.500000e+01</td><td>1.700000e+01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>-1.000000e+00</td><td>True</td><td>-1.000000e+00</td><td>--</td><td>--</td><td>--</td><td>-3.000000e+00</td><td>0.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>1.000000e+01</td><td>False</td><td>1.000000e+01</td><td>--</td><td>--</td><td>--</td><td>1.000000e+01</td><td>5.000000e+01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>7.000000e+00</td><td>True</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>1.500000e+01</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>R_H</td><td>1.000000e+17</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>1.000000e+00</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>1.000000e-01</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    </table></div>



Running the sampler using Open MPI
----------------------------------

.. important::
   To run ultranest via Open MPI , you have to install
     - mpi4py (pip/mamba install mpi4py)
     - OpenMPI (https://docs.open-mpi.org/en/main/installing-open-mpi/quickstart.html#binary-packages)

.. note::
   The configuration in the cell below is meant for a quick test. For a more robust analysis, please leave ``min_num_live_points``, ``nsteps``, ``max_num_improvement_loops``, and ``min_ess`` to their default values, or read the ultranest documentation to understand how to tune them.


When using ``run_open_mpi``, the root directory, storing the ultranest
products and the mpi script will be ``run_mpi``

The parameter ``ultranest_output_dir``, created under ``run_mpi``, by
default, will be set to ``f'ultranest_{self.model.name}'``. You can
override it by passing the desired name.

Setting ``n_proc=8``, will result in running 8 parallel processes,
speeding up the sampler, increase the ``n_proc`` value up to the number
of cores/cpus depending on your machine/cluster setup

.. code:: ipython3

    from jetset.mcmc_ultranest import run_open_mpi
    
    mcmc=run_open_mpi(mcmc,
                     n_proc=8, #it runs 8 parallel processes, speeding up the sampler, increase n_proc value up to the number of cores/cpus depending on yor machine/cluster setup
                     min_num_live_points=64,
                     nsteps=1,
                     max_num_improvement_loops=1, 
                     min_ess=100,
                     num_c_threads=0, #when using openmpi do not enable c-threads
                     ultranest_output_dir='ultranest_openmpi_runs', #to override the directory storing the ultranest products to be inspected if needed , placed in run_mpi/
    )


.. code-block:: text

    ====== ultranest script ========
    from jetset.mcmc_ultranest import UltraNestSampler
    mcmc=UltraNestSampler.load('sampler.pkl')
    
    mcmc.model.set_num_c_threads(0)
    
    mcmc.run_sampler(
                    min_num_live_points=64,
                    nsteps=1,
                    max_num_improvement_loops=1,
                    min_ess=100.0,
                    ultranest_output_dir='ultranest_openmpi_runs',
                )
        
    mcmc.save('sampler.pkl')
    
    ================================
    executing mpirun  -np 8 python run_jetset_ultranest.py
    Creating directory for new run ultranest_openmpi_runs/run1
    ultranest run starting
    ... [output truncated: first 20 lines shown] ...

.. code:: ipython3

    mcmc.plot_chain()




.. parsed-literal::

    [<Figure size 1000x2100 with 7 Axes>]




.. image:: Jet_example_model_fit_only_ultranest_plain_and_openmpi_files/Jet_example_model_fit_only_ultranest_plain_and_openmpi_40_1.png


.. code:: ipython3

    #mcmc.tune_burnin(tau_cor_coeff=4)

Showing the MCMC parameters. Now MCMC bestfit values are updated to the
best-fit MCMC solution

.. code:: ipython3

    mcmc.sampler_parameters




.. raw:: html

    <div><i>Table length=11</i>
    <table id="table4827433248" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>val</th><th>log</th><th>bestfit mcmc</th><th>q16</th><th>q50</th><th>q84</th><th>mcmc bound min</th><th>mcmc bound max</th><th>frozen</th></tr></thead>
    <thead><tr><th>str12</th><th>str16</th><th>float64</th><th>bool</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>bool</th></tr></thead>
    <tr><td>jet_leptonic</td><td>gmin</td><td>2.693291e+00</td><td>True</td><td>2.693291e+00</td><td>2.661238e+00</td><td>2.739324e+00</td><td>2.817553e+00</td><td>0.000000e+00</td><td>3.300000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>N</td><td>1.341023e-01</td><td>True</td><td>1.341023e-01</td><td>-3.107235e-01</td><td>-1.033240e-01</td><td>1.500047e-01</td><td>-3.000000e+00</td><td>3.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gamma_cut</td><td>5.165258e+00</td><td>True</td><td>5.165258e+00</td><td>5.158018e+00</td><td>5.224758e+00</td><td>5.275885e+00</td><td>3.000000e+00</td><td>6.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>p</td><td>2.197052e+00</td><td>False</td><td>2.197052e+00</td><td>2.176380e+00</td><td>2.193828e+00</td><td>2.211298e+00</td><td>1.500000e+00</td><td>3.500000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R</td><td>1.631844e+01</td><td>True</td><td>1.631844e+01</td><td>1.633102e+01</td><td>1.647837e+01</td><td>1.659093e+01</td><td>1.500000e+01</td><td>1.700000e+01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>-1.113762e+00</td><td>True</td><td>-1.113762e+00</td><td>-1.243494e+00</td><td>-1.170492e+00</td><td>-1.073391e+00</td><td>-3.000000e+00</td><td>0.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>4.798453e+01</td><td>False</td><td>4.798453e+01</td><td>3.859726e+01</td><td>4.188718e+01</td><td>4.562633e+01</td><td>1.000000e+01</td><td>5.000000e+01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>7.000000e+00</td><td>True</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>1.500000e+01</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>R_H</td><td>1.000000e+17</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>1.000000e+00</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>1.000000e-01</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    </table></div>



plotting the posterior corner plot
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To have a better rendering on the scatter plot, we redefine the plot
labels

.. code:: ipython3

    mcmc.set_plot_label('N',r'log($N$)',comp_name='jet_leptonic')
    mcmc.set_plot_label('R',r'log($R$)',comp_name='jet_leptonic')
    mcmc.set_plot_label('B',r'log($B$)',comp_name='jet_leptonic')
    
    mcmc.set_plot_label('gmin',r'log($\gamma_{\rm min}$)',comp_name='jet_leptonic')
    mcmc.set_plot_label('p',r'$p$',comp_name='jet_leptonic')
    #mcmc.set_plot_label('r',r'$r$',comp_name='jet_leptonic')
    mcmc.set_plot_label('gamma_cut',r'$\gamma_{\rm cut}$',comp_name='jet_leptonic')
    mcmc.set_plot_label('beam_obj',r'$\delta$',comp_name='jet_leptonic')
    


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



.. image:: Jet_example_model_fit_only_ultranest_plain_and_openmpi_files/Jet_example_model_fit_only_ultranest_plain_and_openmpi_49_0.png


Global corner plot, pass ``per_component=False``

.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 70
    f=mcmc.corner_plot(quantiles=(0.16, 0.5, 0.84),title_kwargs=dict(fontsize=12),title_fmt=".2E",use_math_text=True,per_component=False)



.. image:: Jet_example_model_fit_only_ultranest_plain_and_openmpi_files/Jet_example_model_fit_only_ultranest_plain_and_openmpi_51_0.png


plotting the model
~~~~~~~~~~~~~~~~~~

To plot the sampled model range against the input mcmc best-fit model

.. code:: ipython3

    
    from jetset.data_loader import ObsData
    sed_data=ObsData.load('Mrk_401.pkl')
    mpl.rcParams['figure.dpi'] = 80
    p=mcmc.plot_model(sed_data=sed_data,size=600)
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_example_model_fit_only_ultranest_plain_and_openmpi_files/Jet_example_model_fit_only_ultranest_plain_and_openmpi_54_0.png


To plot the mcmc best-fit model providing quantiles. The size parameter
defines the number of samples to use to extract the quantiles. I am
using a size equal to the number of steps

.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    p=mcmc.plot_model(sed_data=sed_data,size=100,quantiles=[0.05,0.95])
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_example_model_fit_only_ultranest_plain_and_openmpi_files/Jet_example_model_fit_only_ultranest_plain_and_openmpi_56_0.png


If you want to plot also the model components, pass the argument
``plot_components=True``

.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    p=mcmc.plot_model(sed_data=sed_data,size=500,quantiles=[0.05,0.95],plot_components=True)
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)




.. image:: Jet_example_model_fit_only_ultranest_plain_and_openmpi_files/Jet_example_model_fit_only_ultranest_plain_and_openmpi_58_0.png


To plot the frequentist best-fit model range,p roviding quantiles,
provide (``plot_mcmc_best_fit_model=False``)

.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 100
    p=mcmc.plot_model(sed_data=sed_data,size=500,quantiles=[0.05,0.95], plot_mcmc_best_fit_model=False,plot_components=True)
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_example_model_fit_only_ultranest_plain_and_openmpi_files/Jet_example_model_fit_only_ultranest_plain_and_openmpi_60_0.png


plotting chains and individual posteriors
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: ipython3

    import matplotlib.pylab as plt
    import matplotlib as mpl
    
    mpl.rcParams['figure.dpi'] = 80
    f=mcmc.plot_chain(par_name='p',log_plot=False)
    plt.tight_layout()



.. image:: Jet_example_model_fit_only_ultranest_plain_and_openmpi_files/Jet_example_model_fit_only_ultranest_plain_and_openmpi_62_0.png


.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    f=mcmc.plot_chain(log_plot=False)
    plt.tight_layout()



.. image:: Jet_example_model_fit_only_ultranest_plain_and_openmpi_files/Jet_example_model_fit_only_ultranest_plain_and_openmpi_63_0.png


Saving and reloading the sampler
--------------------------------

.. code:: ipython3

    mcmc.save('mcmc_sampler_ultranest_plain_openmpi.pkl')

.. code:: ipython3

    from jetset.mcmc import McmcSampler
    from jetset.data_loader import ObsData
    
    sed_data=ObsData.load('Mrk_401.pkl')
    ms=McmcSampler.load('mcmc_sampler_ultranest_plain_openmpi.pkl')
    
    import matplotlib as mpl


.. code:: ipython3

    ms.sampler_parameters




.. raw:: html

    <div><i>Table length=11</i>
    <table id="table13642410432" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>val</th><th>log</th><th>bestfit mcmc</th><th>q16</th><th>q50</th><th>q84</th><th>mcmc bound min</th><th>mcmc bound max</th><th>frozen</th></tr></thead>
    <thead><tr><th>str12</th><th>str16</th><th>float64</th><th>bool</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>bool</th></tr></thead>
    <tr><td>jet_leptonic</td><td>gmin</td><td>2.693291e+00</td><td>True</td><td>2.693291e+00</td><td>2.661238e+00</td><td>2.739324e+00</td><td>2.817553e+00</td><td>0.000000e+00</td><td>3.300000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>N</td><td>1.341023e-01</td><td>True</td><td>1.341023e-01</td><td>-3.107235e-01</td><td>-1.033240e-01</td><td>1.500047e-01</td><td>-3.000000e+00</td><td>3.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gamma_cut</td><td>5.165258e+00</td><td>True</td><td>5.165258e+00</td><td>5.158018e+00</td><td>5.224758e+00</td><td>5.275885e+00</td><td>3.000000e+00</td><td>6.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>p</td><td>2.197052e+00</td><td>False</td><td>2.197052e+00</td><td>2.176380e+00</td><td>2.193828e+00</td><td>2.211298e+00</td><td>1.500000e+00</td><td>3.500000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R</td><td>1.631844e+01</td><td>True</td><td>1.631844e+01</td><td>1.633102e+01</td><td>1.647837e+01</td><td>1.659093e+01</td><td>1.500000e+01</td><td>1.700000e+01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>-1.113762e+00</td><td>True</td><td>-1.113762e+00</td><td>-1.243494e+00</td><td>-1.170492e+00</td><td>-1.073391e+00</td><td>-3.000000e+00</td><td>0.000000e+00</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>beam_obj</td><td>4.798453e+01</td><td>False</td><td>4.798453e+01</td><td>3.859726e+01</td><td>4.188718e+01</td><td>4.562633e+01</td><td>1.000000e+01</td><td>5.000000e+01</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>7.000000e+00</td><td>True</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>1.500000e+01</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>R_H</td><td>1.000000e+17</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>1.000000e+00</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>1.000000e-01</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    </table></div>



.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    p=ms.plot_model(sed_data=sed_data,size=500,plot_components=True)
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_example_model_fit_only_ultranest_plain_and_openmpi_files/Jet_example_model_fit_only_ultranest_plain_and_openmpi_68_0.png


.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    p=ms.plot_model(sed_data=sed_data,size=100,quantiles=[0.05,0.95],plot_components=True)
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_example_model_fit_only_ultranest_plain_and_openmpi_files/Jet_example_model_fit_only_ultranest_plain_and_openmpi_69_0.png


.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    p=ms.plot_model(sed_data=sed_data,size=100,quantiles=[0.05,0.95],plot_mcmc_best_fit_model=True)
    p=ms.model.plot_model(plot_obj=p)
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_example_model_fit_only_ultranest_plain_and_openmpi_files/Jet_example_model_fit_only_ultranest_plain_and_openmpi_70_0.png


Per-component corner plot

.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    f=ms.corner_plot(quantiles=(0.16, 0.5, 0.84),title_kwargs=dict(fontsize=12),title_fmt=".2E",use_math_text=True)



.. image:: Jet_example_model_fit_only_ultranest_plain_and_openmpi_files/Jet_example_model_fit_only_ultranest_plain_and_openmpi_72_0.png


Global corner plot, pass ``per_component=False``

.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    f=ms.corner_plot(quantiles=(0.16, 0.5, 0.84),title_kwargs=dict(fontsize=12),title_fmt=".2E",use_math_text=True,per_component=False)



.. image:: Jet_example_model_fit_only_ultranest_plain_and_openmpi_files/Jet_example_model_fit_only_ultranest_plain_and_openmpi_74_0.png

