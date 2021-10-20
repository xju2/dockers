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
    cd onnxruntime \
    && . /opt/conda/etc/profile.d/conda.sh && conda activate rapids \
    && for FILE in /opt/conda/envs/rapids/include/cudnn*;\
        do ln -s $FILE /usr/include/$(basename $FILE); done \
    && for FILE in /opt/conda/envs/rapids/lib/libcudnn*.so;\
        do ln -s $FILE /usr/lib/x86_64-linux-gnu/$(basename $FILE); done \
    && ./build.sh --skip_submodule_sync \
        --cuda_home /opt/conda/envs/rapids \
        --cudnn_home /usr/lib/x86_64-linux-gnu/ \
        --use_cuda \
        --config RelWithDebInfo \
        --build_wheel \
		--build_shared_lib \
		--skip_tests \
		--cmake_extra_defines 'CMAKE_CUDA_ARCHITECTURES=60;70;75;80' \
        --parallel && \
		cd build/Linux/RelWithDebInfo && \
		make install && \
		pip install dist/*  && rm -rf /tmp/* 
        #--use_tensorrt \
        #--tensorrt_home /usr/lib/x86_64-linux-gnu/ \
    #     && \
# the above works

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


RUN . /opt/conda/etc/profile.d/conda.sh && conda activate rapids \
    && mamba install -y -c conda-forge -c omnia tbb-devel=2020.2 eigen3

ENV INSTALL_PREFIX=/opt/conda/envs/rapids

RUN ln -s /lib/x86_64-linux-gnu/libpthread.so.0 /lib64/ && \
    ln -s /lib/x86_64-linux-gnu/libpthread.a  /usr/lib64/libpthread_nonshared.a && \
    ln -s /lib/x86_64-linux-gnu/libc.a /usr/lib64/libc_nonshared.a && \
    ln -s /lib/x86_64-linux-gnu/libc.so.6 /lib64/libc.so.6

RUN mkdir /code && cd /code \
    && . /opt/conda/etc/profile.d/conda.sh && conda activate rapids \
    && wget https://lhapdf.hepforge.org/downloads/?f=LHAPDF-6.2.3.tar.gz -O- | tar xz \
    && cd LHAPDF-6.2.3/ && ./configure --prefix=${INSTALL_PREFIX} \
    && make -j && make install \
    && cd ../.. && rm -r /code

RUN 
