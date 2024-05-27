# Geocruncher

Geocruncher is a dependency of Visual Karsys develop by ISSKA to bridge Visual Karsys with gmlib, develop by the BRGM.

# Prebuilt libraries

For our use case, we use pre built binaries of the following geocruncher dependencies:

| Depencency | Version used |
|------------|--------------|
| gmlib      | 0.3.17       |
| MeshTools  | main@5a02671 |
| pycgal     | 0.3.14       |
| vtkwriters | 0.0.10       |

The documentation below explains how to generate each prebuilt binary. This is needed when upgrading dependencies, or when you need to compile them for a different platform.
For each, the resultintg `.whl` file will be created in a `dist` subfolder. You can copy that file to `VISKAR/src/dist/`, and delete the old one.

I am using Anaconda in this documentation to easily setup virtual python environments.

https://docs.conda.io/projects/conda/en/latest/user-guide/install/linux.html

```bash
# Choose here the python version that corresponds to the production environment
# Look for PYTHON_VERSION in the Dockerfiles
conda create -n viskar39 python=3.9
conda activate viskar39
```

## gmlib

The source code is available here. To gain access, see **BRGM Gitlab** in [Workflow](../operation%20&%20controls/workflow.md) 

https://gitlab.brgm.fr/brgm/geomodelling/internal/gmlib

```bash
sudo apt install cmake libeigen3-dev
pip install scikit-build
git clone https://gitlab.brgm.fr/brgm/geomodelling/internal/gmlib
cd gmlib
# Check out specific version you want to build
git checkout v0.3.17
python setup.py bdist_wheel
```

## MeshTools

The source code is available here. To gain access, see **BRGM Gitlab** in [Workflow](../operation%20&%20controls/workflow.md) 

https://gitlab.brgm.fr/brgm/modelisation-geologique/meshtools

```bash
sudo apt install cmake
pip install scikit-build numpy
git clone https://gitlab.brgm.fr/brgm/modelisation-geologique/meshtools
cd meshtools
# Check out specific version you want to build
git checkout v0.0.8
python setup.py bdist_wheel
```

This package seems to build with version number 0.0.0. Manually correct the .whl file name.
The version number inside is likely irrelevant to us. We just need the Docker build to detect the new version for cache busting.

## pycgal

The source code is available here. To gain access, see **BRGM Gitlab** in [Workflow](../operation%20&%20controls/workflow.md)

https://gitlab.brgm.fr/brgm/geomodelling/internal/pycgal

```bash
sudo apt install cmake libeigen3-dev libcgal-dev libboost-all-dev libgmp-dev libmpfr-dev
pip install scikit-build
git clone https://gitlab.brgm.fr/brgm/geomodelling/internal/pycgal
cd pycgal
# Check out specific version you want to build
git checkout v0.3.14
python setup.py bdist_wheel
```

This one is particularly slow to compile...

## vtkwriters

The source code is available here.

https://github.com/BRGM/vtkwriters

```bash
sudo apt install cmake
pip install scikit-build
git clone https://github.com/BRGM/vtkwriters
cd vtkwriters
# Check out specific version you want to build
git checkout v0.0.10
python setup.py bdist_wheel
```
