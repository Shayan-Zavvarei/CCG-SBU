## Jupiter kernel list:
```bash
jupyter kernelspec list 
```
## Delete kernels in the jupyter:
```bash
jupyter kernelspec remove envname
```

## Delete Conda env from the base:
```bash
conda env remove -n envname
# or if you use the path, you can use:
conda env remove -p /path/to/env
```

## See the list of  kernels link to the conda list:
```bash
cat /home/jovyan/.local/share/jupyter/kernels/myenv/kernel.json
```

## Creating a virtual environment in jupyter using conda:
```bash
conda create --prefix /home/jovyan/conda-envs/thesis python=3.10 -y
```

## Activating the environment:
To activate the environment:
```bash
conda activate envname

# some times in jupyter servers with docker image installation method you need to active the environment like:

source activate /home/jovyan/conda-envs/thesis
```
or to deactivate the environment:
```bash 
conda deactivate envname
```

## Activating the kernel:
For activating the kernel, first you need to download and install the ipykernel:
```bash 
conda install -c conda-forge ipykernel -y
 ```
 then you can simply the environment as an active kernel for python:
 ```bash
python -m ipykernel install --user --name=thesis --display-name="Thesis (Persistent)"
 ```
 * After activating the kernel, you can simply install packages in that environment and each time you install a new package, it will add to an jason file and updates the kernel:
 ```bash
 {
 "argv": [
  "/home/jovyan/.conda/envs/thesis/bin/python",
  "-m",
  "ipykernel_launcher",
  "-f",
  "{connection_file}"
 ],
 "display_name": "Thesis (Python 3.10)",
 "language": "python"
}
```
*But be sure that you install the python package after activating the environment*

## Installing python packages in your environment:
You can install packages with both pip and conda:
For example using **pip**:
```bash
pip install \
    numpy scipy matplotlib seaborn pandas \
    numba sympy h5py PyYAML scikit-learn \
    astropy quantities uncertainties \
    Cython pybind11 tqdm jupyterlab ipywidgets \
    ipykernel
```
Similarly using **conda**:
```bash
conda install -c conda-forge -y \
    numpy scipy matplotlib seaborn pandas \
    numba sympy h5py pyyaml scikit-learn \
    astropy quantities uncertainties \
    cython pybind11 tqdm jupyterlab ipywidgets \
    ipykernel
```

## pip VS conda:
`pip`: Installs Python packages from PyPI. May compile from source → needs system compilers (often fails without `sudo`).
`conda` (especially `conda-forge`): Installs pre-built binaries with all C/Fortran dependencies included → no root needed, more reliable for scientific packages.

### Best Practice for Custom (GitHub) Packages with C Code:
1. Install compilers **inside your Conda env**:
```bash
conda install -c conda-forge compilers cxx-compiler
```
2. Then install your package with `pip`:
```bash 
pip install -e .
```

###  Installation of packages that I need:
```bash
conda install -c conda-forge -y \
    python=3.10 \
    numpy scipy numba matplotlib pandas h5py scikit-learn \
    sympy astropy \
    cython pybind11 \
    compilers cxx-compiler c-compiler fortran-compiler \
    ipykernel jupyterlab \
    tmux
```

for testing the successful installation, you can run this code in a cell of notebook:
```bash 
packages = [
    'numpy', 'scipy', 'numba', 'matplotlib', 'pandas', 'h5py',
    'sklearn', 'sympy', 'astropy', 'cython', 'pybind11'
]

import importlib
import sys

print("🔍 Testing package imports...\n")

for pkg in packages:
    try:
        mod = importlib.import_module(pkg)
        version = getattr(mod, '__version__', 'unknown')
        print(f"✅ {pkg:15} | version: {version}")
    except Exception as e:
        print(f"❌ {pkg:15} | ERROR: {e}")

try:
    import numpy as np
    a = np.random.random((1000, 1000))
    b = np.dot(a, a.T)  
    print("\n✅ Numerical test (BLAS/LAPACK) passed!")
except Exception as e:
    print(f"\n❌ Numerical test failed: {e}")

try:
    from numba import jit
    @jit
    def f(x):
        return x ** 2 + 1
    f(10)
    print("✅ Numba JIT test passed!")
except Exception as e:
    print(f"❌ Numba test failed: {e}")

print("\n🎉 If all are ✅, your environment is ready for computational physics!")
```

```bash
pip install \
    seaborn \
    PyYAML \
    tqdm \
    ipywidgets \
    quantities \
    uncertainties \
    getdist \
    corner \
    emcee
```
For testing the successful installation, you can run below code in a cell of notebook:
```bash
import getdist
import corner
import emcee
print("✅ Cosmology tools are ready!")
```
## Installation of Molino package:
1. Setting up the environment:
since my data directory is in:
```bash
 /home/jovyan/DataFiles/Molino/Data
 ```
 we we set below setting in ` ~/.bashrc`:
 ```bash
echo 'export HDF5_USE_FILE_LOCKING=FALSE' >> ~/.bashrc
echo 'export MOLINO_DIR="/home/jovyan/DataFiles/Molino/Data" '>> ~/.bashrc
```
then run the
```bash
source ~/.bashrc
```
This allows the package to find where the downloaded molino data is located.

* To test that you set the environment variables correct, you can check:
```bash
echo $HDF5_USE_FILE_LOCKING
echo $MOLINO_DIR
```
the result should be:
```bash
FALSE
/home/jovyan/DataFiles/Molino/Data
```
### Installing the Molino from its git repo:
```bash
# activate your virtual environment
source activate thesis

# clone the repo
git clone https://github.com/changhoonhahn/molino.git

# go to repo directory
cd molino

# install the package
pip install -e .
```
For testing that you install molino correctly, run below test in a cell of your notebook:
```bash
import os
os.environ['MOLINO_DIR'] = "/home/jovyan/DataFiles/Molino/Data"

import numpy as np
from molino import GalaxyCatalog
# Limit to available files
nbodys = [0]
hods = [1]
# Load Mnu_p catalog
catalog_mnup = GalaxyCatalog(
    'Mnu_p',
    i_nbody=nbodys,
    i_hod=hods,
    apply_rsd=False,
    columns=['x', 'y', 'z', 'x_halo', 'y_halo', 'z_halo', 'gal_type', 'm_halo']
)
print(f"Length of catalog_mnup: {len(catalog_mnup)}")
print(f"Shape of catalog_mnup: {np.array(catalog_mnup).shape}")
```
the result should be:
```bash
Length of catalog_mnup: 162863
Shape of catalog_mnup: (162863, 8)
```
**Now for Installing other packages, you need to go to home directory, so:
```bash 
cd
# or 
cd ..
```

## Installing the Pylians package:

### 1. Activate Your Persistent Environment
```bash
source activate /home/jovyan/conda-envs/thesis
```
*This ensures all packages are installed in your durable environment.*
### 2. Install Required Dependencies (via Conda)
Pylians3 relies on compiled code (C/Cython) and needs OpenMP support. Install everything from `conda-forge`:
```bash
conda install -c conda-forge -y \
    numpy scipy matplotlib h5py cython \
    compilers cxx-compiler c-compiler fortran-compiler \
    libgomp openmp
```
### 3. Set Compilation & Runtime Environment Variables
These tell the build system where to find compilers and libraries:
```bash
export CC=$(which gcc)
export CXX=$(which g++)
export LDFLAGS="-fopenmp -L/home/jovyan/conda-envs/thesis/lib"
export CPPFLAGS="-I/home/jovyan/conda-envs/thesis/include"
export LD_LIBRARY_PATH="/home/jovyan/conda-envs/thesis/lib:$LD_LIBRARY_PATH"
```
**Optional: Add these to ~/.bashrc to make them permanent:**
```bash
echo 'export LD_LIBRARY_PATH="/home/jovyan/conda-envs/thesis/lib:$LD_LIBRARY_PATH"' >> ~/.bashrc
```
### 4. Clone and Install Pylians3
```bash
cd /home/jovyan
git clone https://github.com/franciscovillaescusa/Pylians3.git
cd Pylians3
python -m pip install --no-cache-dir --force-reinstall .
```
* `--no-cache-dir` ensures a fresh build.
* `--force-reinstall` avoids conflicts with partial installs.
### 5. Verify the Installation
Run the official test script:
```bash
python Tests/import_libraries.py
```
**Success:** No output (silent = good!)
You can also test manually in a notebook:
```bash
import MAS_library
import Pk_library
print("✅ Pylians3 is ready for cosmological analysis!")
```

## Bash script of above steps:
```bash 
#!/bin/bash

# Set environment variables for Molino
echo 'export HDF5_USE_FILE_LOCKING=FALSE' >> ~/.bashrc
echo 'export MOLINO_DIR="/home/jovyan/DataFiles/Molino/Data"' >> ~/.bashrc
source ~/.bashrc

# Create Conda environment
conda create --prefix /home/jovyan/conda-envs/thesis python=3.10 -y

# Activate environment
source activate /home/jovyan/conda-envs/thesis

# Install core packages via conda
conda install -c conda-forge -y \
    numpy scipy numba matplotlib pandas h5py scikit-learn \
    sympy astropy cython pybind11 \
    compilers cxx-compiler c-compiler fortran-compiler \
    ipykernel jupyterlab tmux

# Install additional packages via pip
pip install \
    seaborn PyYAML tqdm ipywidgets quantities uncertainties \
    getdist corner emcee

# Install Molino
cd /home/jovyan
git clone https://github.com/changhoonhahn/molino.git
cd molino
pip install -e .

# Install Pylians3 dependencies
conda install -c conda-forge -y \
    libgomp openmp

# Set build environment variables
export CC=$(which gcc)
export CXX=$(which g++)
export LDFLAGS="-fopenmp -L/home/jovyan/conda-envs/thesis/lib"
export CPPFLAGS="-I/home/jovyan/conda-envs/thesis/include"
export LD_LIBRARY_PATH="/home/jovyan/conda-envs/thesis/lib:$LD_LIBRARY_PATH"
echo 'export LD_LIBRARY_PATH="/home/jovyan/conda-envs/thesis/lib:$LD_LIBRARY_PATH"' >> ~/.bashrc

# Install Pylians3
cd /home/jovyan
git clone https://github.com/franciscovillaescusa/Pylians3.git
cd Pylians3
python -m pip install --no-cache-dir --force-reinstall .

# Install Jupyter kernel
python -m ipykernel install --user --name=thesis --display-name="Thesis (Persistent)"
```
### 1. For example, create a file named setup_thesis_env.sh in your home directory:
```bash
cd
nano setup_thesis_env.sh
```
Then paste the entire Bash script content (the one provided earlier) into this file and save it (`Ctrl+O` → Enter → `Ctrl+X` in nano).

### 2. Make the script executable
```bash 
chmod +x setup_thesis_env.sh
```
### 3. Run the script
```bash
./setup_thesis_env.sh
```
**Note:** This script runs directly in the terminal and will sequentially execute all commands (creating the Conda environment, installing packages, cloning repositories, etc.).
Depending on your network speed and system performance, execution may take several minutes.