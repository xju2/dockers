# docexoty/exatrkx-cpu
# Based on https://github.com/acts-project/machines/blob/master/ubuntu2004/Dockerfile
from ubuntu:20.04

ENV DEBIAN_FRONTEND=noninteractive

# see also https://root.cern.ch/build-prerequisites
RUN apt-get update -y \
  && apt-get install -y \
    build-essential \
    curl \
    git \
    libboost-dev \
    libboost-filesystem-dev \
    libboost-program-options-dev \
    libboost-test-dev \
    libtbb-dev \
    ninja-build \
    python3 \
    python3-dev \
    python3-pip \
    python3-numpy \
    rsync \
    zlib1g-dev \
    ccache \
    vim \
    unzip \
    libblas-dev liblapack-dev swig \
  && apt-get clean -y

# manual builds for specific packages
RUN ln -s /usr/bin/python3 /usr/bin/python
ENV LD_LIBRARY_PATH /usr/local/lib
ENV PYTHON_PATH /usr/local/lib

ENV GET curl --location --silent --create-dirs
ENV UNPACK_TO_SRC tar -xz --strip-components=1 --directory src
ENV PREFIX /usr/local

# Require cmake 3.21.2 or higher. The previous version is 3.21.2
RUN mkdir src \
  && ${GET} https://github.com/Kitware/CMake/releases/download/v3.26.4/cmake-3.26.4-Linux-x86_64.tar.gz \
    | ${UNPACK_TO_SRC} \
  && rsync -ruv src/ ${PREFIX} \
  && cd .. \
  && rm -rf src

  # Get pytorch source and build
RUN cd /tmp && \
	git clone --recursive https://github.com/pytorch/pytorch.git && cd pytorch && \
	git checkout -b r1.11 origin/release/1.11 && \
	git submodule sync && git submodule update --init --recursive --jobs 0 && \
    export _GLIBCXX_USE_CXX11_ABI=1 && pip3 install -r requirements.txt && \
	MAX_JOBS=3 USE_CUDA=0 USE_MKL=ON CMAKE_PREFIX_PATH=${PREFIX} python setup.py install && \
	rm -rf /tmp/pytorch

# Install Torch scatter
RUN cd /tmp && mkdir src \
  && ${GET} https://github.com/rusty1s/pytorch_scatter/archive/refs/tags/2.0.9.tar.gz \
    | ${UNPACK_TO_SRC} \
  && cmake -B build -S src -GNinja \
    -DCMAKE_BUILD_TYPE=Release \
    -DCMAKE_CXX_STANDARD=17 \
    -DCMAKE_INSTALL_PREFIX=${PREFIX} \
    -DWITH_CUDA=OFF \
    -DCMAKE_PREFIX_PATH="$(python -c 'import torch;print(torch.utils.cmake_prefix_path)')" \
  && cmake --build build -- install \
  && rm -rf build src


# Faiss for fast similarity search
RUN cd /tmp && mkdir src \
  && ${GET} https://github.com/facebookresearch/faiss/archive/refs/tags/v1.7.4.tar.gz \
    | ${UNPACK_TO_SRC} \
    && cd src && mkdir build && cd build \
    && cmake .. -DFAISS_ENABLE_GPU=OFF -DFAISS_ENABLE_PYTHON=ON \
        -DFAISS_ENABLE_C_API=ON \
        -DPython_EXECUTABLE=/usr/bin/python -DPython_LIBRARIES=/usr/lib/python3.8 \
        -DCMAKE_INSTALL_PREFIX=${PREFIX} \
    && make -j faiss && make -j swigfaiss \
    && cd faiss/python && pip3 install . \
    && cd ../.. && make install && cd .. \
    && rm -rf src

# # RUN apt-get install -y libmkl-full-dev && pip3 install mkl-devel