# docexoty/exatrkx:torch-rapids
ARG RAPIDS_VERSION=22.02-cuda11.5-devel-ubuntu20.04-py3.9
FROM rapidsai/rapidsai-dev:${RAPIDS_VERSION}

ARG CMAKE_VERSION=3.22.3
# need to visit
ARG PYTORCH_VERSION=1.11.0 
ARG CUDANAME=cu115

# Install cmake
RUN cd /tmp && source activate rapids \
    && wget https://github.com/Kitware/CMake/releases/download/v${CMAKE_VERSION}/cmake-${CMAKE_VERSION}-linux-x86_64.sh \
    && bash cmake-${CMAKE_VERSION}-linux-x86_64.sh --prefix=/opt/conda/envs/rapids --exclude-subdir --skip-license \
	&& rm -rf /tmp/*

RUN source activate rapids && \
    pip install torch==${PYTORCH_VERSION}+${CUDANAME} \
    torchvision==0.12.0+${CUDANAME} \
    torchaudio===0.11.0 -f https://download.pytorch.org/whl/${CUDANAME}/torch_stable.html

RUN source activate rapids && \
    conda install -c conda-forge mamba && \
    mamba install -y swig cudnn=8.2.1 pybind11

RUN source activate rapids && \
    cd /tmp && \
    git clone --recursive https://github.com/pytorch/kineto.git && \
    cd kineto/libkineto &&  mkdir build && cd build && cmake .. \
        -DCMAKE_C_COMPILER=/usr/local/bin/gcc \
        -DCMAKE_CXX_COMPILER=/usr/local/bin/g++ && \
    make -j4 && make install && rm -rf /tmp/*
