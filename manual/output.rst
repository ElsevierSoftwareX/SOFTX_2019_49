QXMD Simulation Quick-Start
===========================

The **Examples** directory holds examples of various QXMD simulations
for a variety of systems. Examining the input files in these examples
may be the best way to begin to get an intuition for how to run your
first QXMD simulation. While all the input parameters are given in
section 5, many of these parameters have default settings that need not
be changed, and many of the input sections can be left out of the input
file for many standard (simple) QXMD simulations. Thus, what follows can
be viewed as a quick-start guide for getting your first QXMD simulation
running as soon as possible. To run more complex simulations, the reader
should refer to input parameter settings in Section 5 and sample input
files given in the **Examples** directory.


6.1 Minimal IN.PARAM file
~~~~~~~~~~~~~~~~~~~~~~~~~

Given below is an sample input file for the optimization of a water
molecule which includes all the most important input parameters for
running standard QXMD simulations. Most users will find this input file
sufficient for running optimization and quantum molecular dynamics jobs.
Of course, input parameters given in section 5 can be added to this
input file (an example of how to add a new section of variables will be
shown in the next section on running a non-adiabatic QMD simulation) to
tailor a QXMD job to the user's specifications.

::

   ---(Input-parameter file for QXMD)---
                                 :
   *parallel                     :
   (QM-nodes)                    :
     1  1  1                     : (npx, npy, npz)
   (MD-nodes)                    :
     1  1  1                     : (md_npx, md_npy, md_npz)
   *end                          :
                                 :
                                 :
   *start(on/off)                :
   (how of it)                   :
    .false.                       : (lstart) .true. = restart
   *end                          :
                                 :
                                 :
   *approximation for Exc        :
   (approximation)               :
          2                      : (jgga) = 1:LDA, 2:GGA(PBE), 3:GGA(RPBE),
                                 :          4:GGA(revPBE), 5:vdW-DF, 6:vdW-DF2
                                 :
   (DFT-D)                       : an empirical correction for the vdW interaction
    .true.                       : (ldftd) .true. = on, .false. = off
                                 :
   *end                          :
                                 :
                                 :
                                 :
   *SCF iterations               :
   (global iterations)           :
        100                      : (iscfmx) maximum No. of global iterations
   (tolerances)                  :
         3.0d-08                 : (tolpot) tolerance for total energy
         5.0d-08                 : (tolres) tolerance for average residual
   *end                          :
                                 :
                                 :
   *molecular dynamics           :
   (how of it)                   :
      1                          : (ifmd)
                                 :   0:non, 1:optimization, 2:NVE, 3:NVT, 4:NPT
   (time step)                   :
      50.0d0   1000               : (dtmd, nstop) time step, total step
   (temperature)                 : only for real dynamics (NVE-, NVT-, NPT-MD )
      300.d0                     : (treq) temperature in [K]
   (check temperature)           :
     .true.                     : (liscale) .true. = Do it !
      25                         : (iscnum)  number of temperature check
      20                         : (iscstp)  skip step
                                 :
   (optimization)                : only for structural optimization (ifmd == 1 )
       2                         : (ioptmze)
                                 :  -1: do not optimize atomic coordinates
                                 :   0: Conjugate gradient
                                 :   1: Projected velocity Verlet
                                 :   2: Quasi-Newton method with BFGS formula
                                 :
   (stabilizer for quasi-Newton) : only for quasi-Newton method (ifmd==1 & ioptmze==2)
      0.1d0                      : (gammamin)
                                 :
   (clear Hessian)               : only for quasi-Newton method (ifmd==1 & ioptmze==2)
       0                         : (ibfgsclear) clear Hessian every ibfgsclear step
                                 :           if ibfgsclear == 0, Hessian is not cleared.
                                 :
   (atomic stress & energy)      : only for MD nodes
    .true.                       : (latomic) .true. = output atomic stress & energy
                                 : Note that nskip_atomic = ioskip
                                 : When lstress = false, atomic stress is not output.
                                 :
   (tolerance)                   : tolerance for CG optimization (ifmd == 1 )
     1.d-07                      : (tol_energy) energy/atom in [a.u.]
     5.d-04                      : (tol_force ) max. force  in [a.u.]
                                 :
   (output data)                 : only for MD nodes
       1                         : (ioskip)  skip step
    .true.    1                  : (locoor, ioskipcoor) scaled coordinates
    .false.    1                  : (lovelo, ioskipvelo) scaled velocities
    .false.    1                  : (loforc, ioskipforc) scaled forces
   *end                          :
   ------------------------------:---------------------------------------
   *supercell                    :
   (unit of length)              :
   (ang)                         : (bohr) or (ang)
                                 :
   (lengths & angles)            :
   7.00d0, 7.00d0, 5.0d0         :  lengths of cell vectors
   90.000,  90.000,  90.000  :  angles between cell vec. in [deg.]
   *end                          :
                                 :
   *planewaves                   :
   (unit of cutoff energy)       :
   (ry)                          : (ry) or (hr) or (ev)
   (for wavefunctions)           :
     30.0                        : (ecut)
   (for electron density)        :
    250.0                        : (ecutdens)
   (for soft part of density)    :
    70.0                         : (ecutsoft)
   *end                          :
                                 :
   *electronic bands             :
   (occupied bands)              :
         8                     : (noband)  No. of occupied bands
   (empty bands)                 :
         2                     : (neband)  No. of empty bands
                                 :           total No.= noband + neband
   (broadening)                  :
          3    500.d0            : (lfermi) = 1:nonmetallic, 2:Fermi, 3:Gaussian,
                                 :   lfermi(>3):Methfessel & Paxton, order=lfermi-3
                                 : (tfermi) = electronic temp.(K), if metallic
   *end                          :
                                 :
   ------------------------------:---------------------------------------
   *atoms                        :
   (species)                     :
     2                           : (ntype) No. of atomic species
   ==============================:=======================================
   (atomic number)               :
     8.0                         : (zatom)
                                 :
   (the number of atoms)         : No. of atoms in SUPERCELL, not in UNIT cell
   0                             : (nhk) If zero, atomic positions are read from 
                                 :       'CONFIGURATION_FILE'.
                                 :
   (position file)               : 
   'control/IN.CONFIG'           :
    2                            : 1:scaled, 2:real coordinates
    1                            : (keyword)
   (end)                         :
   ==============================:=======================================
   (atomic number)               :
     1.0                         : (zatom)
                                 :
   (the number of atoms)         : No. of atoms in SUPERCELL, not in UNIT cell
   0                             : (nhk) If zero, atomic positions are read from 
                                 :       'CONFIGURATION_FILE'.
                                 :
   (position file)               : 
   'control/IN.CONFIG'           :
    2                            : 1:scaled, 2:real coordinates
    2                            : (keyword)
   (end)                         :
   ==============================:=======================================
   *end                          : end of setting *atoms

In fact, many of these parameters need not be defined for an
optimization run, but are included so that only two things need to be
changed to make this input file run a quantum molecular dynamics (QMD)
simulation. First and foremost, the '(how of it)' parameter (ifmd) in
the \*molecular dynamics section must be changed. Above, this parameter
is set to 1, which will execute an optimization run, but setting this
parameter to 3 (as shown below) will execute a QMD simulation in the NVT
ensemble (this can also be set to '2' for the NVE ensemble or '4' for
the NPT ensemble).

::

   *molecular dynamics           :
   (how of it)                   :
      3                          : (ifmd)
                                 :   0:non, 1:optimization, 2:NVE, 3:NVT, 4:NPT

The other thing that must be changed is the input configuration file.
Usually, you will want to start a QMD simulation from the optimized
geometry. The optimized atomic positions are obtained after an
optimization run using the 'pick_config.f90' utility file (see section
7.2). One can either give the optimized geometry configuration file a
new name and update this in the (position file) parameter in the \*atoms
section, or replace IN.CONFIG with the new geometry. Once the '(how of
it)' parameter in the \*molecular dynamics section and the input
configuration file is adjusted to the optimized geometry, the input file
is complete for execution of a standard QMD simulation.


6.2 Adding parameters to IN.PARAM
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Here, we give an example of how to add input parameters to the basic
IN.PARAM file given above in section 6.1. As described in the beginning
of Section 5, the main input file is divided into sections corresponding
to different groups of input parameters. Sections start with
``*SECTION_NAME`` and end with ``*end``. If you would like to add a
parameter from a section that already exists in the input file, you main
simply add in the parameter in any position within the section (i.e.
after the ``\*section`` name and before the corresponding \*end). In order
to add a new input parameter from a section that does not already exist
in the input file, you must add that section (i.e. add the
``\*SECTION_NAME`` and ``\*end`` lines with your new parameter in between) along
with the parameter. You need not add all paramters in the newly added section. Any parameters in the section that are not included will simply
be set to their default values. In this section, we show an example for
altering the above IN.PARAM file in Section 6.1 to execute a
non-adiabtic QMD (NAQMD) simulation. In this case, we must add the
``\*TDDFT-MD`` section (see section 5.3), as time-dependent DFT is required
to run a NAQMD simulation. NAQMD simulations involve exciting electrons
from lower lying energy bands to higher lying ones, which is defined in
the ``\*TDDFT-MD`` section shown below.

::

   ---(Input-parameter file for QXMD)---
                                 :
   *parallel                     :
   (QM-nodes)                    :
     1  1  1                     : (npx, npy, npz)
   (MD-nodes)                    :
     1  1  1                     : (md_npx, md_npy, md_npz)
   *end                          :
                                 :
                                 :
   *start(on/off)                :
   (how of it)                   :
    .false.                       : (lstart) .true. = restart
   *end                          :
                                 :
                                 :
   *approximation for Exc        :
   (approximation)               :
          2                      : (jgga) = 1:LDA, 2:GGA(PBE),
                                 :
   (DFT-D)                       : an empirical correction for the vdW interaction
    .true.                       : (ldftd) .true. = on, .false. = off
                                 :
   *end                          :
                                 :
                                 :
                                 :
   *SCF iterations               :
   (global iterations)           :
        100                      : (iscfmx) maximum No. of global iterations
   (tolerances)                  :
         3.0d-08                 : (tolpot) tolerance for total energy
         5.0d-08                 : (tolres) tolerance for average residual
   *end                          :
                                 :
   *TDDFT-MD                     :
   (how of it)                   :
    .true.                       : (ltddft) .true. = execute MD based on TDDFT
                                 :
   (FSSH)                        :
    .true.                       : (ltddft_fssh) .true. = FSSH, .false. = Ehrenfest
                                 :
   (FSSH-switch)                 :
    .true.                       : (lfssh_switch) .true.  = switching aveilable
                                 :                .false. = cccupations are fixed
   (FSSH-ground-state-SCF)       :
    .true.                       : (lfssh_gsscf) .true.  = SCF with the ground  state
                                 :               .false. = SCF with the excited state
   (FSSH-mixing charge)          : only for lfssh_gsscf = .true.
      0.8d0  0.13d0              : (aslh_fssh, bslh_fssh)
                     :
   (time step)                   :
      0.04d0                     : (dttddft) time step in [Hartree a.u.] in TDDFT-FSSH
                                 :
   (restart)                     :
    .false.                      : (ltddft_start) .true. = restart
                                 :
   (occupations)                 : for lrtddft = .false.
       2                         : (nocc_change) # of occupations to be changed
      4   0.0  0.0              : (numband, occ_new) band index, occupations(up&down)
      5   2.0  0.0              : (numband, occ_new) band index, occupations(up&down)

   *end                          :
                                 :
   *molecular dynamics           :
   (how of it)                   :
      3                          : (ifmd)
                                 :   0:non, 1:optimization, 2:NVE, 3:NVT, 4:NPT
   (time step)                   :
      50.0d0   1000               : (dtmd, nstop) time step, total step
   (temperature)                 : only for real dynamics (NVE-, NVT-, NPT-MD )
      300.d0                     : (treq) temperature in [K]
   (check temperature)           :
     .false.                     : (liscale) .true. = Do it !
      25                         : (iscnum)  number of temperature check
      20                         : (iscstp)  skip step
                                 :
   (optimization)                : only for structural optimization (ifmd == 1 )
       2                         : (ioptmze)
                                 :  -1: do not optimize atomic coordinates
                                 :   0: Conjugate gradient
                                 :   1: Projected velocity Verlet
                                 :   2: Quasi-Newton method with BFGS formula
                                 :
   (stabilizer for quasi-Newton) : only for quasi-Newton method (ifmd==1 & ioptmze==2)
      0.1d0                      : (gammamin)
                                 :
   (clear Hessian)               : only for quasi-Newton method (ifmd==1 & ioptmze==2)
       0                         : (ibfgsclear) clear Hessian every ibfgsclear step
                                 :           if ibfgsclear == 0, Hessian is not cleared.
                                 :
   (atomic stress & energy)      : only for MD nodes
    .true.                       : (latomic) .true. = output atomic stress & energy
                                 : Note that nskip_atomic = ioskip
                                 : When lstress = false, atomic stress is not output.
                                 :
   (tolerance)                   : tolerance for CG optimization (ifmd == 1 )
     1.d-07                      : (tol_energy) energy/atom in [a.u.]
     5.d-04                      : (tol_force ) max. force  in [a.u.]
                                 :
   (output data)                 : only for MD nodes
       1                         : (ioskip)  skip step
    .true.    1                  : (locoor, ioskipcoor) scaled coordinates
    .false.    1                  : (lovelo, ioskipvelo) scaled velocities
    .false.    1                  : (loforc, ioskipforc) scaled forces
   *end                          :
   ------------------------------:---------------------------------------
   *supercell                    :
   (unit of length)              :
   (ang)                         : (bohr) or (ang)
                                 :
   (lengths & angles)            :
   7.00d0, 7.00d0, 5.0d0         :  lengths of cell vectors
   90.000,  90.000,  90.000  :  angles between cell vec. in [deg.]
   *end                          :
                                 :
   *planewaves                   :
   (unit of cutoff energy)       :
   (ry)                          : (ry) or (hr) or (ev)
   (for wavefunctions)           :
     30.0                        : (ecut)
   (for electron density)        :
    250.0                        : (ecutdens)
   (for soft part of density)    :
    70.0                         : (ecutsoft)
   *end                          :
                                 :
   *electronic bands             :
   (occupied bands)              :
         8                     : (noband)  No. of occupied bands
   (empty bands)                 :
         2                     : (neband)  No. of empty bands
                                 :           total No.= noband + neband
   (broadening)                  :
          3    500.d0            : (lfermi) = 1:nonmetallic, 2:Fermi, 3:Gaussian,
                                 :   lfermi(>3):Methfessel & Paxton, order=lfermi-3
                                 : (tfermi) = electronic temp.(K), if metallic
   *end                          :
                                 :
   ------------------------------:---------------------------------------
   *atoms                        :
   (species)                     :
     2                           : (ntype) No. of atomic species
   ==============================:=======================================
   (atomic number)               :
     8.0                         : (zatom)
                                 :
   (the number of atoms)         : No. of atoms in SUPERCELL, not in UNIT cell
   0                             : (nhk) If zero, atomic positions are read from 
                                 :       'CONFIGURATION_FILE'.
                                 :
   (position file)               : 
   'control/IN.CONFIG'           :
    2                            : 1:scaled, 2:real coordinates
    1                            : (keyword)
   (end)                         :
   ==============================:=======================================
   (atomic number)               :
     1.0                         : (zatom)
                                 :
   (the number of atoms)         : No. of atoms in SUPERCELL, not in UNIT cell
   0                             : (nhk) If zero, atomic positions are read from 
                                 :       'CONFIGURATION_FILE'.
                                 :
   (position file)               : 
   'control/IN.CONFIG'           :
    2                            : 1:scaled, 2:real coordinates
    2                            : (keyword)
   (end)                         :
   ==============================:=======================================
   *end                          : end of setting *atoms

Note that the \*TDDFT_MD section can be inserted in between any two
sections. Also note that (check temperature) should be set to .FALSE.
for NAQMD runs. Otherwise, everything else in the IN.PARAM file for a
regular (adiabatic) QMD run may remain the same for an NAQMD run, albeit
with with \*TDDFT-MD section added and set to .TRUE.

