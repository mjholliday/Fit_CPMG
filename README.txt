#README for Fit_batch_cpmg.exe

A routine allowing fitting of CPMG RD data from one or multiple residues to the Carver Richards equation, the slow limit equation, and/or the fast limit equation. Run by:

>Fit_cpmg_batch.exe Example.inp

Where Example.inp is an input file as described below

Input File Format

Each set of data is defined to be associated with a given atom and group
  Each atom has a single dw 
  Each group has a single kex and PA

Multiple line types are allowed, at least one valid data line and an output line are required. Lines not beginning with line type keywords will be ignored

Line type: data file
Line format: D_FILE data_file_# atom_# group_# filename res_num field_strength temperature atom_type
Line example:  D_FILE 3 2 2 ./cpmg_data.txt 12 900 10 N
Line description: Input any number of data files. Each line of each data file should list a vcpmg frequency in Hz and a R2 value. For each file, specify the file number (must be different for all data files), atom number, and group number. Program will fit an R0 for every data file, a dw for every atom, and a PA & kex for every group. Field strength is given in MHz, for proton.

Line type: initial conditions
Line descripton:  Entering initial conditions is optional, will default to ic's listed below for any that are not specified set is the data_file_# for parameter R0, atom_# for parameter dw, or group_# for PA or kex. Boundary conditions are also optional. If one boundary condition is specified, both must be. Fit will initially be attempted w/o boundary conditions, and they will be enforced if fit exceeds boundary conditions. 
Line format: IC set param ic boundary_condition_lower(optional) boundary_condition_upper(optional)
Line example:  IC 2 kex 1500
Line example:  IC 1 dw 2 0.1 5

Line type: option
Line description:  Various options, listed below
Line format: OPTION opt
opt may be set to:
COMPARE_MODEL_FIT: only works if multiple groups are specified, will also calculate fit with all data/atoms a single group, with only one kex/PA then compare the fits using both F-test and AIC
CALC_SLOW_EQ: will also generate fit to slow equation, and compare to CR using AIC
CALC_FAST_EQ: will also generate fit to fast equation, and compare to CR using AIC
FIT_GROUPS_SEPARATE: will run minimization separately for each group (requires no atom to be part of several groups, will return error if so.
WEIGHTS X: Weights data by ~1/(Rex^X), default is no weighting (X=0).  Rex is estimated by the difference btwn largest and smallest R2 values within a data set.  All data for a given atom are scaled by the same value, the data set with the largest Rex.

Line type: output file
Line description: File to which output from fitting routine will be printed
Line format: O_FILE ./output.txt

Line type: print file
Line description: Will print data and fits to an eps file if included
Line format: P_file ./output.eps
Flag –norm will normalize all plots to the same Rex

Example Input Files

Simple_fit.inp:
#Will fit data from a single residue to the Carver-Richards Equation, the slow #equation, and the fast equation, and compare the fits
D_FILE 1 1 1 ./Res45_10d_900.dat 45 900 10 N
D_FILE 2 1 1 ./Res45_10d_600.dat 45 600 10 N

OPTION CALC_SLOW_EQ
OPTION CALC_FAST_EQ
OPTION WEIGHTS 1

O_FILE ./45_10d.out
P_FILE ./45_10d.eps –norm

Multi_fit.inp:
#Will fit data from a multiple residues to the Carver-Richards Equation and
#compare the fit when fitting each residue separately to the fit when all are
#fit together
D_FILE 1 1 1 ./Res45_10d_900.dat 45 900 10 N
D_FILE 2 1 1 ./Res45_10d_600.dat 45 600 10 N
D_FILE 3 2 2 ./Res55_10d_900.dat 55 900 10 N
D_FILE 4 2 2 ./Res55_10d_600.dat 55 600 10 N

OPTION COMPARE_MODEL_FIT
OPTION FIT_GROUPS_SEPARATE
OPTION WEIGHTS 1

O_FILE ./45_55_10d.out
P_FILE ./45_55_10d.eps -norm

