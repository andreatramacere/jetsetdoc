.. _hadronic_pp_jet_guide:

Hadronic pp jet model
=====================

In this section we show the hadronic `pp` implemented for the Jet model. The `pp` implementation is based on the work presented in [Kelner2006]_.

Secondaries :math:`e^{\pm}`, are evolved to the equilibrium following the approach in [Inoue96]_. To speed up the process only synchroton cooling is taken into account. In the next release also the option to switch on IC cooling contribution will be added.  

A validation of the integral solution for the :math:`e^{\pm}` equilibrium used for the pp jet against the Fokker-Plank equation solution, implemented in the  :class:`.JetTimeEvol` class, is presented in  :ref:`hadronic_pp_jet_validation_guide`

We remind the approximation of the only synchrotron cooling is used only for this specific class, and for this first release, and that the :class:`.JetTimeEvol` class offers full cooling access (synchrotron, IC, and adiabatic expansion). See :ref:`temp_ev` for more details.

.. code:: ipython3

    from jetset.jet_model import Jet
    from jetset.jetkernel import jetkernel
    from astropy import constants as const
    from jetset.jet_emitters_factory import EmittersFactory
    import matplotlib.pyplot as plt
    import numpy as np

.. code:: ipython3

    def get_component(jet,j_name,nu_name):
        j_nu_ptr=getattr(jet._blob,j_name)
        nu_ptr=getattr(jet._blob,nu_name)
        xg=np.zeros(jet._blob.nu_grid_size)
        yg=np.zeros(jet._blob.nu_grid_size)
        for i in range(jet._blob.nu_grid_size):
            xg[i]=jetkernel.get_spectral_array(nu_ptr,jet._blob,i)
            yg[i]=jetkernel.get_spectral_array(j_nu_ptr,jet._blob,i)
        
        m=yg>0
    
        xg=xg[m]
        yg=yg[m]
        yg=yg*xg
        yg=yg*jetkernel.erg_to_TeV
    
        xg=xg*jetkernel.HPLANCK_TeV
        
        return xg,yg
    


.. code:: ipython3

    import jetset
    print('tested on jetset',jetset.__version__)


.. parsed-literal::

    tested on jetset 1.3.0rc7


To get an hadronic jet with ``pp`` interaction, we set the
``emitters_type='protons'``

.. code:: ipython3

    j=Jet(emitters_distribution='plc',verbose=False,emitters_type='protons')
    j.parameters.R.val=1E16
    j.parameters.N.val=1000
    j.parameters.B.val=1
    j.parameters.z_cosm.val=0.001
    j.parameters.beam_obj.val=20



.. parsed-literal::

    ===> setting C threads to 12


.. code:: ipython3

    
    j.eval()
    j.show_model()



.. parsed-literal::

    
    --------------------------------------------------------------------------------
    model description: 
    --------------------------------------------------------------------------------
    type: Jet
    name: jet_hadronic_pp  
    geometry: spherical  
    
    protons distribution:
     type: plc  
     gamma energy grid size:  201
     gmin grid : 2.000000e+00
     gmax grid : 1.000000e+06
     normalization:  True
     log-values:  False
    
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
       name:PP_gamma, state: on
       name:PP_gamma, hidden: False
       name:PP_neutrino_tot, state: on
       name:PP_neutrino_tot, hidden: False
       name:PP_neutrino_mu, state: on
       name:PP_neutrino_mu, hidden: False
       name:PP_neutrino_e, state: on
       name:PP_neutrino_e, hidden: False
       name:Bremss_ep, state: on
       name:Bremss_ep, hidden: False
    external fields transformation method: blob
    
    SED info:
     nu grid size jetkernel: 1000
     nu size: 500
     nu mix (Hz): 1.000000e+06
     nu max (Hz): 1.000000e+30
    
    flux plot lower bound   :  1.000000e-30
    
    --------------------------------------------------------------------------------



.. raw:: html

    <i>Table length=11</i>
    <table id="table5825625392-264151" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>par type</th><th>units</th><th>val</th><th>phys. bound. min</th><th>phys. bound. max</th><th>log</th><th>frozen</th></tr></thead>
    <tr><td>jet_hadronic_pp</td><td>R</td><td>region_size</td><td>cm</td><td>1.000000e+16</td><td>1.000000e+03</td><td>1.000000e+30</td><td>False</td><td>False</td></tr>
    <tr><td>jet_hadronic_pp</td><td>R_H</td><td>region_position</td><td>cm</td><td>1.000000e+17</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_hadronic_pp</td><td>B</td><td>magnetic_field</td><td>gauss</td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_hadronic_pp</td><td>beam_obj</td><td>beaming</td><td></td><td>2.000000e+01</td><td>1.000000e-04</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_hadronic_pp</td><td>z_cosm</td><td>redshift</td><td></td><td>1.000000e-03</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_hadronic_pp</td><td>gmin</td><td>low-energy-cut-off</td><td>lorentz-factor*</td><td>2.000000e+00</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>jet_hadronic_pp</td><td>gmax</td><td>high-energy-cut-off</td><td>lorentz-factor*</td><td>1.000000e+06</td><td>1.000000e+00</td><td>1.000000e+15</td><td>False</td><td>False</td></tr>
    <tr><td>jet_hadronic_pp</td><td>N</td><td>emitters_density</td><td>1 / cm3</td><td>1.000000e+03</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_hadronic_pp</td><td>NH_pp</td><td>target_density</td><td>1 / cm3</td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_hadronic_pp</td><td>gamma_cut</td><td>turn-over-energy</td><td>lorentz-factor*</td><td>1.000000e+04</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>jet_hadronic_pp</td><td>p</td><td>LE_spectral_slope</td><td></td><td>2.000000e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
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
        console.log("$('#table5825625392-264151').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table5825625392-264151').dataTable({
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


.. code:: ipython3

    gmin=1.0/jetkernel.MPC2_TeV
    m=j.emitters_distribution.gamma_p>=gmin
    print('U(p) (erg/cm3) =',j.emitters_distribution.eval_U(gmin=gmin))


.. parsed-literal::

    U(p) (erg/cm3) = 5.257679637585933


.. code:: ipython3

    %matplotlib inline
    p=j.emitters_distribution.plot()
    p.setlim(y_min=1E-40)



.. image:: hadronic_files/hadronic_13_0.png


.. code:: ipython3

    %matplotlib inline
    p=j.plot_model()
    p.setlim(y_min=1E-27)



.. image:: hadronic_files/hadronic_14_0.png


Jet pp Consistency with Kelner 2006
-----------------------------------

.. code:: ipython3

    j=Jet(emitters_distribution='plc',verbose=False,emitters_type='protons')
    j.parameters.z_cosm.val=z=0.001
    j.parameters.beam_obj.val=10
    j.parameters.gamma_cut.val=1000/(jetkernel.MPC2_TeV)
    j.parameters.NH_pp.val=1
    j.parameters.N.val=1
    j.parameters.p.val=2.0
    j.parameters.B.val=1.0
    j.parameters.R.val=1E18
    j.parameters.gmin.val=1
    j.parameters.gmax.val=1E8
    j.set_emiss_lim(1E-60)
    j.set_IC_nu_size(100)
    j.gamma_grid_size=200
    j.nu_max=1E31



.. parsed-literal::

    ===> setting C threads to 12


.. code:: ipython3

    gamma_sec_evovled=np.copy(j.emitters_distribution.gamma_e)
    n_gamma_sec_evovled=np.copy(j.emitters_distribution.n_gamma_e)
    gamma_sec_inj=np.copy(j.emitters_distribution.gamma_e_second_inj)
    n_gamma_sec_inj=np.copy(j.emitters_distribution.n_gamma_e_second_inj)


.. code:: ipython3

    gmin=1.0/jetkernel.MPC2_TeV
    j.set_N_from_U_emitters(1.0, gmin=gmin)
    j.eval()
    j.show_model()


.. parsed-literal::

    
    --------------------------------------------------------------------------------
    model description: 
    --------------------------------------------------------------------------------
    type: Jet
    name: jet_hadronic_pp  
    geometry: spherical  
    
    protons distribution:
     type: plc  
     gamma energy grid size:  201
     gmin grid : 1.000000e+00
     gmax grid : 1.000000e+08
     normalization:  True
     log-values:  False
    
    radiative fields:
     seed photons grid size:  100
     IC emission grid size:  100
     source emissivity lower bound :  1.000000e-60
     spectral components:
       name:Sum, state: on
       name:Sum, hidden: False
       name:Sync, state: self-abs
       name:Sync, hidden: False
       name:SSC, state: on
       name:SSC, hidden: False
       name:PP_gamma, state: on
       name:PP_gamma, hidden: False
       name:PP_neutrino_tot, state: on
       name:PP_neutrino_tot, hidden: False
       name:PP_neutrino_mu, state: on
       name:PP_neutrino_mu, hidden: False
       name:PP_neutrino_e, state: on
       name:PP_neutrino_e, hidden: False
       name:Bremss_ep, state: on
       name:Bremss_ep, hidden: False
    external fields transformation method: blob
    
    SED info:
     nu grid size jetkernel: 1000
     nu size: 500
     nu mix (Hz): 1.000000e+06
     nu max (Hz): 1.000000e+31
    
    flux plot lower bound   :  1.000000e-30
    
    --------------------------------------------------------------------------------



.. raw:: html

    <i>Table length=11</i>
    <table id="table5844669232-615173" class="table-striped table-bordered table-condensed">
    <thead><tr><th>model name</th><th>name</th><th>par type</th><th>units</th><th>val</th><th>phys. bound. min</th><th>phys. bound. max</th><th>log</th><th>frozen</th></tr></thead>
    <tr><td>jet_hadronic_pp</td><td>R</td><td>region_size</td><td>cm</td><td>1.000000e+18</td><td>1.000000e+03</td><td>1.000000e+30</td><td>False</td><td>False</td></tr>
    <tr><td>jet_hadronic_pp</td><td>R_H</td><td>region_position</td><td>cm</td><td>1.000000e+17</td><td>0.000000e+00</td><td>--</td><td>False</td><td>True</td></tr>
    <tr><td>jet_hadronic_pp</td><td>B</td><td>magnetic_field</td><td>gauss</td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_hadronic_pp</td><td>beam_obj</td><td>beaming</td><td></td><td>1.000000e+01</td><td>1.000000e-04</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_hadronic_pp</td><td>z_cosm</td><td>redshift</td><td></td><td>1.000000e-03</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_hadronic_pp</td><td>gmin</td><td>low-energy-cut-off</td><td>lorentz-factor*</td><td>1.000000e+00</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>jet_hadronic_pp</td><td>gmax</td><td>high-energy-cut-off</td><td>lorentz-factor*</td><td>1.000000e+08</td><td>1.000000e+00</td><td>1.000000e+15</td><td>False</td><td>False</td></tr>
    <tr><td>jet_hadronic_pp</td><td>N</td><td>emitters_density</td><td>1 / cm3</td><td>1.058009e+02</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_hadronic_pp</td><td>NH_pp</td><td>target_density</td><td>1 / cm3</td><td>1.000000e+00</td><td>0.000000e+00</td><td>--</td><td>False</td><td>False</td></tr>
    <tr><td>jet_hadronic_pp</td><td>gamma_cut</td><td>turn-over-energy</td><td>lorentz-factor*</td><td>1.065789e+06</td><td>1.000000e+00</td><td>1.000000e+09</td><td>False</td><td>False</td></tr>
    <tr><td>jet_hadronic_pp</td><td>p</td><td>LE_spectral_slope</td><td></td><td>2.000000e+00</td><td>-1.000000e+01</td><td>1.000000e+01</td><td>False</td><td>False</td></tr>
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
        console.log("$('#table5844669232-615173').dataTable()");
    
    jQuery.extend( jQuery.fn.dataTableExt.oSort, {
        "optionalnum-asc": astropy_sort_num,
        "optionalnum-desc": function (a,b) { return -astropy_sort_num(a, b); }
    });
    
        $('#table5844669232-615173').dataTable({
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


.. code:: ipython3

    m=j.emitters_distribution.gamma_p>=gmin
    print('U(p) (erg/cm3) =',j.emitters_distribution.eval_U(gmin=gmin))


.. parsed-literal::

    U(p) (erg/cm3) = 1.0


.. code:: ipython3

    %matplotlib inline
    p=j.emitters_distribution.plot()



.. image:: hadronic_files/hadronic_20_0.png


.. code:: ipython3

    #Fig 12 Kelner 2006
    %matplotlib inline
    
    
    #j_nu_pp rate
    xg,yg= get_component(j,'j_pp_gamma','nu_pp_gamma')
    x_nu_e,y_nu_e= get_component(j,'j_pp_neutrino_e','nu_pp_neutrino_e')
    x_nu_mu,y_nu_mu= get_component(j,'j_pp_neutrino_mu','nu_pp_neutrino_mu')
    x_nu_tot,y_nu_tot= get_component(j,'j_pp_neutrino_tot','nu_pp_neutrino_tot')
    x_nu_mu_2=x_nu_mu
    y_nu_2=(y_nu_tot-y_nu_mu)*np.pi*4
    x_nu_mu_1=x_nu_mu
    y_nu_mu_1=(y_nu_mu-y_nu_2)*np.pi*4
    
    yg=yg*np.pi*4
    y_nu_mu=y_nu_mu*np.pi*4
    y_nu_e=y_nu_e*np.pi*4
    #e-  rate
    x_inj=np.copy(j.emitters_distribution.gamma_e_second_inj)
    y_inj=np.copy(j.emitters_distribution.n_gamma_e_second_inj)
    y_e=y_inj*x_inj*x_inj*jetkernel.MEC2_TeV
    x_e=x_inj*0.5E6/1E12
    
    plt.loglog(xg,yg,label='gamma')
    plt.loglog(x_e,y_e,label='e-')
    plt.loglog(x_nu_e,y_nu_e,'--',label='nu_e')
    plt.loglog(x_nu_mu,y_nu_mu,label='nu_mu')
    
    #plt.loglog(x_nu_mu_1,y_nu_mu_1,label='nu_mu_1')
    
    plt.ylim(1E-19,3E-17)#
    plt.xlim(1E-5,1E6)
    
    plt.legend()
    plt.axhline(2.15E-17,ls='--',c='b')
    plt.axhline(8.5E-18,ls='--',c='orange')
    plt.axhline(1.1E-17,ls='--',c='r')





.. parsed-literal::

    <matplotlib.lines.Line2D at 0x15c8dc820>




.. image:: hadronic_files/hadronic_21_1.png


.. code:: ipython3

    #Fig 14 left panel
    %matplotlib inline
    y1=yg/(xg*xg)
    plt.plot(xg*1E6,y1/y1.max(),label='gamma')
    
    y1=y_e/(x_e*x_e)
    m=y_e>0
    plt.plot(x_e[m]*1E6,2*y1[m]/y1[m].max(),label='e-')
    
    #y1=y_nu_tot/(x_nu_tot*x_nu_tot)
    #m=y1>0
    #plt.plot(x_nu_tot[m]*1E6,3*y1[m]/y1[m].max(),label='nu_tot')
    y1=y_nu_mu_1/(x_nu_mu_1*x_nu_mu_1)
    m=y1>0
    plt.plot(x_nu_mu_1[m]*1E6,4*y1[m]/y1[m].max(),label='nu_mu_1')
    
    y1=y_nu_mu/(x_nu_mu*x_nu_mu)
    m=y1>0
    plt.plot(x_nu_mu[m]*1E6,5*y1[m]/y1[m].max(),label='nu_mu')
    
    #plt.xlim(1E-5,2E2)
    plt.axvline(70)
    plt.axvline(50)
    plt.axvline(30)
    plt.legend()
    plt.xlim(10,175)





.. parsed-literal::

    (10, 175)




.. image:: hadronic_files/hadronic_22_1.png


.. bibliography:: references.rst

