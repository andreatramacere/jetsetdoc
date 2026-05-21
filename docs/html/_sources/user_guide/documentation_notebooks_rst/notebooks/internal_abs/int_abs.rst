.. _int_abs_guide:

Internal absorption
===================

.. code:: ipython3

    import jetset
    print('tested with',jetset.__version__)


.. parsed-literal::

    tested with 1.4.0rc3


In this tutorial we show how to use the internal absorption for external
radiative fields.

We first create a leptonic jet model with e EC components.

.. code:: ipython3

    from jetset.jet_model import Jet
    import numpy as np
    from jetset.template_2Dmodel import EBLAbsorptionTemplate
    import astropy.units as u
    
    ebl_dominguez_2010=EBLAbsorptionTemplate.from_name('Dominguez_2010_v2011')
    ebl_dominguez_lopez=EBLAbsorptionTemplate.from_name('Dominguez_2023')
    ebl_finke=EBLAbsorptionTemplate.from_name('Finke_2010')
    ebl_franceschini=EBLAbsorptionTemplate.from_name('Franceschini_2008')
    
    
    jetset_model = Jet(name="compact_int_abs",emitters_distribution="bkn",beaming_expr='bulk_theta')
    jetset_model.add_EC_component(EC_components_list=['EC_DT','EC_BLR','EC_Corona'],disk_type='BB')
    jetset_model.make_conical_jet(theta_open=5,R=1E16)
    jetset_model.set_EC_dependencies()
    
    jetset_model.set_par('L_Disk',val=2E45)
    jetset_model.set_par('gmax',val=5E5)
    jetset_model.set_par('gmin',val=2.)
    jetset_model.set_par('R_H',val=1E17)
    
    jetset_model.set_par('p',val=1.5)
    jetset_model.set_par('p_1',val=3.2)
    jetset_model.set_par('B',val=1.5)
    jetset_model.set_par('z_cosm',val=0.6)
    jetset_model.set_par('BulkFactor',val=10)
    jetset_model.set_par('theta',val=1)
    jetset_model.set_par('gamma_break',val=5E2)
    jetset_model.parameters.tau_DT.val=0.1
    jetset_model.parameters.T_DT.val=1000
    jetset_model.parameters.theta.freeze()
    jetset_model.parameters.R_H.val=1E18
    jetset_model.set_N_from_nuFnu(nu_obs=1E13,nuFnu_obs=1E-13)
    jetset_model.set_external_field_transf('blob')
    



.. parsed-literal::

    adding par: R_H to  R
    adding par: theta_open to  R
    ==> par R is depending on ['R_H', 'theta_open'] according to expr:   R =
    np.tan(np.radians(theta_open))*R_H
    setting R_H to 1.1430052302761344e+17
    adding par: L_Disk to  R_BLR_in
    ==> par R_BLR_in is depending on ['L_Disk'] according to expr:   R_BLR_in =
    3E17*(L_Disk/1E46)**0.5
    adding par: R_BLR_in to  R_BLR_out
    ==> par R_BLR_out is depending on ['R_BLR_in'] according to expr:   R_BLR_out =
    R_BLR_in*1.1
    adding par: L_Disk to  R_DT
    ==> par R_DT is depending on ['L_Disk'] according to expr:   R_DT =
    2E19*(L_Disk/1E46)**0.5


.. code:: ipython3

    jetset_model


.. parsed-literal::

    
    --------------------------------------------------------------------------------
    model description: 
    --------------------------------------------------------------------------------
    type: Jet
    name: compact_int_abs  
    geometry: spherical  
    
    electrons distribution:
     type: bkn  
     gamma energy grid size:  201
     gmin grid : 2.000000e+00
     gmax grid : 5.000000e+05
     normalization:  True
     log-values:  False
     ratio of cold protons to relativistic electrons: 1.000000e+00
    
    accretion disk:
     disk Type: BB
     L disk: 2.000000e+45 (erg/s)
     T disk: 1.000000e+05 (K)
     nu peak disk: 8.171810e+15 (Hz)
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
       name:EC_DT, state: on
       name:EC_DT, hidden: False
       name:DT, state: on
       name:DT, hidden: False
       name:Disk, state: on
       name:Disk, hidden: False
       name:EC_BLR, state: on
       name:EC_BLR, hidden: False
       name:EC_Corona, state: on
       name:EC_Corona, hidden: False
       name:Corona, state: on
       name:Corona, hidden: False
    external fields transformation method: blob
    
    SED info:
     nu grid size jetkernel: 1000
     nu size: 500
     nu mix (Hz): 1.000000e+06
     nu max (Hz): 1.000000e+30
    
    flux plot lower bound   :  1.000000e-30
    
    --------------------------------------------------------------------------------


.. parsed-literal::

    WARNING: AstropyDeprecationWarning: 'classic' backend for show_in_notebook() is deprecated as of 6.1. Instead, use the supported backend 'ipydatagrid'. [astropy.table.table]



.. raw:: html

    <i>Table length=28</i>
    <table id="table13214981040-321349" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>par type</th><th>units</th><th>val</th><th>phys. bound. min</th><th>phys. bound. max</th><th>log</th><th>frozen</th></tr></thead>
    <tr><td>compact_int_abs</td><td>*R(D,theta_open)</td><td>region_size</td><td>cm</td><td>8.748866e+16</td><td>1.000000e+03</td><td>1.000000e+30</td><td>False</td><td>True</td></tr>
    <tr><td>compact_int_abs</td><td>R_H(M)</td><td>region_position</td><td>cm</td><td>1.000000e+18</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>compact_int_abs</td><td>B</td><td>magnetic_field</td><td>gauss</td><td>1.500000e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>compact_int_abs</td><td>NH_cold_to_rel_e</td><td>cold_p_to_rel_e_ratio</td><td></td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>compact_int_abs</td><td>theta</td><td>jet-viewing-angle</td><td>deg</td><td>1.000000e+00</td><td>0.000000e+00</td><td>9.000000e+01</td><td>False</td><td>True</td></tr>
    <tr><td>compact_int_abs</td><td>BulkFactor</td><td>jet-bulk-factor</td><td>lorentz-factor*</td><td>1.000000e+01</td><td>1.000000e+00</td><td>1.000000e+05</td><td>False</td><td>False</td></tr>
    <tr><td>compact_int_abs</td><td>z_cosm</td><td>redshift</td><td></td><td>6.000000e-01</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>compact_int_abs</td><td>gmin</td><td>low-energy-cut-off</td><td>lorentz-factor*</td><td>2.000000e+00</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>compact_int_abs</td><td>gmax</td><td>high-energy-cut-off</td><td>lorentz-factor*</td><td>5.000000e+05</td><td>1.000000e+00</td><td>1.000000e+15</td><td>False</td><td>False</td></tr>
    <tr><td>compact_int_abs</td><td>N</td><td>emitters_density</td><td>1 / cm3</td><td>6.143592e-02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>compact_int_abs</td><td>gamma_break</td><td>turn-over-energy</td><td>lorentz-factor*</td><td>5.000000e+02</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>compact_int_abs</td><td>p</td><td>LE_spectral_slope</td><td></td><td>1.500000e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>compact_int_abs</td><td>p_1</td><td>HE_spectral_slope</td><td></td><td>3.200000e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
    <tr><td>compact_int_abs</td><td>T_DT</td><td>DT</td><td>K</td><td>1.000000e+03</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>compact_int_abs</td><td>*R_DT(D,L_Disk)</td><td>DT</td><td>cm</td><td>8.944272e+18</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>compact_int_abs</td><td>tau_DT</td><td>DT</td><td></td><td>1.000000e-01</td><td>0.000000e+00</td><td>1.000000e+00</td><td>False</td><td>False</td></tr>
    <tr><td>compact_int_abs</td><td>tau_BLR</td><td>BLR</td><td></td><td>1.000000e-01</td><td>0.000000e+00</td><td>1.000000e+00</td><td>False</td><td>False</td></tr>
    <tr><td>compact_int_abs</td><td>*R_BLR_in(D,L_Disk)</td><td>BLR</td><td>cm</td><td>1.341641e+17</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>compact_int_abs</td><td>*R_BLR_out(D,R_BLR_in)</td><td>BLR</td><td>cm</td><td>1.475805e+17</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>compact_int_abs</td><td>L_Corona</td><td>Corona</td><td>erg / s</td><td>1.000000e+44</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>compact_int_abs</td><td>R_Corona</td><td>Corona</td><td>cm</td><td>1.000000e+13</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>compact_int_abs</td><td>R_H_Corona</td><td>Corona</td><td>cm</td><td>0.000000e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>compact_int_abs</td><td>alpha_Corona</td><td>Corona</td><td></td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>compact_int_abs</td><td>nu_cut_low_Corona</td><td>Corona</td><td>Hz</td><td>0.000000e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>compact_int_abs</td><td>nu_cut_Corona</td><td>Corona</td><td>Hz</td><td>1.000000e+20</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>compact_int_abs</td><td>L_Disk(M)</td><td>Disk</td><td>erg / s</td><td>2.000000e+45</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>compact_int_abs</td><td>T_Disk</td><td>Disk</td><td>K</td><td>1.000000e+05</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>compact_int_abs</td><td>theta_open(M)</td><td>user_defined</td><td>deg</td><td>5.000000e+00</td><td>1.000000e+00</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
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
        console.log("$('#table13214981040-321349').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table13214981040-321349').dataTable({
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



Now we can enable the internal absorption for the BLR and DT fields, and
evaluate the model with internal absorption applied

.. code:: ipython3

    jetset_model.remove_internal_absorption('DT',)
    jetset_model.remove_internal_absorption('BLR', )
    jetset_model.remove_internal_absorption('Corona')
    
    jetset_model.eval()
    p=jetset_model.plot_model()
    
    #jetset_model.remove_internal_absorption('DT')
    #jetset_model.remove_internal_absorption('BLR')
    #jetset_model.remove_internal_absorption('Corona')
    jetset_model.set_external_field_transf('disk')
    
    jetset_model.eval()
    jetset_model.plot_model(plot_obj=p,comp='EC_DT',label='no DT abs',line_style='--')
    jetset_model.plot_model(plot_obj=p,comp='EC_BLR',label='no BLR abs',line_style='--')
    jetset_model.plot_model(plot_obj=p,comp='EC_Corona',label='no Corona abs',line_style='--')





.. parsed-literal::

    <jetset.plot_sedfit.PlotSED at 0x313d2bec0>




.. image:: int_abs_files/int_abs_8_1.png


.. code:: ipython3

    
    jetset_model.enable_internal_absorption('DT')
    jetset_model.enable_internal_absorption('BLR')
    jetset_model.enable_internal_absorption('Corona')


This model can be used now for model fitting.

.. code:: ipython3

    jetset_model.show_internal_absorption_components()


.. parsed-literal::

    internal absorption  for component: DT
    ('N_hard', 20)
    ('N_soft', 50)
    ('N_theta', 30)
    ('N_R_H', 20)
    ('nu_min', None)
    ('comp', 'DT')
    ('use_R_H_profile_extrapolation', False)
    ('use_sigma_gamma_gamma_fast', False)
    
    internal absorption  for component: BLR
    ('N_hard', 20)
    ('N_soft', 50)
    ('N_theta', 30)
    ('N_R_H', 20)
    ('nu_min', None)
    ('comp', 'BLR')
    ('use_R_H_profile_extrapolation', False)
    ('use_sigma_gamma_gamma_fast', False)
    
    internal absorption  for component: Corona
    ('N_hard', 20)
    ('N_soft', 50)
    ('N_theta', 30)
    ('N_R_H', 20)
    ('nu_min', None)
    ('comp', 'Corona')
    ('use_R_H_profile_extrapolation', False)
    ('use_sigma_gamma_gamma_fast', False)
    



Please, notice that the internal absorption will slow down a bit the
computation. Let’s quantify the effect for a single component. First we
remove the BLR and Corona absorption, to assess the impact of a single
component.

.. code:: ipython3

    jetset_model.remove_internal_absorption('BLR')
    jetset_model.remove_internal_absorption('Corona')
    
    jetset_model.show_internal_absorption_components()


.. parsed-literal::

    internal absorption  for component: DT
    ('N_hard', 20)
    ('N_soft', 50)
    ('N_theta', 30)
    ('N_R_H', 20)
    ('nu_min', None)
    ('comp', 'DT')
    ('use_R_H_profile_extrapolation', False)
    ('use_sigma_gamma_gamma_fast', False)
    


.. code:: ipython3

    %timeit jetset_model.eval()


.. parsed-literal::

    8.57 ms ± 528 μs per loop (mean ± std. dev. of 7 runs, 100 loops each)


.. code:: ipython3

    jetset_model.remove_internal_absorption('DT')


.. code:: ipython3

    %timeit jetset_model.eval()
    jetset_model.show_internal_absorption_components()


.. parsed-literal::

    8.29 ms ± 61.6 μs per loop (mean ± std. dev. of 7 runs, 100 loops each)
    internal absorption not enabled in this jet model


The increase in computational time, for component, compared to an EC
model without absorption, is of a factor of ~ 2.5

By setting ``use_R_H_profile_extrapolation=True``, we can speed up the
process

.. code:: ipython3

    jetset_model.enable_internal_absorption('DT',use_R_H_profile_extrapolation=False)


.. code:: ipython3

    %timeit _=jetset_model.eval_internal_absorption(comp='DT', peak=False)


.. parsed-literal::

    1.09 ms ± 2.85 μs per loop (mean ± std. dev. of 7 runs, 1,000 loops each)


.. code:: ipython3

    jetset_model.enable_internal_absorption('DT',use_R_H_profile_extrapolation=True)


.. code:: ipython3

    %timeit _=jetset_model.eval_internal_absorption(comp='DT', peak=False)


.. parsed-literal::

    986 μs ± 1.65 μs per loop (mean ± std. dev. of 7 runs, 1,000 loops each)


By setting ``use_sigma_gamma_gamma_fast=True``, we can further speed up
the process, by using interpolated cross section.

.. code:: ipython3

    jetset_model.enable_internal_absorption('DT',use_sigma_gamma_gamma_fast=True,use_R_H_profile_extrapolation=True)


.. code:: ipython3

    %timeit _=jetset_model.eval_internal_absorption(comp='DT', peak=False)


.. parsed-literal::

    591 μs ± 1.26 μs per loop (mean ± std. dev. of 7 runs, 1,000 loops each)


.. code:: ipython3

    jetset_model.enable_internal_absorption('DT',use_R_H_profile_extrapolation=True,use_sigma_gamma_gamma_fast=True)
    
    %timeit jetset_model.eval()


.. parsed-literal::

    8.33 ms ± 120 μs per loop (mean ± std. dev. of 7 runs, 100 loops each)


.. code:: ipython3

    jetset_model.enable_internal_absorption('DT',use_R_H_profile_extrapolation=True)
    jetset_model.enable_internal_absorption('BLR',use_R_H_profile_extrapolation=True)
    jetset_model.enable_internal_absorption('Corona',use_R_H_profile_extrapolation=True)
    
    
    %timeit jetset_model.eval()


.. parsed-literal::

    8.36 ms ± 44.6 μs per loop (mean ± std. dev. of 7 runs, 100 loops each)


The increase in computational time is lower now, down to a factor of
~1.1 per absorption component.

In the following some plots showing the accuracy effect when using
``use_R_H_profile_extrapolation=True``, so you can choose accordingly,
if whether to use the approximation or not.

.. code:: ipython3

    jetset_model.remove_internal_absorption('BLR')
    jetset_model.remove_internal_absorption('DT')
    
    jetset_model.enable_internal_absorption('DT',use_R_H_profile_extrapolation=True,use_sigma_gamma_gamma_fast=True)
    jetset_model.enable_internal_absorption('BLR',use_R_H_profile_extrapolation=True,use_sigma_gamma_gamma_fast=True)
    
    %timeit jetset_model.eval()


.. parsed-literal::

    8.34 ms ± 25.1 μs per loop (mean ± std. dev. of 7 runs, 100 loops each)


.. code:: ipython3

    from matplotlib import pylab as plt
    jetset_model.parameters.tau_BLR.val=0.1
    #jetset_model.parameters.T_DT.val=1000
    fig,axs=plt.subplots(3,1,figsize=(8,10))
    R_DT=jetset_model.parameters.R_DT.val
    colors = ['C0','C1','C2']
    comp='DT'
    jetset_model.enable_internal_absorption(comp,N_R_H=50,N_theta=50,N_soft=50,N_hard=50,use_R_H_profile_extrapolation=False)
    nu_hard_min=1E25
    nu_tau=np.logspace(np.log10(nu_hard_min),30,100)
    
    for ID,f in enumerate([.1,1.5,20]):
        jetset_model.parameters.R_H.val=R_DT*f
        jetset_model.eval()
        
        y,x=jetset_model.eval_internal_absorption(comp=comp,peak=False,nu=nu_tau)
        
        x=x*u.Hz
        msk=y>1E-10
    
        axs[0].loglog( x[msk],y[msk],c=colors[ID],label='R_H/R_target=%2.2f, R_H=%2.2e cm '%(f,jetset_model.parameters.R_H.val))
    
        jetset_model.enable_internal_absorption(comp,N_R_H=50,N_theta=50,N_soft=50,N_hard=50,use_R_H_profile_extrapolation=True,use_sigma_gamma_gamma_fast=False)
        y1,x=jetset_model.eval_internal_absorption(comp=comp,peak=False,nu=nu_tau)
        x=x*u.Hz
        axs[0].loglog( x[msk] ,y1[msk] ,'--',c=colors[ID])
    
        axs[1].loglog( x[msk],np.fabs(y1[msk] -y [msk])/y[msk],'--',c=colors[ID])
    
        axs[2].loglog( x[msk],np.exp(-y[msk]),'-',c=colors[ID])
        axs[2].loglog( x[msk],np.exp(-y1[msk]),'--',c=colors[ID])
        x_min=min(nu_hard_min,x[msk].value.min()/2)
    axs[0].set_xlim(x_min,1E30)
    axs[1].set_xlim(x_min,1E30)
    axs[2].set_xlim(x_min,1E30)
    axs[0].legend()
    
    
    axs[1].axhline(.2)
    
    axs[0].set_ylim(1E-10,1E3)
    axs[1].set_ylim(1E-5,2)
    axs[2].set_ylim(1E-30,)
    axs[0].set_xlabel(r'$\nu$')
    axs[1].set_xlabel(r'$\nu$')
    axs[2].set_xlabel(r'$\nu$')
    
    axs[0].set_ylabel(r'$\tau$')
    axs[1].set_ylabel(r'relative error')
    axs[2].set_ylabel(r'$exp^{-\tau}$')
    plt.tight_layout()
    plt.grid()



.. image:: int_abs_files/int_abs_30_0.png


.. code:: ipython3

    from matplotlib import pylab as plt
    jetset_model.parameters.tau_BLR.val=0.1
    #jetset_model.parameters.T_DT.val=1000
    fig,axs=plt.subplots(3,1,figsize=(8,10))
    R_BLR=jetset_model.parameters.R_BLR_in.val
    colors = ['C0','C1','C2']
    comp='BLR'
    jetset_model.enable_internal_absorption(comp,N_R_H=50,N_theta=50,N_soft=50,N_hard=50,use_R_H_profile_extrapolation=False)
    nu_hard_min=1E24
    nu_tau=np.logspace(np.log10(nu_hard_min),30,100)
    for ID,f in enumerate([.1,1.5,50]):
        jetset_model.parameters.R_H.val=R_BLR*f
        jetset_model.eval()
        jetset_model.enable_internal_absorption(comp,N_R_H=50,N_theta=50,N_soft=50,N_hard=50,use_R_H_profile_extrapolation=False)
        y,x=jetset_model.eval_internal_absorption(comp=comp,peak=False,nu=nu_tau)
    
        x=x*u.Hz
        msk=y>1E-10
    
        axs[0].loglog( x[msk],y[msk],c=colors[ID],label='R_H/R_target=%2.2f, R_H=%2.2e cm '%(f,jetset_model.parameters.R_H.val))
    
        jetset_model.enable_internal_absorption(comp,N_R_H=50,N_theta=50,N_soft=50,N_hard=50,use_R_H_profile_extrapolation=True,use_sigma_gamma_gamma_fast=False)
        y1,x=jetset_model.eval_internal_absorption(comp=comp,peak=False,nu=nu_tau)
        x=x*u.Hz
        axs[0].loglog( x[msk] ,y1[msk] ,'--',c=colors[ID])
        axs[1].loglog( x[msk],np.fabs(y1[msk] -y [msk])/y[msk],'--',c=colors[ID])
    
        axs[2].loglog( x[msk],np.exp(-y[msk]),'-',c=colors[ID])
        axs[2].loglog( x[msk],np.exp(-y1[msk]),'-+',c=colors[ID])
        x_min=min(nu_hard_min,x[msk].value.min()/2)
    axs[0].set_xlim(x_min,1E30)
    axs[1].set_xlim(x_min,1E30)
    axs[2].set_xlim(x_min,1E30)
    axs[0].legend()
    
    axs[1].axhline(.2)
    
    axs[0].set_ylim(1E-10,1E3)
    axs[1].set_ylim(1E-3,1)
    axs[2].set_ylim(1E-10,)
    axs[0].set_xlabel(r'$\nu$')
    axs[1].set_xlabel(r'$\nu$')
    axs[2].set_xlabel(r'$\nu$')
    
    axs[0].set_ylabel(r'$\tau$')
    axs[1].set_ylabel(r'relative error')
    axs[2].set_ylabel(r'$exp^{-\tau}$')
    plt.tight_layout()
    plt.grid()



.. image:: int_abs_files/int_abs_31_0.png


.. code:: ipython3

    from matplotlib import pylab as plt
    #jetset_model.parameters.T_DT.val=1000
    fig,axs=plt.subplots(3,1,figsize=(8,10))
    jetset_model.parameters.R_H_Corona.val=1E13
    colors = ['C0','C1','C2']
    comp='Corona'
    nu_hard_min=1E24
    nu_tau=np.logspace(np.log10(nu_hard_min),30,100)
    jetset_model.enable_internal_absorption(comp,N_R_H=50,N_theta=50,N_soft=50,N_hard=50,use_R_H_profile_extrapolation=False)
    
    R_H_corona=jetset_model.parameters.R_H_Corona.val
    for ID,f in enumerate([1.5,5,10]):
        jetset_model.parameters.R_H.val=R_H_corona*f
        jetset_model.eval()
        
        y,x=jetset_model.eval_internal_absorption(comp=comp,peak=False,nu=nu_tau)
        x=x*u.Hz
        msk=y>1E-10
    
        axs[0].loglog( x[msk],y[msk],c=colors[ID],label='R_H/R_target=%2.2f, R_H=%2.2e cm '%(f,jetset_model.parameters.R_H.val))
    
        jetset_model.enable_internal_absorption(comp,N_R_H=50,N_theta=50,N_soft=50,N_hard=50,use_R_H_profile_extrapolation=True,use_sigma_gamma_gamma_fast=False)
        y1,x=jetset_model.eval_internal_absorption(comp=comp,peak=False,nu=nu_tau)
        x=x*u.Hz
        axs[0].loglog( x[msk] ,y1[msk] ,'--',c=colors[ID])
    
        axs[1].loglog( x[msk],np.fabs(y1[msk] -y [msk])/y[msk],'--',c=colors[ID])
    
        axs[2].loglog( x[msk],np.exp(-y[msk]),'-',c=colors[ID])
        axs[2].loglog( x[msk],np.exp(-y1[msk]),'--',c=colors[ID])
        x_min=min(nu_hard_min,x[msk].value.min()/2)
        print(np.max(np.fabs(y1[msk] -y [msk])/y[msk]))
    axs[0].set_xlim(x_min,1E30)
    axs[1].set_xlim(x_min,1E30)
    axs[2].set_xlim(x_min,1E30)
    axs[0].legend()
    
    
    
    axs[1].axhline(.2)
    
    axs[0].set_ylim(1E-5,1E2)
    #axs[1].set_ylim(1E-5,1)
    axs[2].set_ylim(1E-30,)
    axs[0].set_xlabel(r'$\nu$')
    axs[1].set_xlabel(r'$\nu$')
    axs[2].set_xlabel(r'$\nu$')
    
    
    axs[0].set_ylabel(r'$\tau$')
    axs[1].set_ylabel(r'relative error')
    axs[2].set_ylabel(r'$exp^{-\tau}$')
    
    plt.tight_layout()
    plt.grid()


.. parsed-literal::

    3.593865310093714e-10
    0.0
    0.0



.. image:: int_abs_files/int_abs_32_1.png


.. code:: ipython3

    from matplotlib import pylab as plt
    jetset_model.parameters.tau_BLR.val=0.1
    #jetset_model.parameters.T_DT.val=1000
    fig,axs=plt.subplots(3,1,figsize=(8,10))
    R_H_corona=jetset_model.parameters.R_H_Corona.val
    colors = ['C0','C1','C2']
    comp='Corona'
    jetset_model.enable_internal_absorption(comp,N_R_H=50,N_theta=50,N_soft=50,N_hard=50,use_R_H_profile_extrapolation=False)
    nu_hard_min=1E24
    nu_tau=np.logspace(np.log10(nu_hard_min),30,100)
    for ID,f in enumerate([2.,5,10]):
        jetset_model.parameters.R_H.val=R_H_corona*f
        jetset_model.eval()
        jetset_model.enable_internal_absorption(comp,N_R_H=50,N_theta=50,N_soft=50,N_hard=50,use_R_H_profile_extrapolation=False)
        y,x=jetset_model.eval_internal_absorption(comp=comp,peak=False,nu=nu_tau)
        msk=y>1E-10
    
        x=x*u.Hz
        msk=y>1E-10
    
        axs[0].loglog( x[msk],y[msk],c=colors[ID],label='R_H/R_target=%2.2f, R_H=%2.2e cm '%(f,jetset_model.parameters.R_H.val))
    
        jetset_model.enable_internal_absorption(comp,N_R_H=50,N_theta=20,N_soft=20,N_hard=50,use_R_H_profile_extrapolation=False,use_sigma_gamma_gamma_fast=False)
        y1,x=jetset_model.eval_internal_absorption(comp=comp,peak=False,nu=nu_tau)
        x=x*u.Hz
        axs[0].loglog( x[msk] ,y1[msk] ,'--',c=colors[ID])
    
        axs[1].loglog( x[msk],np.fabs(y1[msk] -y [msk])/y[msk],'--',c=colors[ID])
    
        axs[2].loglog( x[msk],np.exp(-y[msk]),'-',c=colors[ID])
        axs[2].loglog( x[msk],np.exp(-y1[msk]),'--',c=colors[ID])
        try:
            x_min=min(nu_hard_min,x[msk].value.min()/2)
        except:
            x_min=None
    axs[0].set_xlim(x_min,1E30)
    axs[1].set_xlim(x_min,1E30)
    axs[2].set_xlim(x_min,1E30)
    axs[0].legend()
    
    
    axs[1].axhline(.5)
    
    axs[0].set_ylim(1E-10,5E3)
    axs[1].set_ylim(1E-3,2)
    axs[2].set_ylim(1E-5,)
    axs[0].set_xlabel(r'$\nu$')
    axs[1].set_xlabel(r'$\nu$')
    axs[2].set_xlabel(r'$\nu$')
    
    axs[0].set_ylabel(r'$\tau$')
    axs[1].set_ylabel(r'relative error')
    axs[2].set_ylabel(r'$exp^{-\tau}$')
    plt.tight_layout()
    plt.grid()



.. image:: int_abs_files/int_abs_33_0.png


.. code:: ipython3

    jetset_model.enable_internal_absorption(comp,N_R_H=50,N_theta=20,N_soft=20,N_hard=20,use_R_H_profile_extrapolation=False,use_sigma_gamma_gamma_fast=True)
    %timeit y1,x=jetset_model.eval_internal_absorption(comp=comp,peak=False)


.. parsed-literal::

    6.14 ms ± 18 μs per loop (mean ± std. dev. of 7 runs, 100 loops each)


.. code:: ipython3

    jetset_model.enable_internal_absorption(comp,use_R_H_profile_extrapolation=True)
    %timeit y1,x=jetset_model.eval_internal_absorption(comp=comp,peak=False)


.. parsed-literal::

    899 μs ± 9.69 μs per loop (mean ± std. dev. of 7 runs, 1,000 loops each)


.. code:: ipython3

    j=Jet()

.. code:: ipython3

    nu=np.logspace(7,30,500)
    y=j.eval(nu=nu,get_model=True)
    nu_sync, y_sync = j.spectral_components.Sync.get_SED_points(lin_nu=nu, log_log=False, interp=j._jetkernel_interp)
    nu_ssc,  y_ssc  = j.spectral_components.SSC.get_SED_points(lin_nu=nu, log_log=False, interp=j._jetkernel_interp)
    y1=j.eval(nu=nu,get_model=True)
    nu_sync, y_sync_1 = j.spectral_components.Sync.get_SED_points(lin_nu=nu, log_log=False, interp=j._jetkernel_interp)
    nu_ssc,  y_ssc_1  = j.spectral_components.SSC.get_SED_points(lin_nu=nu, log_log=False, interp=j._jetkernel_interp)

.. code:: ipython3

    import numpy as np
    from jetset.jet_model import Jet
    
    
    def build_jet():
        j = Jet(name="compact_int_abs", emitters_distribution="bkn", beaming_expr="bulk_theta")
        j.add_EC_component(EC_components_list=["EC_DT", "EC_BLR","EC_Corona"], disk_type="BB")
    
        j.parameters.z_cosm.val = 0.03
        j.parameters.L_Disk.val = 2e45
        j.parameters.R_H.val = 1e18
        j.parameters.tau_DT.val = 0.1
        j.parameters.tau_BLR.val = 0.1
        j.parameters.L_Corona.val = 5e44
        j.parameters.R_Corona.val = 5e15
        j.parameters.R_H_Corona.val = 2e17
        j.parameters.alpha_Corona.val = 1.1
        j.parameters.nu_cut_Corona.val = 1e20
        j.parameters.B.val = 0.2
    
        j.set_gamma_grid_size(120)
        j.set_IC_nu_size(80)
        return j
    
    
    def stats(label, num, den):
        ratio = np.divide(num, den, out=np.full_like(num, np.nan), where=(den != 0))
        m = np.isfinite(ratio)
        print(f"{label}:")
        print(f"  max_ratio = {np.nanmax(ratio[m]):.6g}")
        print(f"  min_ratio = {np.nanmin(ratio[m]):.6g}")
        print(f"  n_ratio_gt_1.0001 = {np.sum(ratio[m] > 1.0001)}")
        print(f"  n_ratio_lt_0.9999 = {np.sum(ratio[m] < 0.9999)}")
        return ratio
    
    
    j = build_jet()
    nu = np.logspace(20, 29, 120)
    
    # Cold vs warm, NO IA
    y_cold = np.asarray(j.eval(nu=nu, get_model=True), dtype=float)
    y_warm = np.asarray(j.eval(nu=nu, get_model=True), dtype=float)
    stats("NO IA (warm / cold)", y_warm, y_cold)
    
    # Enable IA
    j.enable_internal_absorption("DT", N_soft=12, N_hard=12, N_R_H=10, N_theta=10, use_sigma_gamma_gamma_fast=True)
    j.enable_internal_absorption("BLR", N_soft=12, N_hard=12, N_R_H=10, N_theta=10)
    j.enable_internal_absorption("Corona", N_soft=12, N_hard=12, N_R_H=10, N_theta=10)
    
    y_ia = np.asarray(j.eval(nu=nu, get_model=True), dtype=float)
    
    stats("IA / cold", y_ia, y_cold)
    r_ia_warm = stats("IA / warm", y_ia, y_warm)
    
    idx = np.where(np.isfinite(r_ia_warm) & (r_ia_warm > 1.0001))[0]
    print(f"IA/warm bins > 1.0001: {idx.size}")
    if idx.size:
        print("first bins > 1.0001:", idx[:10])



.. parsed-literal::

    NO IA (warm / cold):
      max_ratio = 1
      min_ratio = 1
      n_ratio_gt_1.0001 = 0
      n_ratio_lt_0.9999 = 0
    IA / cold:
      max_ratio = 1
      min_ratio = 1.07958e-21
      n_ratio_gt_1.0001 = 0
      n_ratio_lt_0.9999 = 38
    IA / warm:
      max_ratio = 1
      min_ratio = 1.07958e-21
      n_ratio_gt_1.0001 = 0
      n_ratio_lt_0.9999 = 38
    IA/warm bins > 1.0001: 0


.. code:: ipython3

    from matplotlib import pylab as plt
    plt.loglog(nu,y_ia)
    plt.loglog(nu,y_cold,'+')
    plt.loglog(nu,y_warm,'--')




.. parsed-literal::

    [<matplotlib.lines.Line2D at 0x3195d9670>]




.. image:: int_abs_files/int_abs_39_1.png


.. code:: ipython3

    j = build_jet()
    nu = np.logspace(20, 29, 120)
    
    # Cold vs warm, NO IA
    #print(j._blob.core.dist,j.cosmo._c)
    j.eval()
    #print(j._blob.core.dist,j.cosmo._c)
    
    print(np.trapz(j.spectral_components.Corona.SED.nuLnu_src/j.spectral_components.Corona.SED.nu_src,j.spectral_components.Corona.SED.nu_src,))
    p=j.plot_model(comp='Corona',frame='src')
    j.eval()
    print(np.trapz(j.spectral_components.Corona.SED.nuLnu_src/j.spectral_components.Corona.SED.nu_src,j.spectral_components.Corona.SED.nu_src,))
    
    p=j.plot_model(plot_obj=p,comp='Corona',line_style='--',frame='src')
    j.eval()
    p=j.plot_model(plot_obj=p,comp='Corona',line_style='--',frame='src')


.. parsed-literal::

    /var/folders/rs/w64c54l549x1jl7cp3m6x_q00000gn/T/ipykernel_9491/691292194.py:9: DeprecationWarning: `trapz` is deprecated. Use `trapezoid` instead, or one of the numerical integration functions in `scipy.integrate`.
      print(np.trapz(j.spectral_components.Corona.SED.nuLnu_src/j.spectral_components.Corona.SED.nu_src,j.spectral_components.Corona.SED.nu_src,))


.. parsed-literal::

    4.92655058833799e+44 erg / s


.. parsed-literal::

    /var/folders/rs/w64c54l549x1jl7cp3m6x_q00000gn/T/ipykernel_9491/691292194.py:12: DeprecationWarning: `trapz` is deprecated. Use `trapezoid` instead, or one of the numerical integration functions in `scipy.integrate`.
      print(np.trapz(j.spectral_components.Corona.SED.nuLnu_src/j.spectral_components.Corona.SED.nu_src,j.spectral_components.Corona.SED.nu_src,))


.. parsed-literal::

    4.92655058833799e+44 erg / s



.. image:: int_abs_files/int_abs_40_4.png


.. code:: ipython3

    j = build_jet()
    #print(j._blob.core.dist,j.cosmo._c)
    j.get_DL_cm(eval_model=True)
    print(j._blob.core.dist,j.cosmo._c)
    j.eval()
    print(j._blob.core.dist,j.cosmo._c)
    j.get_DL_cm()
    print(j._blob.core.dist,j.cosmo._c)


.. parsed-literal::

    4.188398477600799e+26 FlatLambdaCDM(name="Planck13", H0=67.77 km / (Mpc s), Om0=0.30712, Tcmb0=2.7255 K, Neff=3.046, m_nu=[0.   0.   0.06] eV, Ob0=0.048252)
    4.188398477600799e+26 FlatLambdaCDM(name="Planck13", H0=67.77 km / (Mpc s), Om0=0.30712, Tcmb0=2.7255 K, Neff=3.046, m_nu=[0.   0.   0.06] eV, Ob0=0.048252)
    4.188398477600799e+26 FlatLambdaCDM(name="Planck13", H0=67.77 km / (Mpc s), Om0=0.30712, Tcmb0=2.7255 K, Neff=3.046, m_nu=[0.   0.   0.06] eV, Ob0=0.048252)


