# docexoty/exatrkx:tf2.7-torch1.10-cuda11.4-rapids21.10-devel
ARG RAPIDS_VERSION=21.10-cuda11.4-devel-ubuntu20.04-py3.8
FROM rapidsai/rapidsai-dev:${RAPIDS_VERSION}

ARG CMAKE_VERSION=3.21.1
ARG TF_VERSION=2.7

RUN source activate rapids && conda install -c conda-forge mamba \
    && mamba install -y swig cudnn=8.2.1 xtensor pybind11 

# pytorch
RUN source activate rapids \
    && pip install torch==1.10.0+cu113 torchvision==0.11.1+cu113 \
        torchaudio==0.10.0+cu113 -f https://download.pytorch.org/whl/cu113/torch_stable.html

# tensorflow
RUN source activate rapids && pip install tensorflow_gpu==${TF_VERSION}

# Install cmake
RUN cd /tmp && source activate rapids \
    && wget https://github.com/Kitware/CMake/releases/download/v${CMAKE_VERSION}/cmake-${CMAKE_VERSION}-linux-x86_64.sh \
    && bash cmake-${CMAKE_VERSION}-linux-x86_64.sh --prefix=/opt/conda/envs/rapids --exclude-subdir --skip-license \
	&& rm -rf /tmp/*


ARG PYG_WHL=https://data.pyg.org/whl/torch-1.10.0+cu113.html
# pyG
RUN source activate rapids && \
    pip install --user torch-scatter -f ${PYG_WHL} \
	&& pip install --user torch-sparse  -f ${PYG_WHL} \
	&& pip install --user torch-geometric

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
RUN source activate rapids \
    && pip install pytorch-lightning \
    git+https://github.com/LAL/trackml-library.git \
    git+https://github.com/deepmind/graph_nets.git \
    matplotlib sklearn pyyaml>=5.1 tqdm networkx 



RUN source activate rapids \
    && pip install wandb tables \
    && cd /rapids && git clone https://github.com/facebookresearch/faiss.git \
    && cd faiss && mkdir build && cd build \
    && cmake .. -DFAISS_ENABLE_GPU=ON -DFAISS_ENABLE_PYTHON=ON \
        -DCMAKE_CUDA_ARCHITECTURES="60-real;70-real;75-real;80" \
        -DPython_EXECUTABLE=${CONDA_PREFIX}/bin/python \
        -DCMAKE_INSTALL_PREFIX=${CONDA_PREFIX} \
    && make -j faiss  && make -j swigfaiss \
    && cd faiss/python && pip install . \
    && cd /rapids/faiss/build && make install

ENV export LD_PRELOAD=/opt/conda/envs/rapids/lib/libmkl_def.so:/opt/conda/envs/rapids/lib/libmkl_avx2.so:/opt/conda/envs/rapids/lib/libmkl_core.so:/opt/conda/envs/rapids/lib/libmkl_intel_lp64.so:/opt/conda/envs/rapids/lib/libmkl_intel_thread.so:/opt/conda/envs/rapids/lib/libiomp5.so

RUN cd /rapids && \
    git clone --recursive --branch v1.9.1 https://github.com/Microsoft/onnxruntime \
    && cd onnxruntime \
    && source activate rapids \
    && for FILE in /opt/conda/envs/rapids/include/cudnn*; \
        do ln -s $FILE /usr/include/$(basename $FILE); done \
    && for FILE in /opt/conda/envs/rapids/lib/libcudnn*.so; \
        do ln -s $FILE /usr/lib/x86_64-linux-gnu/$(basename $FILE); done \
    && ./build.sh --skip_submodule_sync \
        --cuda_home /opt/conda/envs/rapids \
        --cudnn_home /usr/lib/x86_64-linux-gnu/ \
        --use_cuda \
        --config Release \
        --build_wheel \
		--build_shared_lib \
		--skip_tests --skip_onnx_tests \
        --path_to_protoc_exe /opt/conda/envs/rapids/bin/protoc \
		--cmake_extra_defines 'CMAKE_CUDA_ARCHITECTURES=60;70;75;80' \
            'CMAKE_INSTALL_PREFIX=/opt/conda/envs/rapids' \
            'BUILD_ONNX_PYTHON=ON' \
            'CMAKE_C_COMPILER=/usr/local/bin/gcc' \
            'CMAKE_CXX_COMPILER=/usr/local/bin/g++' \
        --parallel 4 --update --build && cd build/Linux/Release && \
		make install && pip install dist/*

RUN source activate rapids && mamba install onnx=1.10.0

# # some errors
# RUN source activate rapids \
#     && cd /rapids && git clone https://github.com/xju2/prefix_sum.git \
#     && git clone https://github.com/murnanedaniel/FRNN.git && cd prefix_sum \
#     && NVCC_FLAGS="-gencode=arch=compute_70,code=sm_70 -gencode=arch=compute_80,code=sm_80" python setup.py install \
#     && pip install . && cd /rapids/FRNN \
#     && NVCC_FLAGS="-gencode=arch=compute_70,code=sm_70 -gencode=arch=compute_80,code=sm_80" python setup.py install \
#     && pip install . 

# RUN source activate rapids && \
    HOROVOD_WITH_TENSORFLOW=1 HOROVOD_WITH_PYTORCH=1 HOROVOD_WITH_MPI=1 \
	HOROVOD_WITH_GLOO=1 HOROVOD_GPU_OPERATIONS=NCCL \
    HOROVOD_BUILD_CUDA_CC_LIST=60,70,75,80 \
    HOROVOD_CUDA_HOME=/opt/conda/envs/rapids \
	pip install --no-cache-dir --user git+https://github.com/horovod/horovod.git@v0.23.0