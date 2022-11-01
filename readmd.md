@flat35hd99 @yamato97 

# QA calculation result

## Compated environments

1. CURP v1.3.1 https://github.com/yamatolab/current-calculations-for-proteins/tree/v1.3.1
    python environment: ([environment_curp131](curp131.txt))
    ```
        Package      Version
    ------------ ---------
    Benchmarker  4.0.1
    certifi      2020.6.20
    configparser 4.0.2
    Curp         1.3.1
    mpi4py       3.1.3
    netCDF4      1.2.4
    nose         1.3.7
    numpy        1.16.6
    pip          19.3.1
    pygraphviz   1.5
    setuptools   44.1.1
    wheel        0.37.1
    ```

1. CURP ver. develop branch https://github.com/yamatolab/current-calculations-for-proteins/tree/develop
    python environment: ([requirements_py3pr32](py3pr32.txt))
    ```
    Package     Version
    ----------- ---------
    Benchmarker 4.0.1
    certifi     2022.9.24
    cftime      1.6.2
    curp        1.3.1
    mpi4py      3.1.3
    netCDF4     1.6.1
    nose        1.3.7
    numpy       1.23.4
    pip         22.2.2
    pygraphviz  1.10
    setuptools  63.4.1
    wheel       0.37.1
    ```

## QA calculation setup

The calculations of flux and thermal conductivity was conducted under these configurations discripted below.  

The set of input files inlcuding trajectory used in these calculation was shared by @flat35hd99 (you can find and download the tested dataset in our lab's slack)  

1. Flux calculation
    [input/inter_residue_131.cfg](input/inter_residue.cfg)
    ```cfg: inter_residue.cfg
    [input]
    format = amber
    first_last_interval = 1 -1 1

    [input_amber]
    target = trajectory
    topology_file = system.dry.prmtop
    coordinate_format = netcdf
    coordinate_file = md.crd.nc
    velocity_format = netcdf
    velocity_file = md.vel.nc

    [curp]
    potential = amber12SB
    method = heat-flux

    group_method = residue
    flux_grain = group

    remove_trans =  yes
    remove_rotate = yes

    log_frequency = 1000

    [output]
    filename = flux.nc
    format = netcdf
    decomp = no

    output_energy = no
    ```

1. Thermal conductivity calculation
    execution command: 
    ```bash
    curp cal-tc --frame-range 1 10000 1 --average-shift 2 --dt 0.001 -a acf.nc -o $outdir/conductivity.dat flux_grp.nc > $outdir/conductivity.log
    ```

## Tested dataset

you can find and download the tested dataset in our lab's slack.
QA calculations were conducted with both of dataset which means 00_0 and 00_1.
```
00_0
00_1
```

## Result
Results are stored in [output/](output/)

1. Summary
    - The output computed under develop sourcecode was NOT the same as the one of v1.3.1.
    - The cause of this difference is assumed by comparing the outputs and the assumed casue is that numbers of rounding digits for floating point numbers are not the same.
    - Quick servey towards scripts that might affect the rouding digits (e.g. cal_tc.py, compute.py, lib_acf.f90, sum_acf.py and etc..) couldn't find out the change of its number.
    - Thus, the cause was assumed that the version differencies of the dependent packages of CURP beween v1.3.1 and ver. develop branch.

    the conductivity left: obtained under ver. develop branch, right: obtained under v1.3.1.  

    You can see the number of rouding digits was probably increased from v1.3.1 to ver. develop branch.
    ```bash
    00001_MET    00001_MET  1748.1905599270644                 |    00001_MET    00001_MET  1748.19055993
    00001_MET    00002_LEU  -52.8369212152526                  |    00001_MET    00002_LEU  -52.8369212153
    00001_MET    00003_SER  -0.8973068104263184                |    00001_MET    00003_SER  -0.897306810426
    00001_MET    00004_ASP  -0.608104630949924                 |    00001_MET    00004_ASP  -0.60810463095
    00001_MET    00005_GLU  -0.09694790319182034               |    00001_MET    00005_GLU  -0.0969479031918
    00001_MET    00006_ASP  2.364356941851162                  |    00001_MET    00006_ASP  2.36435694185
    00001_MET    00007_PHE  -0.010277902355916961              |    00001_MET    00007_PHE  -0.0102779023559
    00001_MET    00008_LYS  0.12261583015714167                |    00001_MET    00008_LYS  0.122615830157
    00001_MET    00009_ALA  0.0019590260726762426              |    00001_MET    00009_ALA  0.00195902607268
    00001_MET    00010_VAL  0.29585499019516365                |    00001_MET    00010_VAL  0.295854990195
    00001_MET    00011_PHE  0.017420287138677715               |    00001_MET    00011_PHE  0.0174202871387
    00001_MET    00012_GLY  -0.04073224302178635               |    00001_MET    00012_GLY  -0.0407322430218
    00001_MET    00013_MET  0.200844892293936                  |    00001_MET    00013_MET  0.200844892294
    00001_MET    00014_THR  -0.04193376474557947               |    00001_MET    00014_THR  -0.0419337647456
    00001_MET    00015_ARG  7.55975511133712                   |    00001_MET    00015_ARG  7.55975511134
    00001_MET    00016_SER  0.06290815212317666                |    00001_MET    00016_SER  0.0629081521232
    00001_MET    00017_ALA  -0.08188449224376373               |    00001_MET    00017_ALA  -0.0818844922438
    00001_MET    00018_PHE  0.08730237883677522                |    00001_MET    00018_PHE  0.0873023788368
    00001_MET    00019_ALA  0.02041714355565917                |    00001_MET    00019_ALA  0.0204171435557
    00001_MET    00020_ASN  0.5638339365252332                 |    00001_MET    00020_ASN  0.563833936525
    00001_MET    00021_LEU  0.0724327873285965                 |    00001_MET    00021_LEU  0.0724327873286
    00001_MET    00022_PRO  0.03741719757597049                |    00001_MET    00022_PRO  0.037417197576
    00001_MET    00023_LEU  0.09623680780380944                |    00001_MET    00023_LEU  0.0962368078038
    00001_MET    00024_TRP  0.20027289237638302                |    00001_MET    00024_TRP  0.200272892376
    00001_MET    00025_LYS  -2.9464382862747733                |    00001_MET    00025_LYS  -2.94643828627
    00001_MET    00026_GLN  0.19326317325633763                |    00001_MET    00026_GLN  0.193263173256
    00001_MET    00027_GLN  -0.9369979066456883                |    00001_MET    00027_GLN  -0.936997906646
    00001_MET    00028_ASN  0.01173348543601789                |    00001_MET    00028_ASN  0.011733485436
    00001_MET    00029_LEU  -0.12418199253102793               |    00001_MET    00029_LEU  -0.124181992531
    ```