# docexoty/mltools:ubuntu20.04
FROM nvidia/cuda:11.2.2-cudnn8-devel-ubuntu20.04

RUN apt-get update -y  && DEBIAN_FRONTEND="noninteractive" TZ="America/Los_Angeles"  apt-get -y install \
	python3-dev python3-pip git vim libhdf5-mpich-dev cmake nodejs

# point python to python3.
RUN ln -s /usr/bin/python3.8 /usr/bin/python && pip install --user --upgrade pip 

ENV PYTHONUSERBASE="/usr/local/python3.8"
ENV PATH=/usr/local/root/bin:/usr/local/python3.8/bin:$PATH
ENV PYTHONPATH=${PYTHONPATH}:/usr/lib/python3.8/site-packages:/usr/local/root/lib/root
ENV LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:/usr/local/cuda/lib64

RUN pip install --user torch==1.9.0+cu111 torchvision==0.10.0+cu111 torchaudio===0.9.0 -f https://download.pytorch.org/whl/torch_stable.html \
    && pip install --user torch-scatter -f https://pytorch-geometric.com/whl/torch-1.9.0+cu111.html \
    && pip install --user torch-sparse  -f https://pytorch-geometric.com/whl/torch-1.9.0+cu111.html \
    && pip install --user torch-cluster -f https://pytorch-geometric.com/whl/torch-1.9.0+cu111.html \
    && pip install --user torch-spline-conv -f https://pytorch-geometric.com/whl/torch-1.9.0+cu111.html \
    && pip install --user torch-geometric \
    && pip install --user tensorflow_gpu==2.5 \
	&& pip install --user pytorch-lightning \
    		git+https://github.com/LAL/trackml-library.git \
    		git+https://github.com/deepmind/graph_nets.git \
    		matplotlib sklearn pyyaml>=5.1 tqdm networkx pytest onnx \
	&& pip install --user -U tf2onnx

RUN HOROVOD_WITH_TENSORFLOW=1 HOROVOD_WITH_PYTORCH=1 HOROVOD_WITH_MPI=1 HOROVOD_WITH_GLOO=1 HOROVOD_GPU_OPERATIONS=NCCL pip install --no-cache-dir --user git+https://github.com/horovod/horovod.git@v0.21.3

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

# Install cmake
ARG CMAKE_VERSION=3.21.1
RUN cd /tmp && \
    wget https://github.com/Kitware/CMake/releases/download/v${CMAKE_VERSION}/cmake-${CMAKE_VERSION}-linux-x86_64.sh && \
    bash cmake-${CMAKE_VERSION}-linux-x86_64.sh --prefix=/usr/local --exclude-subdir --skip-license \
	&& rm -rf /tmp/*

# Install ONNX Runtime
# https://github.com/leimao/ONNX-Runtime-Inference/blob/main/docker/onnxruntime-cuda.Dockerfile#L115
RUN cd /tmp && \
    git clone --recursive --branch v1.8.2 https://github.com/Microsoft/onnxruntime && \
    cd onnxruntime && \
    ./build.sh \
        --cuda_home /usr/local/cuda \
        --cudnn_home /usr/lib/x86_64-linux-gnu/ \
        --use_cuda \
        # --use_tensorrt \
        # --tensorrt_home /usr/lib/x86_64-linux-gnu/ \
        --config RelWithDebInfo \
        --build_shared_lib \
        --build_wheel \
        --skip_tests \
        --parallel ${NUM_JOBS} && \
    cd build/Linux/RelWithDebInfo && \
    make install && \
    pip install --user dist/* && rm -rf /tmp/* 

# Install clang-format
RUN apt-get update -y && apt-get install -y clang-format


# faiss-gpu
RUN pip install --user faiss-gpu
# RUN cd /tmp && git clone https://github.com/facebookresearch/faiss.git \
#   && cd faiss && mkdir build && cd build \
#   && cmake .. -DFAISS_ENABLE_GPU=ON -DFAISS_ENABLE_PYTHON=ON \
#   -DCMAKE_CUDA_ARCHITECTURES="60-real;70-real;75-real;80" \
#   -DPython_EXECUTABLE=`which python`\ 
#   -DCMAKE_INSTALL_PREFIX=`/usr/local` \
#   && make -j faiss  && make -j swigfaiss \
#   && cd faiss/python && pip install --user . \
#   && cd /rapids/faiss/build && make install 
