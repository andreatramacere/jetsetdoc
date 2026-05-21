.. _jet_physical_guide_EC_Corona:

Example for using a Corona within the EC scenario
=================================================

Adding the Corona the EC fields
-------------------------------

.. code:: ipython3

    from jetset.jet_model import Jet
    
    jet = Jet(name='test_corona_component', emitters_distribution='bkn',beaming_expr='bulk_theta',verbose=False)
    
    jet.add_EC_component(['EC_Corona','Disk'],disk_type='MultiBB')
    
    jet.parameters.N.val = 1E3
    jet.parameters.gmax.val = 5E3
    jet.parameters.gamma_break.val = 1000
    
    
    jet.parameters.B.val = 1
    jet.parameters.L_Disk.val = 1E46
    jet.make_conical_jet()
    jet.parameters.R_H.val = 7E17
    



.. parsed-literal::

    adding par: R_H to  R
    adding par: theta_open to  R
    ==> par R is depending on ['R_H', 'theta_open'] according to expr:   R =
    np.tan(np.radians(theta_open))*R_H
    setting R_H to 5.715026151380672e+16


Linking the Corona physical parameters to the Disk Luminosity and Rg
--------------------------------------------------------------------

In the case of the Corona can be useful to define the radius
``R_Corona``, the distance from the BH, ``R_H_Corona``, and the
luminosity as a function of ``R_g`` and of the disk luminosity.

.. code:: ipython3

    
    #The Corona luminosity will scale as the disk luminosity according to 'L_Disk*L_DC_ratio'
    jet.add_user_par('L_DC_ratio',val=1,val_min=0,val_max=100)
    jet.make_dependent_par('L_Corona',depends_on=['L_DC_ratio','L_Disk'],par_expr='L_Disk*L_DC_ratio')
    jet.parameters.L_DC_ratio.val = .8
    
    
    jet.add_user_par('R_H_Corona_ratio',val=1,val_min=1,val_max=100)
    jet.add_user_par('R_Corona_ratio',val=1,val_min=1,val_max=100)
    jet.add_user_par('R_g',val=1E13,val_min=1,val_max=1E20)
    
    def eval_R_g(M_BH):
        from astropy.constants import M_sun,c,G
        return M_BH*(G.cgs.value*M_sun.cgs.value/(c.cgs.value**2))
    
    #R_g will de determined by the actual physical relation R_g=2*M_BH/c**2
    jet.make_dependent_par('R_g',depends_on=['M_BH'],par_expr=eval_R_g)
    
    def eval_R_H_corona(R_g,R_H_Corona_ratio):
        return R_H_Corona_ratio*R_g
    
    #The distance of the Corona from the BH will scale as R_g, according to R_H_Corona_ratio*R_g
    jet.make_dependent_par('R_H_Corona',depends_on=['R_g','R_H_Corona_ratio'],par_expr="R_H_Corona_ratio*R_g")
    
    
    def eval_R_corona(R_g,R_Corona_ratio):
        return R_Corona_ratio*R_g
    
    #The radius of the Corona from the BH will scale as R_g, according to R_Corona_ratio*R_g
    jet.make_dependent_par('R_Corona',depends_on=['R_g','R_Corona_ratio'],par_expr="R_Corona_ratio*R_g")
    



.. parsed-literal::

    adding par: L_DC_ratio to  L_Corona
    adding par: L_Disk to  L_Corona
    ==> par L_Corona is depending on ['L_DC_ratio', 'L_Disk'] according to expr:   L_Corona =
    L_Disk*L_DC_ratio
    adding par: M_BH to  R_g
    ==> par R_g is depending on ['M_BH'] according to expr:   R_g =
    def eval_R_g(M_BH):
        from astropy.constants import M_sun,c,G
        return M_BH*(G.cgs.value*M_sun.cgs.value/(c.cgs.value**2))
    
    adding par: R_g to  R_H_Corona
    adding par: R_H_Corona_ratio to  R_H_Corona
    ==> par R_H_Corona is depending on ['R_g', 'R_H_Corona_ratio'] according to expr:   R_H_Corona =
    R_H_Corona_ratio*R_g
    adding par: R_g to  R_Corona
    adding par: R_Corona_ratio to  R_Corona
    ==> par R_Corona is depending on ['R_g', 'R_Corona_ratio'] according to expr:   R_Corona =
    R_Corona_ratio*R_g


.. code:: ipython3

    
    jet.parameters.R_H_Corona_ratio.val=10
    jet.parameters.R_Corona_ratio.val=20

.. code:: ipython3

    '%e'%eval_R_g(1E9)




.. parsed-literal::

    '1.476625e+14'



We can set the spectral parameters of the Corona

.. code:: ipython3

    jet.parameters.alpha_Corona.val_min=-2
    jet.parameters.alpha_Corona.val = 1
    jet.parameters.nu_cut_Corona.val = 1.5E18
    jet.parameters.nu_cut_low_Corona.val = 1E15

.. code:: ipython3

    jet.parameters


.. parsed-literal::

    WARNING: AstropyDeprecationWarning: 'classic' backend for show_in_notebook() is deprecated as of 6.1. Instead, use the supported backend 'ipydatagrid'. [astropy.table.table]



.. raw:: html

    <i>Table length=29</i>
    <table id="table5997158160-916700" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>par type</th><th>units</th><th>val</th><th>phys. bound. min</th><th>phys. bound. max</th><th>log</th><th>frozen</th></tr></thead>
    <tr><td>test_corona_component</td><td>*R(D,theta_open)</td><td>region_size</td><td>cm</td><td>6.124206e+16</td><td>1.000000e+03</td><td>1.000000e+30</td><td>False</td><td>True</td></tr>
    <tr><td>test_corona_component</td><td>R_H(M)</td><td>region_position</td><td>cm</td><td>7.000000e+17</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>test_corona_component</td><td>B</td><td>magnetic_field</td><td>gauss</td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>test_corona_component</td><td>NH_cold_to_rel_e</td><td>cold_p_to_rel_e_ratio</td><td></td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>test_corona_component</td><td>theta</td><td>jet-viewing-angle</td><td>deg</td><td>1.000000e-01</td><td>0.000000e+00</td><td>9.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>test_corona_component</td><td>BulkFactor</td><td>jet-bulk-factor</td><td>lorentz-factor*</td><td>1.000000e+01</td><td>1.000000e+00</td><td>1.000000e+05</td><td>False</td><td>False</td></tr>
    <tr><td>test_corona_component</td><td>z_cosm</td><td>redshift</td><td></td><td>1.000000e-01</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>test_corona_component</td><td>gmin</td><td>low-energy-cut-off</td><td>lorentz-factor*</td><td>2.000000e+00</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>test_corona_component</td><td>gmax</td><td>high-energy-cut-off</td><td>lorentz-factor*</td><td>5.000000e+03</td><td>1.000000e+00</td><td>1.000000e+15</td><td>False</td><td>False</td></tr>
    <tr><td>test_corona_component</td><td>N</td><td>emitters_density</td><td>1 / cm3</td><td>1.000000e+03</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>test_corona_component</td><td>gamma_break</td><td>turn-over-energy</td><td>lorentz-factor*</td><td>1.000000e+03</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>test_corona_component</td><td>p</td><td>LE_spectral_slope</td><td></td><td>2.500000e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>test_corona_component</td><td>p_1</td><td>HE_spectral_slope</td><td></td><td>3.500000e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>test_corona_component</td><td>*L_Corona(D,L_Disk)</td><td>Corona</td><td>erg / s</td><td>8.000000e+45</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>test_corona_component</td><td>*R_Corona(D,R_Corona_ratio)</td><td>Corona</td><td>cm</td><td>2.953250e+15</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>test_corona_component</td><td>*R_H_Corona(D,R_H_Corona_ratio)</td><td>Corona</td><td>cm</td><td>1.476625e+15</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>test_corona_component</td><td>alpha_Corona</td><td>Corona</td><td></td><td>1.000000e+00</td><td>-2.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>test_corona_component</td><td>nu_cut_low_Corona</td><td>Corona</td><td>Hz</td><td>1.000000e+15</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>test_corona_component</td><td>nu_cut_Corona</td><td>Corona</td><td>Hz</td><td>1.500000e+18</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>test_corona_component</td><td>R_inner_Sw</td><td>Disk</td><td>Sw. radii*</td><td>3.000000e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>test_corona_component</td><td>R_ext_Sw</td><td>Disk</td><td>Sw. radii*</td><td>5.000000e+02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>test_corona_component</td><td>accr_eff</td><td>Disk</td><td></td><td>8.000000e-02</td><td>6.000000e-02</td><td>1.000000e-01</td><td>False</td><td>False</td></tr>
    <tr><td>test_corona_component</td><td>M_BH(M)</td><td>Disk</td><td>M_sun*</td><td>1.000000e+09</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>test_corona_component</td><td>L_Disk(M)</td><td>Disk</td><td>erg / s</td><td>1.000000e+46</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>test_corona_component</td><td>theta_open(M)</td><td>user_defined</td><td>deg</td><td>5.000000e+00</td><td>1.000000e+00</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>test_corona_component</td><td>L_DC_ratio(M)</td><td>user_defined</td><td></td><td>8.000000e-01</td><td>0.000000e+00</td><td>1.000000e+02</td><td>False</td><td>False</td></tr>
    <tr><td>test_corona_component</td><td>R_H_Corona_ratio(M)</td><td>user_defined</td><td></td><td>1.000000e+01</td><td>1.000000e+00</td><td>1.000000e+02</td><td>False</td><td>False</td></tr>
    <tr><td>test_corona_component</td><td>R_Corona_ratio(M)</td><td>user_defined</td><td></td><td>2.000000e+01</td><td>1.000000e+00</td><td>1.000000e+02</td><td>False</td><td>False</td></tr>
    <tr><td>test_corona_component</td><td>*R_g(D,M_BH)</td><td>user_defined</td><td></td><td>1.476625e+14</td><td>1.000000e+00</td><td>1.000000e+20</td><td>False</td><td>True</td></tr>
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
        console.log("$('#table5997158160-916700').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table5997158160-916700').dataTable({
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

    jet.eval()
    p=jet.plot_model()
    p.setlim(y_min=1E-13)



.. image:: Jet_exmple_corona_files/Jet_exmple_corona_12_0.png


