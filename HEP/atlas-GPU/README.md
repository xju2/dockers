# Find CUDA Home
Find CUDA and TBB.
```bash
export CUDA_HOME=/cvmfs/atlas.cern.ch/repo/sw/software/22.0/sw/lcg/releases/cuda/11.4-166ec/x86_64-centos7-gcc11-opt/
export CUDNN_HOME=/cvmfs/atlas.cern.ch/repo/sw/software/22.0/sw/lcg/releases/cudnn/8.2.4.15-ca3b5/x86_64-centos7-gcc11-opt/
export TBB_HOME=/cvmfs/atlas.cern.ch/repo/sw/software/22.0/sw/lcg/releases/LCG_101_ATLAS_14/tbb/2020_U2/x86_64-centos7-gcc11-opt/lib/cmake
```

# Install Pytorch
* Setup athena
```
asetup Athena,22.0.59,here
```

* Install dependence:
```bash
pip install --user typing_extensions
```

* Compile and install
```bash
MAX_JOBS=6 USE_CUDA=1 USE_OPENMP=1 USE_MPI=0 USE_TBB=1 USE_SYSTEM_TBB=1 \
USE_MKLDNN=1 MKL_THREADING=TBB MKLDNN_CPU_RUNTIME=TBB BUILD_BINARY=0 \
BUILD_CUSTOM_PROTOBUF=ON CMAKE_PREFIX_PATH=$TBB_HOME \
python setup.py install --cmake --user
```