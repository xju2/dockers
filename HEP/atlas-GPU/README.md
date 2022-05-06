# Table of Contents
- [Introduction](#Introduction)
- [Daily Setup](#daily-setup)
- [Setup global variables](#setup-global-variables)
    - [Install Pytorch](#install-pytorch)
    - [Install system dependences](#install-system-dependences)
    - [Install onnxruntime](#install-onnxruntime)
    - [Install kineto](#install-kineto)
    - [Install FRNN](#install-frnn)
- [Compile Athena](#compile-athena)
- Examples
    - [Onnxruntime Example](#onnxruntime-example)
    - [ExaTrkX Example](#exatrkx-example)

# Introduction
Image Name: `docexoty/centos7-atlasos-gpu`. 
* Base Image: `nvidia/cuda:11.4.2-devel-centos7`
* OS: `CentOS7`

# Daily Setup
Need to setup the following everytime the container starts.
I hasitate to put it into the container as it may not needed when all libraries can be found in LCG.
```bash
export LD_LIBRARY_PATH=/opt/onnxruntime/lib64:/home/atlas/python3.9/lib/python3.9/site-packages/torch/lib:$LD_LIBRARY_PATH
```

# Setup global variables
Find CUDA and TBB and ZLIB
```bash
export CUDA_HOME=/cvmfs/atlas.cern.ch/repo/sw/software/22.0/sw/lcg/releases/cuda/11.4-166ec/x86_64-centos7-gcc11-opt/
export CUDNN_HOME=/cvmfs/atlas.cern.ch/repo/sw/software/22.0/sw/lcg/releases/cudnn/8.2.4.15-ca3b5/x86_64-centos7-gcc11-opt/
export TBB_HOME=/cvmfs/atlas.cern.ch/repo/sw/software/22.0/sw/lcg/releases/LCG_101_ATLAS_14/tbb/2020_U2/x86_64-centos7-gcc11-opt/lib/cmake
export ZLIB_HOME=/cvmfs/atlas.cern.ch/repo/sw/software/22.0/sw/lcg/releases/LCG_101_ATLAS_14/zlib/1.2.11/x86_64-centos7-gcc11-opt
export RANGEV3_HOME=/cvmfs/atlas.cern.ch/repo/sw/software/22.0/sw/lcg/releases/LCG_101_ATLAS_14/rangev3/0.11.0/x86_64-centos7-gcc11-opt
export fmt_HOME=/cvmfs/sft.cern.ch/lcg/views/LCG_101_ATLAS_14/x86_64-centos7-gcc11-opt/lib64/cmake/fmt
export Boost_HOME=/cvmfs/sft.cern.ch/lcg/views/LCG_101_ATLAS_14/x86_64-centos7-gcc11-opt
export LD_LIBRARY_PATH=/opt/onnxruntime/lib64:/home/atlas/python3.9/lib/python3.9/site-packages/torch/lib:$LD_LIBRARY_PATH
```

# Install Pytorch
* Setup athena
```
asetup Athena,22.0.59,here
```

* Install dependence:
```bash
pip install --user typing_extensions  pybind11
```

* Downlaod source code and Compile and install
```bash
MAX_JOBS=6 USE_CUDA=1 USE_OPENMP=1 USE_MPI=0 USE_TBB=1 USE_SYSTEM_TBB=1 \
USE_MKLDNN=1 MKL_THREADING=TBB MKLDNN_CPU_RUNTIME=TBB BUILD_BINARY=0 \
BUILD_CUSTOM_PROTOBUF=ON CMAKE_PREFIX_PATH=$TBB_HOME \
python setup.py install --cmake --user
```

# Install system dependences
```bash
sudo yum install -y libEGL libGL
```
then add a few soft links
```bash
cd /lib64 && sudo ln -s libEGL.so.1.1.0 libEGL.so && sudo ln -s libEGL_mesa.so.0.0.0 libEGL_mesa.so.0 \
&& sudo ln -s libGL.so.1.7.0 libGL.so
```

# Install onnxruntime
In case the cuda provider is not in athena, one can compile onnxruntime. 

* Checkout the repository
```bash
git clone https://github.com/Microsoft/onnxruntime && cd onnxruntime && \
git checkout tags/1.11.0 -b v1.11.0
```

* Compile
    * Paths to `gcc` and `g++` are hardcoded.
    * Add ` --skip_submodule_sync` in case the repository is already up to date.

```bash
./build.sh --cuda_home $CUDA_HOME \
        --cudnn_home $CUDNN_HOME \
        --use_cuda \
        --config Release \
        --build_wheel \
        --build_shared_lib \
        --skip_tests --skip_onnx_tests \
        --cmake_extra_defines 'CMAKE_CUDA_ARCHITECTURES=60;70;75;80' \
            'CMAKE_INSTALL_PREFIX=/opt/onnxruntime' \
            'BUILD_ONNX_PYTHON=ON' \
            'CMAKE_C_COMPILER=/cvmfs/sft.cern.ch/lcg/releases/gcc/11.2.0-ad950/x86_64-centos7/bin/gcc' \
            'CMAKE_CXX_COMPILER=/cvmfs/sft.cern.ch/lcg/releases/gcc/11.2.0-ad950/x86_64-centos7/bin/g++' \
        --parallel 6 --update --build --skip_submodule_sync
```

* Install
```bash
cd build/Linux/Release && sudo make install
```

Optional to install the python packages `pip install dist/*`.

* Configure
```bash
export LD_LIBRARY_PATH=/opt/onnxruntime/lib64:/home/atlas/python3.9/lib/python3.9/site-packages/torch/lib:$LD_LIBRARY_PATH
```

# Install kineto

```bash
git clone --recursive https://github.com/pytorch/kineto.git && \
    cd kineto/libkineto &&  mkdir build && cd build && cmake .. \
        -DCMAKE_INSTALL_PREFIX=/home/atlas/python3.9 && \
    make -j4 && make install
```

# Install FRNN

```bash
git clone https://github.com/xju2/libFRNN.git && cd libFRNN && \
cmake .. -DCMAKE_PREFIX_PATH=$(python -c 'import torch;print(torch.utils.cmake_prefix_path)') \
  -DCMAKE_INSTALL_PREFIX=/home/atlas/python3.9 && \
make -j4 && make install 
```

# Compile Athena

In the working directory, create a new file `package_filters.txt` with following content.
```text
+ Control/AthOnnxruntimeUtils
+ Control/AthenaExamples/AthExOnnxRuntime
+ InnerDetector/InDetRecTools/InDetRecToolInterfaces
+ InnerDetector/InDetRecAlgs/SiSPGNNTrackFinder
+ InnerDetector/InDetRecTools/SiGNNTrackFinderTool
- .*
```
Then `mkdir build && cd build `.

```bash
cmake -DCMAKE_MODULE_PATH=/home/atlas/projects/tracking/integrateToAthena/cmake \
   -DCMAKE_PREFIX_PATH=$(python -c 'import torch;print(torch.utils.cmake_prefix_path)') \
   -DATLAS_PACKAGE_FILTER_FILE=../package_filters.txt \
   -Dcugraph_DIR=/home/atlas/python3.9 \
   -DZLIB_INCLUDE_DIR=${ZLIB_HOME}/include -DZLIB_LIBRARY=${ZLIB_HOME}/lib \
   -DRANGEV3_INCLUDE_DIR=${RANGEV3_HOME}/include -DRANGEV3_LCGROOT=${RANGEV3_HOME} \
   -Dfmt_DIR=${fmt_HOME} \
   -DBoost_INCLUDE_DIR=${Boost_HOME}/include \
   -DCUDNN_INCLUDE_PATH=${CUDNN_HOME}/include -DCUDNN_LIBRARY_PATH=${CUDNN_HOME}/lib64 \
   ~/code/athena/Projects/WorkDir
```

# onnxruntime example
```bash
athena x86_64-centos7-gcc11-opt/jobOptions/AthExOnnxRuntime/AthExOnnxRuntime_jobOptions.py
```

# Exa.TrkX example
Don't forgot: `source x86_64-centos7-gcc11-opt/setup.sh`.
```bash
athena x86_64-centos7-gcc11-opt/jobOptions/SiSPGNNTrackFinder/SiSPSeededGNNTracksStandaloneFromESD.py
```