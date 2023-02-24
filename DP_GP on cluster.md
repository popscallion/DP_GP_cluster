# DP_GP on cluster
## SSH into cluster
```
ssh [username]@login.rc.fas.harvard.edu
[password]
[2-factor]
```
## Load Anaconda and create a virtual environment named e.g. DP_GP so you can load custom modules
```
module load Anaconda/5.0.1-fasrc02
pip install GPy
#error: trying to install to read-only python dir
conda create --name DP_GP python=2.7
```
## Activate the new virtual environment and install required dependencies
Once the virtual environment is active (indicated by its name in parentheses in front of the terminal cursor) all python-related stuff happens in a bubble and doesn't affect the rest of the system.

DP_GP uses C code and requires Cython. GPy needs decorator at or higher than 4.0.10, as well as paramz==0.7.4(which needs to be fetched from the conda-forge channel instead of the regular one.)
```
source activate DP_GP
conda install matplotlib numpy pandas scikit-learn cython decorator==4.0.10
conda install -c conda-forge paramz==0.7.4
```
The latest GPy version needs scipy<1.5.0,>=1.3.0 but scipy 1.3.0 and above are incompatible with Python 2.7.

We need GPy v1.0.6 from 2016, which is the last time DP_GP was updated. This requires us to install it from the source code on Github instead of from conda or pip. 



## Install GPy 1.0.6 from source code
wget downloads the gzipped source code from Github, and the tar -xz option unzips it into your user folder.
```
cd /n/holylabs/LABS/hanken_lab/Users/jkaustiff
wget https://github.com/SheffieldML/GPy/archive/refs/tags/v1.0.6.tar.gz  -O - | tar -xz
cd GPy-1.0.6
python setup.py install

```
## Install DP_GP from source code
```
cd ..
wget https://github.com/anncfs/DP_GP_cluster/archive/refs/heads/master.tar.gz  -O - | tar -xz
cd DP_GP_cluster-master
```
## Verify that everything is installed correctly
DP_GP_cluster should show up here:
```
conda list
```
## Run test
If you're running a non-GUI session, you'll need to first run `echo "backend: Agg" > ~/.config/matplotlib/matplotlibrc` or you'll get a `_tkinter.TclError: no display name and no $DISPLAY environment variable` error from matplotlib.
```
cd DP_GP
DP_GP_cluster.py -i test/test.txt -o test/test -p png -n 20 --plot
```
This should spit out a bunch of new files in the test directory.
```
<!-- convert xenopus file to log10, replace -inf with 0s, rename column 1 to gene -->
scp ./test/xen_cluster_no_log_nans.txt jkaustiff@login.rc.fas.harvard.edu:/n/holylabs/LABS/hanken_lab/Users/jkaustiff/DP_GP_cluster-master/xen_cluster_no_log_nans.txt
DP_GP_cluster.py -i  /n/holylabs/LABS/hanken_lab/Users/jkaustiff/DP_GP_cluster-master/xen_cluster_no_log_nans.txt -o /n/holylabs/LABS/hanken_lab/Users/jkaustiff/DP_GP_cluster-master/xen_cluster_output -p png -n 20 --plot --criterion MAP
```