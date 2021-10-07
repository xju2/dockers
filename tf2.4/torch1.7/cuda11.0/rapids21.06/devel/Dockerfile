# this is for docexoty/exatrkx:tf2.4-torch1.7-cuda11.0-ubuntu20.04-rapids21.06-py3.8-devel
# Horovod not working!
FROM rapidsai/rapidsai-dev:21.06-cuda11.0-devel-ubuntu20.04-py3.8

RUN . /opt/conda/etc/profile.d/conda.sh && conda activate rapids \
    && conda install -c conda-forge swig cudnn


RUN . /opt/conda/etc/profile.d/conda.sh && conda activate rapids \
    && pip install torch==1.7.1+cu110 -f https://download.pytorch.org/whl/torch_stable.html \
    && pip install torch-scatter -f https://pytorch-geometric.com/whl/torch-1.7.0+cu110.html \
    && pip install --no-cache-dir torch-sparse==0.6.9  -f https://pytorch-geometric.com/whl/torch-1.7.0+cu110.html \
    && pip install torch-cluster -f https://pytorch-geometric.com/whl/torch-1.7.0+cu110.html \
    && pip install torch-spline-conv -f https://pytorch-geometric.com/whl/torch-1.7.0+cu110.html \
    && pip install torch-geometric \
    && pip install tensorflow==2.4 tensorboard==2.4


RUN . /opt/conda/etc/profile.d/conda.sh && conda activate rapids \
    && cd /rapids && git clone https://github.com/facebookresearch/faiss.git \
    && cd faiss && mkdir build && cd build \
    && cmake .. -DFAISS_ENABLE_GPU=ON -DFAISS_ENABLE_PYTHON=ON \
        -DCMAKE_CUDA_ARCHITECTURES="60-real;70-real;75-real;80" \
        -DPython_EXECUTABLE=`which python`\ 
        -DCMAKE_INSTALL_PREFIX=`/opt/conda/envs/rapids` \
    && make -j faiss  && make -j swigfaiss \
    && cd faiss/python && pip install . \
    && cd /rapids/faiss/build && make install 

# NV-system for profiling
# https://docs.nvidia.com/nsight-systems/InstallationGuide/index.html#cuda-support
RUN apt-get update -y && \
     DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
         apt-transport-https \
         ca-certificates \
         gnupg \
         wget && \
     rm -rf /var/lib/apt/lists/*
RUN wget -qO - https://developer.download.nvidia.com/devtools/repos/ubuntu2004/amd64/nvidia.pub | apt-key add - && \
     echo "deb https://developer.download.nvidia.com/devtools/repos/ubuntu2004/amd64/ /" >> /etc/apt/sources.list.d/nsight.list && \
     apt-get update -y && \
     DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
         nsight-systems-2021.2  && \
    rm -rf /var/lib/apt/lists/*

# nsight-compute-2020.1.0
# https://developer.nvidia.com/nsight-compute-history
# https://developer.nvidia.com/blog/using-nsight-compute-in-containers/
RUN  echo "deb https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64 /" > /etc/apt/sources.list.d/cuda.list && \
     wget -qO - https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub | apt-key add - && \
         apt-get update -y && \
     DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
         nsight-compute-2021.1 && \
     rm -rf /var/lib/apt/lists/*

RUN . /opt/conda/etc/profile.d/conda.sh && conda activate rapids \
    && cd /rapids && git clone https://github.com/xju2/prefix_sum.git \
    && git clone https://github.com/murnanedaniel/FRNN.git && cd prefix_sum \
    && NVCC_FLAGS="-gencode=arch=compute_70,code=sm_70 -gencode=arch=compute_80,code=sm_80" python setup.py install \
    && pip install . && cd /rapids/FRNN \
    && NVCC_FLAGS="-gencode=arch=compute_70,code=sm_70 -gencode=arch=compute_80,code=sm_80" python setup.py install \
    && pip install . 

RUN . /opt/conda/etc/profile.d/conda.sh && conda activate rapids \
    && conda install -c conda-forge xtensor 


# RUN . /opt/conda/etc/profile.d/conda.sh && conda activate rapids \
#     && pip install mxnet-cu110 \
#     &&  HOROVOD_WITH_TENSORFLOW=1 HOROVOD_WITH_PYTORCH=1 HOROVOD_WITH_MPI=1 HOROVOD_WITH_GLOO=0\
#     HOROVOD_BUILD_CUDA_CC_LIST=70,75,80 HOROVOD_GPU_OPERATIONS=NCCL HOROVOD_NCCL_LINK=SHARED \
#     pip install --no-cache-dir --user git+https://github.com/horovod/horovod.git@v0.22.1

RUN cd /rapids && git clone --recursive --branch v1.8.1 https://github.com/Microsoft/onnxruntime 

# because onnx runtime looking for cudnn in cudnn_DIR/lib64
RUN ln -s /opt/conda/envs/rapids/lib /opt/conda/envs/rapids/lib64

RUN . /opt/conda/etc/profile.d/conda.sh && conda activate rapids \
    && conda install -c conda-forge pybind11 mamba \
    # && cd /rapids && git clone --recursive --branch v1.8.0 https://github.com/Microsoft/onnxruntime \
    && cd /rapids/onnxruntime && git clean -fdx\
    && ./build.sh --update \
        --cuda_home /usr/local/cuda \
        --cudnn_home /opt/conda/envs/rapids \
        --use_cuda  --config Release \
        --build_shared_lib --build_wheel --skip_tests --parallel 0 \
        --mpi_home /usr/lib/x86_64-linux-gnu/openmpi \
        --nccl_home /opt/conda/envs/rapids \
        --cmake_extra_defines CMAKE_INSTALL_PREFIX=/opt/conda/envs/rapids \
        CMAKE_CUDA_ARCHITECTURES="60-real;70-real;75-real;80" BUILD_ONNX_PYTHON=ON \
        --enable_pybind  \
    && cd build/Linux/Release && make install && pip install dist/* \
    && cd /rapids && rm -rf onnxruntime