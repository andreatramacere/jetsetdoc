.. code:: ipython3

    import warnings
    warnings.filterwarnings('ignore')

.. code:: ipython3

    import jetset
    print(jetset.__version__)


.. parsed-literal::

    1.4.0rc3


.. _model_fitting_lept_eq_EC:

Model fitting: External Compton with leptonic equilibrium.
==========================================================

Loading data
------------

see the :ref:`data_format` user guide for further information about loading data and :ref:`jet_physical_guide_EC` for the information regarding the implementation of the external Conpton model

.. code:: ipython3

    from jetset.data_loader import Data,ObsData
    from jetset.test_data_helper import  test_SEDs
    test_SEDs





.. parsed-literal::

    ['/Users/orion/miniforge3/envs/jetset/lib/python3.12/site-packages/jetset/test_data/SEDs_data/SED_3C345.ecsv',
     '/Users/orion/miniforge3/envs/jetset/lib/python3.12/site-packages/jetset/test_data/SEDs_data/SED_MW_Mrk421_EBL_DEABS.ecsv',
     '/Users/orion/miniforge3/envs/jetset/lib/python3.12/site-packages/jetset/test_data/SEDs_data/SED_MW_Mrk501_EBL_ABS.ecsv',
     '/Users/orion/miniforge3/envs/jetset/lib/python3.12/site-packages/jetset/test_data/SEDs_data/SED_MW_Mrk501_EBL_DEABS.ecsv']



.. code:: ipython3

    data=Data.from_file(test_SEDs[0])


.. code:: ipython3

    t=data.table[data.table['dataset']!='-1']

.. code:: ipython3

    t=t[t['dataset']!='2']

.. code:: ipython3

    t.write('~/3C454.3.ecsv',overwrite=True)

.. code:: ipython3

    sed_data=ObsData(data_table=data)

.. code:: ipython3

    %matplotlib inline
    p=sed_data.plot_sed(show_dataset=True)



.. image:: Jet_example_model_fit_leptonic_eq_EC_files/Jet_example_model_fit_leptonic_eq_EC_12_0.png


we filter out the data set ``-1``

.. code:: ipython3

    sed_data.show_data_sets()
    sed_data.filter_data_set('-1',exclude=True)
    sed_data.filter_data_set('2',exclude=True)
    sed_data.show_data_sets()
    p=sed_data.plot_sed()



.. parsed-literal::

    current datasets
    dataset -1
    dataset 0
    dataset 1
    dataset 2
    ---> excluding  dataset/s ['-1']
    filter -1 192
    current datasets
    dataset 0
    dataset 1
    dataset 2
    ---> data sets left after filtering None
    ---> data len after filtering=192
    ---> excluding  dataset/s ['2']
    filter 2 191
    current datasets
    dataset 0
    dataset 1
    ---> data sets left after filtering None
    ---> data len after filtering=191
    current datasets
    dataset 0
    dataset 1



.. image:: Jet_example_model_fit_leptonic_eq_EC_files/Jet_example_model_fit_leptonic_eq_EC_14_1.png


.. code:: ipython3

    sed_data.data.write('~/3C454.3.ecsv',overwrite=True)

.. code:: ipython3

    sed_data.group_data(bin_width=.15)
    sed_data.add_systematics(0.1,[10.**6,10.**29])
    #sed_data.add_systematics(0.05,[10.**19,10.**30])
    
    p=sed_data.plot_sed()


.. parsed-literal::

    ================================================================================
    
    ***  binning data  ***
    ---> N bins= 98
    ---> bin_width= 0.15
    ================================================================================
    



.. image:: Jet_example_model_fit_leptonic_eq_EC_files/Jet_example_model_fit_leptonic_eq_EC_16_1.png


.. code:: ipython3

    sed_data.save('3C454_data.pkl')

Phenomenological model constraining
-----------------------------------

see the :ref:`phenom_constr` user guide for further information about phenomenological model constraining

.. code:: ipython3

    from jetset.sed_shaper import  SEDShape
    my_shape=SEDShape(sed_data)
    my_shape.eval_indices(silent=True)
    p=my_shape.plot_indices()
    p.setlim(y_min=1E-15,y_max=1E-9)


.. parsed-literal::

    ================================================================================
    
    *** evaluating spectral indices for data ***
    ================================================================================
    



.. image:: Jet_example_model_fit_leptonic_eq_EC_files/Jet_example_model_fit_leptonic_eq_EC_20_1.png


for the synchrotron sed_shaping we include the check for Big Blue Bump
(BBB) component. Moreover, we force the model to use a pure
log-parabolic function and not a log-cubic one in order to get a better
estimation of the BBB component. The fit values of the BBB component
will be used in the ``ObsConstrain`` to guess the accretion disk
luminosity and temperature

.. code:: ipython3

    mm,best_fit=my_shape.sync_fit(check_BBB_template=True,
                                  check_host_gal_template=False,
                                  use_log_par=True,
                                  Ep_start=None,
                                  minimizer='lsb',
                                  silent=True,
                                  fit_range=[9,16])


.. parsed-literal::

    ================================================================================
    
    *** Log-Polynomial fitting of the synchrotron component ***
    ---> first blind fit run,  fit range: [9, 16]
    --> class:  LSP
    
    --> class:  LSP
    
    



.. raw:: html

    <i>Table length=5</i>
    <table id="table6305256640-676524" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>val</th><th>bestfit val</th><th>err +</th><th>err -</th><th>start val</th><th>fit range min</th><th>fit range max</th><th>frozen</th></tr></thead>
    <tr><td>LogParabolaEp</td><td>b</td><td>-3.175784e-01</td><td>-3.175784e-01</td><td>3.360071e-02</td><td>--</td><td>-1.560612e-01</td><td>-1.000000e+01</td><td>0.000000e+00</td><td>False</td></tr>
    <tr><td>LogParabolaEp</td><td>Ep</td><td>1.167956e+01</td><td>1.167956e+01</td><td>1.276551e-01</td><td>--</td><td>1.286767e+01</td><td>0.000000e+00</td><td>3.000000e+01</td><td>False</td></tr>
    <tr><td>LogParabolaEp</td><td>Sp</td><td>-1.123620e+01</td><td>-1.123620e+01</td><td>4.508175e-02</td><td>--</td><td>-1.087583e+01</td><td>-3.000000e+01</td><td>0.000000e+00</td><td>False</td></tr>
    <tr><td>BBB</td><td>nuFnu_p_BBB</td><td>-1.156569e+01</td><td>-1.156569e+01</td><td>2.922622e-02</td><td>--</td><td>-1.087583e+01</td><td>-1.287583e+01</td><td>-8.875830e+00</td><td>False</td></tr>
    <tr><td>BBB</td><td>nu_scale</td><td>8.865061e-03</td><td>8.865061e-03</td><td>3.068053e-03</td><td>--</td><td>0.000000e+00</td><td>-5.000000e-01</td><td>5.000000e-01</td><td>False</td></tr>
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
        console.log("$('#table6305256640-676524').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table6305256640-676524').dataTable({
            order: [],
            pageLength: 100,
            lengthMenu: [[10, 25, 50, 100, 500, 1000, -1], [10, 25, 50, 100, 500, 1000, 'All']],
            pagingType: "full_numbers",
            columnDefs: [{targets: [2, 3, 4, 5, 6, 7, 8], type: "optionalnum"}]
        });
    });
    </script>



.. parsed-literal::

    ---> sync       nu_p=+1.167956e+01 (err=+1.276551e-01)  nuFnu_p=-1.123620e+01 (err=+4.508175e-02) curv.=-3.175784e-01 (err=+3.360071e-02)
    ================================================================================
    


.. code:: ipython3

    my_shape.IC_fit(fit_range=[16,26],minimizer='minuit', silent=True)
    p=my_shape.plot_shape_fit()
    p.setlim(y_min=1E-15)


.. parsed-literal::

    ================================================================================
    
    *** Log-Polynomial fitting of the IC component ***
    ---> fit range: [16, 26]
    ---> LogCubic fit
    
    



.. raw:: html

    <i>Table length=4</i>
    <table id="table13254208752-530075" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>val</th><th>bestfit val</th><th>err +</th><th>err -</th><th>start val</th><th>fit range min</th><th>fit range max</th><th>frozen</th></tr></thead>
    <tr><td>LogCubic</td><td>b</td><td>-1.331773e-01</td><td>-1.331773e-01</td><td>1.456625e-02</td><td>--</td><td>-1.000000e+00</td><td>-1.000000e+01</td><td>0.000000e+00</td><td>False</td></tr>
    <tr><td>LogCubic</td><td>c</td><td>-1.352785e-02</td><td>-1.352785e-02</td><td>2.463746e-03</td><td>--</td><td>-1.000000e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td></tr>
    <tr><td>LogCubic</td><td>Ep</td><td>2.230023e+01</td><td>2.230023e+01</td><td>1.126805e-01</td><td>--</td><td>2.228823e+01</td><td>0.000000e+00</td><td>3.000000e+01</td><td>False</td></tr>
    <tr><td>LogCubic</td><td>Sp</td><td>-1.035586e+01</td><td>-1.035586e+01</td><td>5.102578e-02</td><td>--</td><td>-1.000000e+01</td><td>-3.000000e+01</td><td>0.000000e+00</td><td>False</td></tr>
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
        console.log("$('#table13254208752-530075').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table13254208752-530075').dataTable({
            order: [],
            pageLength: 100,
            lengthMenu: [[10, 25, 50, 100, 500, 1000, -1], [10, 25, 50, 100, 500, 1000, 'All']],
            pagingType: "full_numbers",
            columnDefs: [{targets: [2, 3, 4, 5, 6, 7, 8], type: "optionalnum"}]
        });
    });
    </script>



.. parsed-literal::

    ---> IC         nu_p=+2.230023e+01 (err=+1.126805e-01)  nuFnu_p=-1.035586e+01 (err=+5.102578e-02) curv.=-1.331773e-01 (err=+1.456625e-02)
    ================================================================================
    



.. image:: Jet_example_model_fit_leptonic_eq_EC_files/Jet_example_model_fit_leptonic_eq_EC_23_3.png


In this case we use the ``constrain_SSC_EC_model``, and we ask to use a
dusty torus and BLR component external component

read the section :ref:`jet_physical_guide_EC`  for more information regarding the EC model

.. code:: ipython3

    from jetset.obs_constrain import ObsConstrain
    from jetset.minimizer import fit_SED
    sed_obspar=ObsConstrain(B_range=[0.1,0.2],
                            distr_e='bkn',
                            t_var_sec=15*86400,
                            nu_cut_IR=1E9,
                            theta=2,
                            bulk_factor=20,
                            SEDShape=my_shape)
    
    
    prefit_jet=sed_obspar.constrain_SSC_EC_model(electron_distribution_log_values=False,EC_components_list=['EC_DT','EC_BLR'],R_H=2E18,silent=True,)



.. parsed-literal::

    ================================================================================
    
    ***  constrains parameters from observable ***
    
    adding par: L_Disk to  R_BLR_in
    ==> par R_BLR_in is depending on ['L_Disk'] according to expr:   R_BLR_in =
    3E17*(L_Disk/1E46)**0.5
    adding par: R_BLR_in to  R_BLR_out
    ==> par R_BLR_out is depending on ['R_BLR_in'] according to expr:   R_BLR_out =
    R_BLR_in*1.1
    adding par: L_Disk to  R_DT
    ==> par R_DT is depending on ['L_Disk'] according to expr:   R_DT =
    2E19*(L_Disk/1E46)**0.5



.. raw:: html

    <i>Table length=21</i>
    <table id="table13300035520-582856" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>par type</th><th>units</th><th>val</th><th>phys. bound. min</th><th>phys. bound. max</th><th>log</th><th>frozen</th></tr></thead>
    <tr><td>jet_leptonic</td><td>R</td><td>region_size</td><td>cm</td><td>6.764638e+16</td><td>1.000000e+03</td><td>1.000000e+30</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>R_H</td><td>region_position</td><td>cm</td><td>2.000000e+18</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>B</td><td>magnetic_field</td><td>gauss</td><td>1.500000e-01</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>NH_cold_to_rel_e</td><td>cold_p_to_rel_e_ratio</td><td></td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>theta</td><td>jet-viewing-angle</td><td>deg</td><td>2.000000e+00</td><td>0.000000e+00</td><td>9.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>BulkFactor</td><td>jet-bulk-factor</td><td>lorentz-factor*</td><td>2.000000e+01</td><td>1.000000e+00</td><td>1.000000e+05</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>z_cosm</td><td>redshift</td><td></td><td>5.930000e-01</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmin</td><td>low-energy-cut-off</td><td>lorentz-factor*</td><td>1.033091e+01</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gmax</td><td>high-energy-cut-off</td><td>lorentz-factor*</td><td>1.351959e+04</td><td>1.000000e+00</td><td>1.000000e+15</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>N</td><td>emitters_density</td><td>1 / cm3</td><td>1.145713e+03</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>gamma_break</td><td>turn-over-energy</td><td>lorentz-factor*</td><td>2.259008e+02</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>p</td><td>LE_spectral_slope</td><td></td><td>2.301767e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>p_1</td><td>HE_spectral_slope</td><td></td><td>3.500000e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>T_DT</td><td>DT</td><td>K</td><td>1.000000e+02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>*R_DT(D,L_Disk)</td><td>DT</td><td>cm</td><td>1.292162e+19</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>tau_DT</td><td>DT</td><td></td><td>1.000000e-01</td><td>0.000000e+00</td><td>1.000000e+00</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>tau_BLR</td><td>BLR</td><td></td><td>1.000000e-01</td><td>0.000000e+00</td><td>1.000000e+00</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>*R_BLR_in(D,L_Disk)</td><td>BLR</td><td>cm</td><td>1.938243e+17</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>*R_BLR_out(D,R_BLR_in)</td><td>BLR</td><td>cm</td><td>2.132067e+17</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_leptonic</td><td>L_Disk(M)</td><td>Disk</td><td>erg / s</td><td>4.174205e+45</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_leptonic</td><td>T_Disk</td><td>Disk</td><td>K</td><td>3.018434e+04</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
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
        console.log("$('#table13300035520-582856').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table13300035520-582856').dataTable({
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
    p=prefit_jet.plot_model(sed_data=sed_data)



.. image:: Jet_example_model_fit_leptonic_eq_EC_files/Jet_example_model_fit_leptonic_eq_EC_27_0.png


.. code:: ipython3

    prefit_jet.make_conical_jet(theta_open=5)


.. parsed-literal::

    adding par: R_H to  R
    adding par: theta_open to  R
    ==> par R is depending on ['R_H', 'theta_open'] according to expr:   R =
    np.tan(np.radians(theta_open))*R_H
    setting R_H to 7.732016219800707e+17


.. code:: ipython3

    prefit_jet.set_EC_dependencies()


.. parsed-literal::

    ==> par R_BLR_in is depending on ['L_Disk'] according to expr:   R_BLR_in =
    3E17*(L_Disk/1E46)**0.5
    ==> par R_BLR_out is depending on ['R_BLR_in'] according to expr:   R_BLR_out =
    R_BLR_in*1.1
    ==> par R_DT is depending on ['L_Disk'] according to expr:   R_DT =
    2E19*(L_Disk/1E46)**0.5


.. code:: ipython3

    prefit_jet.set_external_field_transf('disk')

.. code:: ipython3

    prefit_jet.eval()
    p=prefit_jet.plot_model(sed_data=sed_data)




.. image:: Jet_example_model_fit_leptonic_eq_EC_files/Jet_example_model_fit_leptonic_eq_EC_31_0.png


Enabling the leptonic equilibrium
---------------------------------

We change the emitters distribution for the ``prefit_jet`` model. We
just preserve the original values of ``gmin`` and ``gmax``

.. code:: ipython3

    from jetset.jet_emitters_factory import InjEmittersFactory
    gmin=prefit_jet.parameters.gmin.val
    gmax=prefit_jet.parameters.gmax.val
    p=prefit_jet.parameters.p.val
    q_inj = InjEmittersFactory().create_inj_emitters('pl')
    prefit_jet.set_emitters_distribution(q_inj)
    prefit_jet.parameters.gmin.val=gmin
    prefit_jet.parameters.gmax.val=gmax
    prefit_jet.parameters.p.val=p
    


We change the name of the jet model (this is not necessary)

.. code:: ipython3

    prefit_jet.name='leptonic_eq'

We renormalize the injected Luminosity in order to get a flux level
consistent with the date, we can use the usual method
``set_N_from_nuFnu``, it will work.

.. code:: ipython3

    prefit_jet.set_N_from_nuFnu(nu_obs=1E12,nuFnu_obs=8E-12)

.. code:: ipython3

    prefit_jet.parameters



.. raw:: html

    <i>Table length=21</i>
    <table id="table13326054080-585607" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>par type</th><th>units</th><th>val</th><th>phys. bound. min</th><th>phys. bound. max</th><th>log</th><th>frozen</th></tr></thead>
    <tr><td>leptonic_eq</td><td>*R(D,theta_open)</td><td>region_size</td><td>cm</td><td>6.764638e+16</td><td>1.000000e+03</td><td>1.000000e+30</td><td>False</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>R_H(M)</td><td>region_position</td><td>cm</td><td>7.732016e+17</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>B</td><td>magnetic_field</td><td>gauss</td><td>1.500000e-01</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>NH_cold_to_rel_e</td><td>cold_p_to_rel_e_ratio</td><td></td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>theta</td><td>jet-viewing-angle</td><td>deg</td><td>2.000000e+00</td><td>0.000000e+00</td><td>9.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>BulkFactor</td><td>jet-bulk-factor</td><td>lorentz-factor*</td><td>2.000000e+01</td><td>1.000000e+00</td><td>1.000000e+05</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>z_cosm</td><td>redshift</td><td></td><td>5.930000e-01</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>T_DT</td><td>DT</td><td>K</td><td>1.000000e+02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>*R_DT(D,L_Disk)</td><td>DT</td><td>cm</td><td>1.292162e+19</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>tau_DT</td><td>DT</td><td></td><td>1.000000e-01</td><td>0.000000e+00</td><td>1.000000e+00</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>tau_BLR</td><td>BLR</td><td></td><td>1.000000e-01</td><td>0.000000e+00</td><td>1.000000e+00</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>*R_BLR_in(D,L_Disk)</td><td>BLR</td><td>cm</td><td>1.938243e+17</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>*R_BLR_out(D,R_BLR_in)</td><td>BLR</td><td>cm</td><td>2.132067e+17</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>L_Disk(M)</td><td>Disk</td><td>erg / s</td><td>4.174205e+45</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>T_Disk</td><td>Disk</td><td>K</td><td>3.018434e+04</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>theta_open(M)</td><td>user_defined</td><td>deg</td><td>5.000000e+00</td><td>1.000000e+00</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>gmin</td><td>low-energy-cut-off</td><td>lorentz-factor*</td><td>1.033091e+01</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>gmax</td><td>high-energy-cut-off</td><td>lorentz-factor*</td><td>1.351959e+04</td><td>1.000000e+00</td><td>1.000000e+15</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>p</td><td>LE_spectral_slope</td><td></td><td>2.301767e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>T_esc_e_primaries</td><td>escape_time</td><td>R / c</td><td>1.000000e+00</td><td>1.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>L_inj</td><td>L_inj</td><td>erg / s</td><td>1.881392e+43</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
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
        console.log("$('#table13326054080-585607').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table13326054080-585607').dataTable({
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

    prefit_jet.eval()
    p=prefit_jet.plot_model(sed_data=sed_data)
    prefit_jet.save_model('prefit_jet_EC.pkl')




.. image:: Jet_example_model_fit_leptonic_eq_EC_files/Jet_example_model_fit_leptonic_eq_EC_40_0.png


The prefit model should works well for the synchrotron component, but
the EC one is a bit problematic. We can set as starting values a
slightly harder value of ``p``, and a larger value of ``gamma_break``
and ``gmax``. We freeze some parameters, and we also set some
``fit_range`` values. Setting fit_range can speed-up the fit convergence
but should be judged by the user each time according to the physics of
the particular source

EC model fit
------------

.. note::
    Please, read the introduction and the caveats :ref:`for the frequentist model fitting <frequentist_model_fitting>` to understand the frequentist fitting workflow
    see the :ref:`composite_models` user guide for further information about the implementation of :class:`.FitModel`, in particular for parameter setting

.. code:: ipython3

    from jetset.data_loader import ObsData
    sed_data=ObsData.load('3C454_data.pkl')
    from jetset.jet_model import Jet

.. code:: ipython3

    from jetset.model_manager import  FitModel
    jet=Jet.load_model('prefit_jet_EC.pkl')
    jet.set_gamma_grid_size(100)
    fit_model=FitModel( jet=jet, name='EC-best-fit-lsb')
    fit_model.show_model_components()


.. parsed-literal::

    
    --------------------------------------------------------------------------------
    Composite model description
    --------------------------------------------------------------------------------
    name: EC-best-fit-lsb  
    type: composite_model  
    components models:
     -model name: leptonic_eq model type: jet
    
    --------------------------------------------------------------------------------


Setting the fit range for the model parameters
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: ipython3

    
    fit_model.freeze('leptonic_eq','z_cosm')
    fit_model.freeze('leptonic_eq','theta')
    
    fit_model.free('leptonic_eq','R_H')
    fit_model.freeze('leptonic_eq','tau_DT')
    fit_model.freeze('leptonic_eq','tau_BLR')
    fit_model.freeze('leptonic_eq','theta_open')
    
    fit_model.leptonic_eq.parameters.L_Disk.fit_range=[1E45,1E46]
    fit_model.leptonic_eq.parameters.R_H.fit_range=[5E17,5E19]
    fit_model.leptonic_eq.parameters.T_Disk.fit_range=[1E4,1E5]
    fit_model.leptonic_eq.parameters.T_DT.fit_range=[100,1000]
    fit_model.leptonic_eq.parameters.gmax.fit_range=[1E4,1E5]
    fit_model.leptonic_eq.parameters.gmin.fit_range=[1,1000]
    
    fit_model.leptonic_eq.parameters.B.fit_range=[1E-2,2]
    fit_model.leptonic_eq.parameters.p.fit_range=[2.0,3.5]
    fit_model.leptonic_eq.parameters.BulkFactor.fit_range=[5,30]
    fit_model.leptonic_eq.parameters.L_inj.fit_range=[1E41,1E44]
    fit_model.leptonic_eq.parameters.T_esc_e_primaries.fit_range=[1,2]

How to enable Internal absorption
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you want to enable the ``DT`` absorption, you can use this
instruction

.. code:: python

   fit_model.jet_leptonic.enable_internal_absorption('DT')

Please read the tutorial :ref:`int_abs_guide` for more information on the internal absorption.

.. code:: ipython3

    fit_model.leptonic_eq.enable_internal_absorption('DT',use_R_H_profile_extrapolation=True)


Run the fit
~~~~~~~~~~~

.. code:: ipython3

    from jetset.minimizer import ModelMinimizer
    model_minimizer=ModelMinimizer('minuit')
    best_fit=model_minimizer.fit(fit_model,sed_data,3E10,1E29,fitname='EC-best-fit-lsb',repeat=2)


.. parsed-literal::

    filtering data in fit range = [3.000000e+10,1.000000e+29]
    data length 25
    ================================================================================
    
    *** start fit process ***
    ----- 
    fit run: 0



.. parsed-literal::

    0it [00:00, ?it/s]


.. parsed-literal::

    - best chisq=1.99632e+02
    
    fit run: 1
    - old chisq=1.99632e+02



.. parsed-literal::

    0it [00:00, ?it/s]


.. parsed-literal::

    - best chisq=3.44169e+01
    
    -------------------------------------------------------------------------
    Fit report
    
    Model: EC-best-fit-lsb



.. raw:: html

    <i>Table length=21</i>
    <table id="table13329218112-561126" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>par type</th><th>units</th><th>val</th><th>phys. bound. min</th><th>phys. bound. max</th><th>log</th><th>frozen</th></tr></thead>
    <tr><td>leptonic_eq</td><td>gmin</td><td>low-energy-cut-off</td><td>lorentz-factor*</td><td>1.738901e+01</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>gmax</td><td>high-energy-cut-off</td><td>lorentz-factor*</td><td>1.609210e+04</td><td>1.000000e+00</td><td>1.000000e+15</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>p</td><td>LE_spectral_slope</td><td></td><td>2.194541e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>T_esc_e_primaries</td><td>escape_time</td><td>R / c</td><td>1.872693e+00</td><td>1.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>L_inj</td><td>L_inj</td><td>erg / s</td><td>4.656823e+43</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>T_DT</td><td>DT</td><td>K</td><td>6.428131e+02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>*R_DT(D,L_Disk)</td><td>DT</td><td>cm</td><td>1.498503e+19</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>tau_DT</td><td>DT</td><td></td><td>1.000000e-01</td><td>0.000000e+00</td><td>1.000000e+00</td><td>False</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>tau_BLR</td><td>BLR</td><td></td><td>1.000000e-01</td><td>0.000000e+00</td><td>1.000000e+00</td><td>False</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>*R_BLR_in(D,L_Disk)</td><td>BLR</td><td>cm</td><td>2.247755e+17</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>*R_BLR_out(D,R_BLR_in)</td><td>BLR</td><td>cm</td><td>2.472531e+17</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>L_Disk(M)</td><td>Disk</td><td>erg / s</td><td>5.613781e+45</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>T_Disk</td><td>Disk</td><td>K</td><td>3.358086e+04</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>*R(D,theta_open)</td><td>region_size</td><td>cm</td><td>5.815817e+17</td><td>1.000000e+03</td><td>1.000000e+30</td><td>False</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>R_H(M)</td><td>region_position</td><td>cm</td><td>6.647509e+18</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>B</td><td>magnetic_field</td><td>gauss</td><td>7.306254e-02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>NH_cold_to_rel_e</td><td>cold_p_to_rel_e_ratio</td><td></td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>theta</td><td>jet-viewing-angle</td><td>deg</td><td>2.000000e+00</td><td>0.000000e+00</td><td>9.000000e+01</td><td>False</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>BulkFactor</td><td>jet-bulk-factor</td><td>lorentz-factor*</td><td>7.291856e+00</td><td>1.000000e+00</td><td>1.000000e+05</td><td>False</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>z_cosm</td><td>redshift</td><td></td><td>5.930000e-01</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>theta_open(M)</td><td>user_defined</td><td>deg</td><td>5.000000e+00</td><td>1.000000e+00</td><td>1.000000e+01</td><td>False</td><td>True</td></tr>
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
        console.log("$('#table13329218112-561126').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table13329218112-561126').dataTable({
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
    calls=4583
    mesg=



.. raw:: html

    <table>
        <tr>
            <th colspan="2" style="text-align:center" title="Minimizer"> Migrad </th>
        </tr>
        <tr>
            <td style="text-align:left" title="Minimum value of function"> FCN = 34.42 </td>
            <td style="text-align:center" title="Total number of function and (optional) gradient evaluations"> Nfcn = 4583 </td>
        </tr>
        <tr>
            <td style="text-align:left" title="Estimated distance to minimum and goal"> EDM = 0.0826 (Goal: 0.0002) </td>
            <td style="text-align:center" title="Total run time of algorithms"> time = 64.6 sec </td>
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
            <td> 17.3890 </td>
            <td> 0.0004 </td>
            <td>  </td>
            <td>  </td>
            <td> 1 </td>
            <td> 1E+03 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 1 </th>
            <td> par_1 </td>
            <td> 16.0921e3 </td>
            <td> 0.0020e3 </td>
            <td>  </td>
            <td>  </td>
            <td> 1E+04 </td>
            <td> 1E+05 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 2 </th>
            <td> par_2 </td>
            <td> 2.194541 </td>
            <td> 0.000004 </td>
            <td>  </td>
            <td>  </td>
            <td> 2 </td>
            <td> 3.5 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 3 </th>
            <td> par_3 </td>
            <td> 1.872693 </td>
            <td> 0.000005 </td>
            <td>  </td>
            <td>  </td>
            <td> 1 </td>
            <td> 2 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 4 </th>
            <td> par_4 </td>
            <td> 46.6e42 </td>
            <td> 0.7e42 </td>
            <td>  </td>
            <td>  </td>
            <td> 1E+41 </td>
            <td> 1E+44 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 5 </th>
            <td> par_5 </td>
            <td> 0.6e3 </td>
            <td> 0.7e3 </td>
            <td>  </td>
            <td>  </td>
            <td> 100 </td>
            <td> 1E+03 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 6 </th>
            <td> par_6 </td>
            <td> 5.61e45 </td>
            <td> 0.06e45 </td>
            <td>  </td>
            <td>  </td>
            <td> 1E+45 </td>
            <td> 1E+46 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 7 </th>
            <td> par_7 </td>
            <td> 33.6e3 </td>
            <td> 0.4e3 </td>
            <td>  </td>
            <td>  </td>
            <td> 1E+04 </td>
            <td> 1E+05 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 8 </th>
            <td> par_8 </td>
            <td> 6.65e18 </td>
            <td> 0.21e18 </td>
            <td>  </td>
            <td>  </td>
            <td> 5E+17 </td>
            <td> 5E+19 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 9 </th>
            <td> par_9 </td>
            <td> 0.0731 </td>
            <td> 0.0035 </td>
            <td>  </td>
            <td>  </td>
            <td> 0.01 </td>
            <td> 2 </td>
            <td>  </td>
        </tr>
        <tr>
            <th> 10 </th>
            <td> par_10 </td>
            <td> 7.29186 </td>
            <td> 0.00007 </td>
            <td>  </td>
            <td>  </td>
            <td> 5 </td>
            <td> 30 </td>
            <td>  </td>
        </tr>
    </table>


.. parsed-literal::

    dof=14
    chisq=34.416878, chisq/red=2.458348 null hypothesis sig=0.001792
    
    best fit pars



.. raw:: html

    <i>Table length=21</i>
    <table id="table13322771136-657421" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>val</th><th>bestfit val</th><th>err +</th><th>err -</th><th>start val</th><th>fit range min</th><th>fit range max</th><th>frozen</th></tr></thead>
    <tr><td>leptonic_eq</td><td>gmin</td><td>1.738901e+01</td><td>1.738901e+01</td><td>4.244518e-04</td><td>--</td><td>1.033091e+01</td><td>1.000000e+00</td><td>1.000000e+03</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>gmax</td><td>1.609210e+04</td><td>1.609210e+04</td><td>2.012680e+00</td><td>--</td><td>1.351959e+04</td><td>1.000000e+04</td><td>1.000000e+05</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>p</td><td>2.194541e+00</td><td>2.194541e+00</td><td>4.147450e-06</td><td>--</td><td>2.301767e+00</td><td>2.000000e+00</td><td>3.500000e+00</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>T_esc_e_primaries</td><td>1.872693e+00</td><td>1.872693e+00</td><td>5.113175e-06</td><td>--</td><td>1.000000e+00</td><td>1.000000e+00</td><td>2.000000e+00</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>L_inj</td><td>4.656823e+43</td><td>4.656823e+43</td><td>7.040951e+41</td><td>--</td><td>1.881392e+43</td><td>1.000000e+41</td><td>1.000000e+44</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>T_DT</td><td>6.428131e+02</td><td>6.428131e+02</td><td>6.989515e+02</td><td>--</td><td>1.000000e+02</td><td>1.000000e+02</td><td>1.000000e+03</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>*R_DT(D,L_Disk)</td><td>1.498503e+19</td><td>--</td><td>--</td><td>--</td><td>1.292162e+19</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>tau_DT</td><td>1.000000e-01</td><td>--</td><td>--</td><td>--</td><td>1.000000e-01</td><td>0.000000e+00</td><td>1.000000e+00</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>tau_BLR</td><td>1.000000e-01</td><td>--</td><td>--</td><td>--</td><td>1.000000e-01</td><td>0.000000e+00</td><td>1.000000e+00</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>*R_BLR_in(D,L_Disk)</td><td>2.247755e+17</td><td>--</td><td>--</td><td>--</td><td>1.938243e+17</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>*R_BLR_out(D,R_BLR_in)</td><td>2.472531e+17</td><td>--</td><td>--</td><td>--</td><td>2.132067e+17</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>L_Disk(M)</td><td>5.613781e+45</td><td>5.613781e+45</td><td>6.335158e+43</td><td>--</td><td>4.174205e+45</td><td>1.000000e+45</td><td>1.000000e+46</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>T_Disk</td><td>3.358086e+04</td><td>3.358086e+04</td><td>4.463865e+02</td><td>--</td><td>3.018434e+04</td><td>1.000000e+04</td><td>1.000000e+05</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>*R(D,theta_open)</td><td>5.815817e+17</td><td>--</td><td>--</td><td>--</td><td>6.764638e+16</td><td>1.000000e+03</td><td>1.000000e+30</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>R_H(M)</td><td>6.647509e+18</td><td>6.647509e+18</td><td>2.095647e+17</td><td>--</td><td>7.732016e+17</td><td>5.000000e+17</td><td>5.000000e+19</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>B</td><td>7.306254e-02</td><td>7.306254e-02</td><td>3.518598e-03</td><td>--</td><td>1.500000e-01</td><td>1.000000e-02</td><td>2.000000e+00</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>NH_cold_to_rel_e</td><td>1.000000e+00</td><td>--</td><td>--</td><td>--</td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>theta</td><td>2.000000e+00</td><td>--</td><td>--</td><td>--</td><td>2.000000e+00</td><td>0.000000e+00</td><td>9.000000e+01</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>BulkFactor</td><td>7.291856e+00</td><td>7.291856e+00</td><td>7.196863e-05</td><td>--</td><td>2.000000e+01</td><td>5.000000e+00</td><td>3.000000e+01</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>z_cosm</td><td>5.930000e-01</td><td>--</td><td>--</td><td>--</td><td>5.930000e-01</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>theta_open(M)</td><td>5.000000e+00</td><td>--</td><td>--</td><td>--</td><td>5.000000e+00</td><td>1.000000e+00</td><td>1.000000e+01</td><td>True</td></tr>
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
        console.log("$('#table13322771136-657421').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table13322771136-657421').dataTable({
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

    p=model_minimizer.plot_corr_matrix()



.. image:: Jet_example_model_fit_leptonic_eq_EC_files/Jet_example_model_fit_leptonic_eq_EC_54_0.png



.. image:: Jet_example_model_fit_leptonic_eq_EC_files/Jet_example_model_fit_leptonic_eq_EC_54_1.png


.. code:: ipython3

    %matplotlib inline
    fit_model.set_nu_grid(1E6,1E30,200)
    fit_model.eval()
    p2=fit_model.plot_model(sed_data=sed_data)
    p2.setlim(y_min=1E-14,y_max=1E-9,x_min=1E6,x_max=2E28)



.. image:: Jet_example_model_fit_leptonic_eq_EC_files/Jet_example_model_fit_leptonic_eq_EC_55_0.png


.. code:: ipython3

    jet.energetic_report()



.. raw:: html

    <i>Table length=43</i>
    <table id="table6306144688-603531" class="table-striped table-bordered table-condensed">
    <thead><tr><th>name</th><th>type</th><th>units</th><th>val</th></tr></thead>
    <tr><td>BulkLorentzFactor</td><td>jet-bulk-factor</td><td></td><td>7.291856e+00</td></tr>
    <tr><td>U_e</td><td>Energy dens. blob rest. frame</td><td>erg / cm3</td><td>1.534733e-03</td></tr>
    <tr><td>U_p_cold</td><td>Energy dens. blob rest. frame</td><td>erg / cm3</td><td>1.503276e-01</td></tr>
    <tr><td>U_B</td><td>Energy dens. blob rest. frame</td><td>erg / cm3</td><td>2.123977e-04</td></tr>
    <tr><td>U_Synch</td><td>Energy dens. blob rest. frame</td><td>erg / cm3</td><td>2.003324e-05</td></tr>
    <tr><td>U_Synch_DRF</td><td>Energy dens. disk rest. frame</td><td>erg / cm3</td><td>6.941450e-01</td></tr>
    <tr><td>U_Disk</td><td>Energy dens. blob rest. frame</td><td>erg / cm3</td><td>1.656420e-06</td></tr>
    <tr><td>U_BLR</td><td>Energy dens. blob rest. frame</td><td>erg / cm3</td><td>1.157138e-07</td></tr>
    <tr><td>U_DT</td><td>Energy dens. blob rest. frame</td><td>erg / cm3</td><td>7.064949e-04</td></tr>
    <tr><td>U_Corona</td><td>Energy dens. blob rest. frame</td><td>erg / cm3</td><td>0.000000e+00</td></tr>
    <tr><td>U_CMB</td><td>Energy dens. blob rest. frame</td><td>erg / cm3</td><td>0.000000e+00</td></tr>
    <tr><td>U_Star</td><td>Energy dens. blob rest. frame</td><td>erg / cm3</td><td>0.000000e+00</td></tr>
    <tr><td>U_Disk_DRF</td><td>Energy dens. disk rest. frame</td><td>erg / cm3</td><td>3.381845e-04</td></tr>
    <tr><td>U_BLR_DRF</td><td>Energy dens. disk rest. frame</td><td>erg / cm3</td><td>2.184737e-05</td></tr>
    <tr><td>U_DT_DRF</td><td>Energy dens. disk rest. frame</td><td>erg / cm3</td><td>6.675126e-06</td></tr>
    <tr><td>U_Corona_DRF</td><td>Energy dens. disk rest. frame</td><td>erg / cm3</td><td>0.000000e+00</td></tr>
    <tr><td>U_CMB_DRF</td><td>Energy dens. disk rest. frame</td><td>erg / cm3</td><td>0.000000e+00</td></tr>
    <tr><td>U_Star_DRF</td><td>Energy dens. disk rest. frame</td><td>erg / cm3</td><td>0.000000e+00</td></tr>
    <tr><td>U_seed_tot</td><td>Energy dens. blob rest. frame</td><td>erg / cm3</td><td>7.283003e-04</td></tr>
    <tr><td>L_Sync_rf</td><td>Lum. blob rest. frame.</td><td>erg / s</td><td>2.552720e+42</td></tr>
    <tr><td>L_SSC_rf</td><td>Lum. blob rest. frame.</td><td>erg / s</td><td>6.655531e+41</td></tr>
    <tr><td>L_EC_Disk_rf</td><td>Lum. blob rest. frame.</td><td>erg / s</td><td>0.000000e+00</td></tr>
    <tr><td>L_EC_BLR_rf</td><td>Lum. blob rest. frame.</td><td>erg / s</td><td>1.564628e+39</td></tr>
    <tr><td>L_EC_DT_rf</td><td>Lum. blob rest. frame.</td><td>erg / s</td><td>9.039235e+42</td></tr>
    <tr><td>L_EC_Corona_rf</td><td>Lum. blob rest. frame.</td><td>erg / s</td><td>0.000000e+00</td></tr>
    <tr><td>L_EC_CMB_rf</td><td>Lum. blob rest. frame.</td><td>erg / s</td><td>0.000000e+00</td></tr>
    <tr><td>L_EC_Star_rf</td><td>Lum. blob rest. frame.</td><td>erg / s</td><td>0.000000e+00</td></tr>
    <tr><td>jet_L_Sync</td><td>jet Lum.</td><td>erg / s</td><td>3.361216e+43</td></tr>
    <tr><td>jet_L_SSC</td><td>jet Lum.</td><td>erg / s</td><td>8.763468e+42</td></tr>
    <tr><td>jet_L_EC_Disk</td><td>jet Lum.</td><td>erg / s</td><td>0.000000e+00</td></tr>
    <tr><td>jet_L_EC_BLR</td><td>jet Lum.</td><td>erg / s</td><td>2.060176e+40</td></tr>
    <tr><td>jet_L_EC_DT</td><td>jet Lum.</td><td>erg / s</td><td>1.190214e+44</td></tr>
    <tr><td>jet_L_EC_Corona</td><td>jet Lum.</td><td>erg / s</td><td>0.000000e+00</td></tr>
    <tr><td>jet_L_EC_Star</td><td>jet Lum.</td><td>erg / s</td><td>0.000000e+00</td></tr>
    <tr><td>jet_L_EC_CMB</td><td>jet Lum.</td><td>erg / s</td><td>0.000000e+00</td></tr>
    <tr><td>jet_L_pp_gamma</td><td>jet Lum.</td><td>erg / s</td><td>0.000000e+00</td></tr>
    <tr><td>jet_L_rad</td><td>jet Lum.</td><td>erg / s</td><td>1.614176e+44</td></tr>
    <tr><td>jet_L_kin</td><td>jet Lum.</td><td>erg / s</td><td>2.547977e+47</td></tr>
    <tr><td>jet_L_tot</td><td>jet Lum.</td><td>erg / s</td><td>2.553155e+47</td></tr>
    <tr><td>jet_L_e</td><td>jet Lum.</td><td>erg / s</td><td>2.575005e+45</td></tr>
    <tr><td>jet_L_B</td><td>jet Lum.</td><td>erg / s</td><td>3.563650e+44</td></tr>
    <tr><td>jet_L_p_cold</td><td>jet Lum.</td><td>erg / s</td><td>2.522227e+47</td></tr>
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
        console.log("$('#table6306144688-603531').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table6306144688-603531').dataTable({
            order: [],
            pageLength: 100,
            lengthMenu: [[10, 25, 50, 100, 500, 1000, -1], [10, 25, 50, 100, 500, 1000, 'All']],
            pagingType: "full_numbers",
            columnDefs: [{targets: [3], type: "optionalnum"}]
        });
    });
    </script>



.. code:: ipython3

    best_fit.save_report('EC-best-fit-minuit.pkl')
    model_minimizer.save_model('EC_model_minimizer_minuit.pkl')
    fit_model.save_model('EC_fit_model_minuit.pkl')

MCMC sampling
-------------

.. note::
    Please, read the introduction and the caveats :ref:`for the Bayesian model fitting <bayesian_model_fitting>` to understand the MCMC sampler workflow.


.. code:: ipython3

    from jetset.mcmc import McmcSampler
    from jetset.minimizer import ModelMinimizer
    model_minimizer_minuit = ModelMinimizer.load_model('EC_model_minimizer_minuit.pkl')


.. code:: ipython3

    mcmc=McmcSampler(model_minimizer_minuit)


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

    <div><i>Table length=21</i>
    <table id="table13893326016" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>val</th><th>log</th><th>bestfit mcmc</th><th>q16</th><th>q50</th><th>q84</th><th>mcmc bound min</th><th>mcmc bound max</th><th>frozen</th></tr></thead>
    <thead><tr><th>str11</th><th>str22</th><th>float64</th><th>bool</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>bool</th></tr></thead>
    <tr><td>leptonic_eq</td><td>gmin</td><td>1.738901e+01</td><td>False</td><td>1.738901e+01</td><td>--</td><td>--</td><td>--</td><td>1.000000e+00</td><td>1.000000e+03</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>gmax</td><td>1.609210e+04</td><td>False</td><td>1.609210e+04</td><td>--</td><td>--</td><td>--</td><td>1.000000e+04</td><td>1.000000e+05</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>p</td><td>2.194541e+00</td><td>False</td><td>2.194541e+00</td><td>--</td><td>--</td><td>--</td><td>2.000000e+00</td><td>3.500000e+00</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>T_esc_e_primaries</td><td>1.872693e+00</td><td>False</td><td>1.872693e+00</td><td>--</td><td>--</td><td>--</td><td>1.000000e+00</td><td>2.000000e+00</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>L_inj</td><td>4.656823e+43</td><td>False</td><td>4.656823e+43</td><td>--</td><td>--</td><td>--</td><td>1.000000e+41</td><td>1.000000e+44</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>T_DT</td><td>6.428131e+02</td><td>False</td><td>6.428131e+02</td><td>--</td><td>--</td><td>--</td><td>1.000000e+02</td><td>1.000000e+03</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>L_Disk(M)</td><td>5.613781e+45</td><td>False</td><td>5.613781e+45</td><td>--</td><td>--</td><td>--</td><td>1.000000e+45</td><td>1.000000e+46</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>T_Disk</td><td>3.358086e+04</td><td>False</td><td>3.358086e+04</td><td>--</td><td>--</td><td>--</td><td>1.000000e+04</td><td>1.000000e+05</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>R_H(M)</td><td>6.647509e+18</td><td>False</td><td>6.647509e+18</td><td>--</td><td>--</td><td>--</td><td>5.000000e+17</td><td>5.000000e+19</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>B</td><td>7.306254e-02</td><td>False</td><td>7.306254e-02</td><td>--</td><td>--</td><td>--</td><td>1.000000e-02</td><td>2.000000e+00</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>BulkFactor</td><td>7.291856e+00</td><td>False</td><td>7.291856e+00</td><td>--</td><td>--</td><td>--</td><td>5.000000e+00</td><td>3.000000e+01</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>*R_DT(D,L_Disk)</td><td>1.498503e+19</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>tau_DT</td><td>1.000000e-01</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>1.000000e+00</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>tau_BLR</td><td>1.000000e-01</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>1.000000e+00</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>*R_BLR_in(D,L_Disk)</td><td>2.247755e+17</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>*R_BLR_out(D,R_BLR_in)</td><td>2.472531e+17</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>*R(D,theta_open)</td><td>5.815817e+17</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>1.000000e+03</td><td>1.000000e+30</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>NH_cold_to_rel_e</td><td>1.000000e+00</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>theta</td><td>2.000000e+00</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>9.000000e+01</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>z_cosm</td><td>5.930000e-01</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>theta_open(M)</td><td>5.000000e+00</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>1.000000e+00</td><td>1.000000e+01</td><td>True</td></tr>
    </table></div>



If you want to exclude or include parameters from the samplers just
freeze/free them.

.. code:: ipython3

    mcmc.model.leptonic_eq.parameters.freeze_all()
    mcmc.model.leptonic_eq.parameters.L_inj.free()
    mcmc.model.leptonic_eq.parameters.B.free()
    mcmc.model.leptonic_eq.parameters.BulkFactor.free()
    mcmc.model.leptonic_eq.parameters.p.free()

.. code:: ipython3

    mcmc.set_bounds(bound=5.0,bound_rel=True)


.. parsed-literal::

    par: p  ref value:  2.19454134020838  mcmc bounds: [2.0, 3.5]
    par: L_inj  ref value:  4.656823377886678e+43  mcmc bounds: [1e+41, 1e+44]
    par: B  ref value:  0.07306254320286021  mcmc bounds: [0.01, np.float64(0.43837525921716125)]
    par: BulkFactor  ref value:  7.2918555383078045  mcmc bounds: [5, 30]


Or, you can set per-parameter bounds as follows, passing the actual
bound for each parameter.

You can set different bounds for all the free parameters with the
instruction below. In case you want to preserve the ``fit_range``
defined in the frequentist minimizer you can pass
``preserve_fit_range=True``

.. note::
    Please, read the introduction and the caveats :ref:`for the Bayesian model fitting <bayesian_model_fitting>` to understand better the bound settings.

.. code:: ipython3

    mcmc.set_bounds(bound=5.0,bound_rel=True)


.. parsed-literal::

    par: p  ref value:  2.19454134020838  mcmc bounds: [2.0, 3.5]
    par: L_inj  ref value:  4.656823377886678e+43  mcmc bounds: [1e+41, 1e+44]
    par: B  ref value:  0.07306254320286021  mcmc bounds: [0.01, np.float64(0.43837525921716125)]
    par: BulkFactor  ref value:  7.2918555383078045  mcmc bounds: [5, 30]


Or, you can set per-parameter bounds as follows, passing the actual
bound for each parameter.

.. code:: ipython3

    mcmc.set_bounds(comp_name='leptonic_eq',par_name='L_inj',par_bounds=[1E42,1E44])



.. parsed-literal::

    par: L_inj  ref value:  4.656823377886678e+43  mcmc bounds: [1e+42, 1e+44]


.. code:: ipython3

    mcmc.sampler_parameters




.. raw:: html

    <div><i>Table length=21</i>
    <table id="table13893954848" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>val</th><th>log</th><th>bestfit mcmc</th><th>q16</th><th>q50</th><th>q84</th><th>mcmc bound min</th><th>mcmc bound max</th><th>frozen</th></tr></thead>
    <thead><tr><th>str11</th><th>str22</th><th>float64</th><th>bool</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>bool</th></tr></thead>
    <tr><td>leptonic_eq</td><td>p</td><td>2.194541e+00</td><td>False</td><td>2.194541e+00</td><td>--</td><td>--</td><td>--</td><td>2.000000e+00</td><td>3.500000e+00</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>L_inj</td><td>4.656823e+43</td><td>False</td><td>4.656823e+43</td><td>--</td><td>--</td><td>--</td><td>1.000000e+42</td><td>1.000000e+44</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>B</td><td>7.306254e-02</td><td>False</td><td>7.306254e-02</td><td>--</td><td>--</td><td>--</td><td>1.000000e-02</td><td>4.383753e-01</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>BulkFactor</td><td>7.291856e+00</td><td>False</td><td>7.291856e+00</td><td>--</td><td>--</td><td>--</td><td>5.000000e+00</td><td>3.000000e+01</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>gmin</td><td>1.738901e+01</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>1.000000e+00</td><td>1.000000e+03</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>gmax</td><td>1.609210e+04</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>1.000000e+04</td><td>1.000000e+05</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>T_esc_e_primaries</td><td>1.872693e+00</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>1.000000e+00</td><td>2.000000e+00</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>T_DT</td><td>6.428131e+02</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>1.000000e+02</td><td>1.000000e+03</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>*R_DT(D,L_Disk)</td><td>1.498503e+19</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>tau_DT</td><td>1.000000e-01</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>1.000000e+00</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>tau_BLR</td><td>1.000000e-01</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>1.000000e+00</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>*R_BLR_in(D,L_Disk)</td><td>2.247755e+17</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>*R_BLR_out(D,R_BLR_in)</td><td>2.472531e+17</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>L_Disk(M)</td><td>5.613781e+45</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>1.000000e+45</td><td>1.000000e+46</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>T_Disk</td><td>3.358086e+04</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>1.000000e+04</td><td>1.000000e+05</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>*R(D,theta_open)</td><td>5.815817e+17</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>1.000000e+03</td><td>1.000000e+30</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>R_H(M)</td><td>6.647509e+18</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>5.000000e+17</td><td>5.000000e+19</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>NH_cold_to_rel_e</td><td>1.000000e+00</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>theta</td><td>2.000000e+00</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>9.000000e+01</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>z_cosm</td><td>5.930000e-01</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>theta_open(M)</td><td>5.000000e+00</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>1.000000e+00</td><td>1.000000e+01</td><td>True</td></tr>
    </table></div>



.. code:: ipython3

    mcmc.run_sampler(nwalkers=20, burnin=50,steps=500,progress='notebook')


.. parsed-literal::

    mcmc run starting
    



.. parsed-literal::

      0%|          | 0/500 [00:00<?, ?it/s]


.. parsed-literal::

    mcmc run done, with 1 threads took 161.24 seconds
    ----------------------------
    MCMC best fit solution
    comp: leptonic_eq par: p  mcmc best fit val: 2.2158666482455045 quantiles(0.16,0.5,0.84): [2.17433795 2.22002688 2.26464902] 
    comp: leptonic_eq par: L_inj  mcmc best fit val: 4.99037550574399e+43 quantiles(0.16,0.5,0.84): [4.67867507e+43 5.02306897e+43 5.36037522e+43] 
    comp: leptonic_eq par: B  mcmc best fit val: 0.0702310903522585 quantiles(0.16,0.5,0.84): [0.06660811 0.07020439 0.07381892] 
    comp: leptonic_eq par: BulkFactor  mcmc best fit val: 7.21751325528065 quantiles(0.16,0.5,0.84): [7.052696   7.22843666 7.39680692] 
    ----------------------------


Showing the MCMC parameters. Now MCMC bestfit values are updated to the
best-fit MCMC solution

.. code:: ipython3

    mcmc.sampler_parameters




.. raw:: html

    <div><i>Table length=21</i>
    <table id="table13893956048" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>val</th><th>log</th><th>bestfit mcmc</th><th>q16</th><th>q50</th><th>q84</th><th>mcmc bound min</th><th>mcmc bound max</th><th>frozen</th></tr></thead>
    <thead><tr><th>str11</th><th>str22</th><th>float64</th><th>bool</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>float64</th><th>bool</th></tr></thead>
    <tr><td>leptonic_eq</td><td>p</td><td>2.215867e+00</td><td>False</td><td>2.215867e+00</td><td>2.174338e+00</td><td>2.220027e+00</td><td>2.264649e+00</td><td>2.000000e+00</td><td>3.500000e+00</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>L_inj</td><td>4.990376e+43</td><td>False</td><td>4.990376e+43</td><td>4.678675e+43</td><td>5.023069e+43</td><td>5.360375e+43</td><td>1.000000e+42</td><td>1.000000e+44</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>B</td><td>7.023109e-02</td><td>False</td><td>7.023109e-02</td><td>6.660811e-02</td><td>7.020439e-02</td><td>7.381892e-02</td><td>1.000000e-02</td><td>4.383753e-01</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>BulkFactor</td><td>7.217513e+00</td><td>False</td><td>7.217513e+00</td><td>7.052696e+00</td><td>7.228437e+00</td><td>7.396807e+00</td><td>5.000000e+00</td><td>3.000000e+01</td><td>False</td></tr>
    <tr><td>leptonic_eq</td><td>gmin</td><td>1.738901e+01</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>1.000000e+00</td><td>1.000000e+03</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>gmax</td><td>1.609210e+04</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>1.000000e+04</td><td>1.000000e+05</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>T_esc_e_primaries</td><td>1.872693e+00</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>1.000000e+00</td><td>2.000000e+00</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>T_DT</td><td>6.428131e+02</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>1.000000e+02</td><td>1.000000e+03</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>*R_DT(D,L_Disk)</td><td>1.498503e+19</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>tau_DT</td><td>1.000000e-01</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>1.000000e+00</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>tau_BLR</td><td>1.000000e-01</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>1.000000e+00</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>*R_BLR_in(D,L_Disk)</td><td>2.247755e+17</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>*R_BLR_out(D,R_BLR_in)</td><td>2.472531e+17</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>L_Disk(M)</td><td>5.613781e+45</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>1.000000e+45</td><td>1.000000e+46</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>T_Disk</td><td>3.358086e+04</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>1.000000e+04</td><td>1.000000e+05</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>*R(D,theta_open)</td><td>5.815817e+17</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>1.000000e+03</td><td>1.000000e+30</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>R_H(M)</td><td>6.647509e+18</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>5.000000e+17</td><td>5.000000e+19</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>NH_cold_to_rel_e</td><td>1.000000e+00</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>theta</td><td>2.000000e+00</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>9.000000e+01</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>z_cosm</td><td>5.930000e-01</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>0.000000e+00</td><td>--</td><td>True</td></tr>
    <tr><td>leptonic_eq</td><td>theta_open(M)</td><td>5.000000e+00</td><td>False</td><td>--</td><td>--</td><td>--</td><td>--</td><td>1.000000e+00</td><td>1.000000e+01</td><td>True</td></tr>
    </table></div>



post-run tuning of the burnin
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

We plot the chains, before and after, tuning the burnin (using
autocorrelation time of emcee).

.. code:: ipython3

    p=mcmc.plot_chain()
    




.. image:: Jet_example_model_fit_leptonic_eq_EC_files/Jet_example_model_fit_leptonic_eq_EC_81_0.png


.. code:: ipython3

    mcmc.tune_burnin(tau_coeff=2)



.. parsed-literal::

    ----------------------------
    MCMC best fit solution
    comp: leptonic_eq par: p  mcmc best fit val: 2.2158666482455045 quantiles(0.16,0.5,0.84): [2.17428931 2.22014035 2.26496807] 
    comp: leptonic_eq par: L_inj  mcmc best fit val: 4.99037550574399e+43 quantiles(0.16,0.5,0.84): [4.67980761e+43 5.02526113e+43 5.36290356e+43] 
    comp: leptonic_eq par: B  mcmc best fit val: 0.0702310903522585 quantiles(0.16,0.5,0.84): [0.06659429 0.07019401 0.07381892] 
    comp: leptonic_eq par: BulkFactor  mcmc best fit val: 7.21751325528065 quantiles(0.16,0.5,0.84): [7.05180026 7.22808376 7.39682438] 
    ----------------------------


.. code:: ipython3

    p=mcmc.plot_chain()



.. image:: Jet_example_model_fit_leptonic_eq_EC_files/Jet_example_model_fit_leptonic_eq_EC_83_0.png


The updated burnin value has increased compared to the initial one, now
the chain samples a parameter space closer to the convergence.

plotting the posterior corner plot
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To have a better rendering on the scatter plot, we redefine the plot
labels

.. code:: ipython3

    mcmc.set_plot_label('L_inj',r'$L_{\rm inj}$',comp_name='leptonic_eq')
    mcmc.set_plot_label('B',r'$B$',comp_name='leptonic_eq')
    mcmc.set_plot_label('p',r'$p$',comp_name='leptonic_eq')
    mcmc.set_plot_label('BulkFactor',r'$\Gamma$',comp_name='leptonic_eq')
    


The code below lets you tune the output.

1. ``mpl.rcParams['figure.dpi']``: increase it to get better definition.
2. ``title_fmt='.2E'``: Python format with 2 significant digits in
   scientific notation.
3. ``title_kwargs=dict(fontsize=12)``: change the title font size.

Per-component corner plot

.. code:: ipython3

    import matplotlib as mpl
    
    mpl.rcParams['figure.dpi'] = 80
    f=mcmc.corner_plot(quantiles=(0.16, 0.5, 0.84),title_kwargs=dict(fontsize=12),title_fmt=".2E",use_math_text=True)



.. image:: Jet_example_model_fit_leptonic_eq_EC_files/Jet_example_model_fit_leptonic_eq_EC_89_0.png


plotting the model
~~~~~~~~~~~~~~~~~~

To plot the sampled model range against the input mcmc best-fit model

.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    p=mcmc.plot_model(sed_data=sed_data,size=500)
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_example_model_fit_leptonic_eq_EC_files/Jet_example_model_fit_leptonic_eq_EC_92_0.png


To plot the mcmc best-fit model providing quantiles. The size parameter
defines the number of samples to use to extract the quantiles. I am
using a size equal to the number of steps

.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    p=mcmc.plot_model(sed_data=sed_data,size=500,quantiles=[0.05,0.95])
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_example_model_fit_leptonic_eq_EC_files/Jet_example_model_fit_leptonic_eq_EC_94_0.png


If you want to plot also the model components, pass the argument
``plot_components=True``

.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    p=mcmc.plot_model(sed_data=sed_data,size=500,quantiles=[0.05,0.95],plot_components=True)
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_example_model_fit_leptonic_eq_EC_files/Jet_example_model_fit_leptonic_eq_EC_96_0.png


To plot the frequentist best-fit model range, providing quantiles,
provide (``plot_mcmc_best_fit_model=False``)

.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 100
    p=mcmc.plot_model(sed_data=sed_data,size=500,quantiles=[0.05,0.95], plot_mcmc_best_fit_model=False,plot_components=True)
    p.setlim(y_min=1E-14,x_min=1E6,x_max=2E28)



.. image:: Jet_example_model_fit_leptonic_eq_EC_files/Jet_example_model_fit_leptonic_eq_EC_98_0.png


plotting chains and individual posteriors
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: ipython3

    import matplotlib.pylab as plt
    
    mpl.rcParams['figure.dpi'] = 80
    f=mcmc.plot_chain(par_name='p',log_plot=False)
    plt.tight_layout()



.. image:: Jet_example_model_fit_leptonic_eq_EC_files/Jet_example_model_fit_leptonic_eq_EC_100_0.png


.. code:: ipython3

    mpl.rcParams['figure.dpi'] = 80
    f=mcmc.plot_chain(log_plot=False)
    plt.tight_layout()



.. image:: Jet_example_model_fit_leptonic_eq_EC_files/Jet_example_model_fit_leptonic_eq_EC_101_0.png


Save and reuse MCMC
-------------------

.. code:: ipython3

    mcmc.save('mcmc_sampler.pkl')

.. code:: ipython3

    from jetset.mcmc import McmcSampler
    from jetset.data_loader import ObsData
    from jetset.plot_sedfit import PlotSED
    from jetset.test_data_helper import  test_SEDs
    
    sed_data=ObsData.load('3C454_data.pkl')
    
    ms=McmcSampler.load('mcmc_sampler.pkl')

.. code:: ipython3

    ms.model.set_nu_grid(1E6,1E30,200)
    
    p=ms.plot_model(sed_data=sed_data,fit_range=[3E10, 1E29],size=100)
    p.setlim(y_min=1E-13,x_min=1E6,x_max=2E28)



.. image:: Jet_example_model_fit_leptonic_eq_EC_files/Jet_example_model_fit_leptonic_eq_EC_105_0.png


.. code:: ipython3

    p=ms.plot_model(sed_data=sed_data,fit_range=[3E10, 1E29],size=100,quantiles=[0.05,0.95])
    p.setlim(y_min=1E-13,x_min=1E6,x_max=2E28)



.. image:: Jet_example_model_fit_leptonic_eq_EC_files/Jet_example_model_fit_leptonic_eq_EC_106_0.png


.. code:: ipython3

    f=ms.plot_par('p',log_plot=False)




.. image:: Jet_example_model_fit_leptonic_eq_EC_files/Jet_example_model_fit_leptonic_eq_EC_107_0.png

