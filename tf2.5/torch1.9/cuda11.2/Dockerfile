# docexoty/exatrkx:tf2.5-torch1.9-cuda11.2-ubuntu20.04-rapids21.10-py3.8-devel
ARG RAPIDS_VERSION=21.10-cuda11.2-devel-ubuntu20.04-py3.8
FROM rapidsai/rapidsai-dev:${RAPIDS_VERSION}

ARG CMAKE_VERSION=3.21.1
ARG TF_VERSION=2.5

RUN . /opt/conda/etc/profile.d/conda.sh && conda activate rapids \
    && conda install -c conda-forge mamba 

RUN . /opt/conda/etc/profile.d/conda.sh && conda activate rapids \
    && mamba install -y swig cudnn xtensor pybind11 

RUN . /opt/conda/etc/profile.d/conda.sh && conda activate rapids \
    && pip install torch==1.9.0+cu111 torchvision==0.10.0+cu111 torchaudio===0.9.0 -f https://download.pytorch.org/whl/torch_stable.html \
    && pip install torch-scatter -f https://pytorch-geometric.com/whl/torch-1.9.0+cu111.html \
    && pip install torch-sparse  -f https://pytorch-geometric.com/whl/torch-1.9.0+cu111.html \
    && pip install torch-cluster -f https://pytorch-geometric.com/whl/torch-1.9.0+cu111.html \
    && pip install torch-spline-conv -f https://pytorch-geometric.com/whl/torch-1.9.0+cu111.html \
    && pip install torch-geometric \
    && pip install tensorflow_gpu==${TF_VERSION}

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

# nsight-compute-2021.1.0
# https://developer.nvidia.com/nsight-compute-history
# https://developer.nvidia.com/blog/using-nsight-compute-in-containers/
RUN  echo "deb https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64 /" > /etc/apt/sources.list.d/cuda.list && \
     wget -qO - https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub | apt-key add - && \
         apt-get update -y && \
     DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
         nsight-compute-2021.1 && \
     rm -rf /var/lib/apt/lists/*

# install other inputs for exatrkx
RUN . /opt/conda/etc/profile.d/conda.sh && conda activate rapids \
    && pip install pytorch-lightning \
    git+https://github.com/LAL/trackml-library.git \
    git+https://github.com/deepmind/graph_nets.git \
    matplotlib sklearn pyyaml>=5.1 tqdm networkx 

RUN . /opt/conda/etc/profile.d/conda.sh && conda activate rapids \
    && cd /rapids && git clone https://github.com/xju2/prefix_sum.git \
    && git clone https://github.com/murnanedaniel/FRNN.git && cd prefix_sum \
    && NVCC_FLAGS="-gencode=arch=compute_70,code=sm_70 -gencode=arch=compute_80,code=sm_80" python setup.py install \
    && pip install . && cd /rapids/FRNN \
    && NVCC_FLAGS="-gencode=arch=compute_70,code=sm_70 -gencode=arch=compute_80,code=sm_80" python setup.py install \
    && pip install . 

# Install cmake
RUN cd /tmp && . /opt/conda/etc/profile.d/conda.sh && conda activate rapids \
    && wget https://github.com/Kitware/CMake/releases/download/v${CMAKE_VERSION}/cmake-${CMAKE_VERSION}-linux-x86_64.sh \
    && bash cmake-${CMAKE_VERSION}-linux-x86_64.sh --prefix=/opt/conda/envs/rapids --exclude-subdir --skip-license \
	&& rm -rf /tmp/*


RUN cd /tmp && \
    git clone --recursive --branch v1.9.1 https://github.com/Microsoft/onnxruntime && \
    cd onnxruntime


# RUN cd /tmp && \
#     git clone --recursive --branch v1.9.1 https://github.com/Microsoft/onnxruntime && \
#     cd onnxruntime && \
#     ./build.sh \
#         --cuda_home /usr/local/cuda \
#         --cudnn_home /usr/lib/x86_64-linux-gnu/ \
#         --use_cuda \
#         # --use_tensorrt \
#         # --tensorrt_home /usr/lib/x86_64-linux-gnu/ \
#         --config RelWithDebInfo \
#         --build_shared_lib \
#         --build_wheel \
#         --skip_tests \
#         --parallel ${NUM_JOBS} && \
#     cd build/Linux/RelWithDebInfo && \
#     make install && \
#     pip install --user dist/* && rm -rf /tmp/* 


# # Add pre-compiled libtorch
# RUN cd /rapids \
#     && wget https://download.pytorch.org/libtorch/cu111/libtorch-shared-with-deps-1.9.0%2Bcu111.zip \
#     && unzip libtorch-shared-with-deps-1.9.0+cu111.zip && rm libtorch-shared-with-deps-1.9.0+cu111.zip

# add pre-compiled onnx-runtime
#RUN cd /rapids && mkdir onnxSrc && cd onnxSrc \
#    && wget -O onnx_archive.nupkg https://www.nuget.org/api/v2/package/Microsoft.ML.OnnxRuntime/1.8.0 \
#    && unzip onnx_archive.nupkg && rm onnx_archive.nupkg \
#    && mkdir -p /rapids/onnx/lib /rapids/onnx/include/onnxruntime \
#    && cp runtimes/linux-x64/native/libonnxruntime.so /rapids/onnx/lib \
#    && cp -r build/native/include /rapids/onnx/include/onnxruntime \
#    && chmod -R a+rX /rapids/onnx && cd /rapids && rm -rf onnxSrc
#
## RUN . /opt/conda/etc/profile.d/conda.sh && conda activate rapids \
##     && mamba install -y -c conda-forge mpi4py nvcc_linux-64=11.2 cxx-compiler 
#    
#
#RUN . /opt/conda/etc/profile.d/conda.sh && conda activate rapids &&  HOROVOD_WITH_TENSORFLOW=1 HOROVOD_WITH_PYTORCH=1 \
#    HOROVOD_BUILD_CUDA_CC_LIST=70,75,80 HOROVOD_GPU_OPERATIONS=NCCL HOROVOD_NCCL_LINK=SHARED \
#    pip install --no-cache-dir --user git+https://github.com/horovod/horovod.git@v0.21.3
#
#RUN cd /tmp/ && wget https://portal.nersc.gov/project/atlas/xju/onnx.tar.gz \
#    && tar xzf onnx.tar.gz && mkdir /usr/local/onnxruntime/ && mv include lib /usr/local/onnxruntime \
#    && rm -rf * 
