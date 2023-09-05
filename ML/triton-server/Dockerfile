# docexoty/exatrkx-triton-cpu
ARG TRITON_VERSION=22.02-py3
FROM nvcr.io/nvidia/tritonserver:${TRITON_VERSION}

# Update the CUDA Linux GPG Repository Key
RUN apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/3bf863cc.pub 

# See also https://root.cern.ch/build-prerequisites
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
    rapidjson-dev \
  && apt-get clean -y

# Manual builds for specific packages
RUN ln -s /usr/bin/python3 /usr/bin/python
RUN pip3 install --upgrade pip
ENV LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/usr/local/lib"
ENV PYTHON_PATH="/usr/local/lib"

ENV GET curl --location --silent --create-dirs
ENV UNPACK_TO_SRC tar -xz --strip-components=1 --directory src
ENV PREFIX /usr/local
# Append to the bashrc to set the PYTHONPATH
ENV PYTHONPATH "${PYTHONPATH}:/usr/lib/python3.8/site-packages/"

# Require cmake 3.21.2 or higher. The previous version is 3.21.2
RUN cd /tmp && mkdir -p src \
  && ${GET} https://github.com/Kitware/CMake/releases/download/v3.26.4/cmake-3.26.4-Linux-x86_64.tar.gz \
    | ${UNPACK_TO_SRC} \
  && rsync -ruv src/ ${PREFIX} \
  && cd /tmp && rm -rf /tmp/src  

RUN cd /tmp && \
	git clone --recursive https://github.com/pytorch/pytorch.git && cd pytorch && \
	git checkout -b r1.11 origin/release/1.11 && \
	git submodule sync && git submodule update --init --recursive --jobs 8 && \
    export _GLIBCXX_USE_CXX11_ABI=1 && pip3 install -r requirements.txt && \
	MAX_JOBS=20 USE_CUDA=0 USE_MKL=ON USE_TBB=1 python setup.py install && \
	cd /tmp && rm -rf /tmp/pytorch

# # # Install Torch scatter
# # RUN pip install torch-scatter -f https://data.pyg.org/whl/torch-1.11.0+cpu.html
RUN cd /tmp && mkdir -p src \
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

# # # Faiss for fast similarity search
RUN cd /tmp && mkdir -p src \
  && ${GET} https://github.com/facebookresearch/faiss/archive/refs/tags/v1.7.4.tar.gz \
    | ${UNPACK_TO_SRC} \
  && cd src && mkdir build && cd build \
  && cmake .. -DFAISS_ENABLE_GPU=OFF -DFAISS_ENABLE_PYTHON=ON \
        -DFAISS_ENABLE_C_API=ON -DBUILD_SHARED_LIBS=ON \
        -DPython_EXECUTABLE=/usr/bin/python -DPython_LIBRARIES=/usr/lib/python3.8 \
        -DCMAKE_INSTALL_PREFIX=${PREFIX} \
  && make -j8 faiss && make -j8 swigfaiss \
  && cd faiss/python && pip3 install . \
  && cd ../.. && make install -j8 && cd .. \
  && rm -rf src

# To build exatrkx_triton, a cpp version of client  
# Install grpc
RUN git clone --recurse-submodules -b v1.49.1 --depth 1 https://github.com/grpc/grpc src\
    && cmake -B build -S src -DgRPC_INSTALL=ON \
        -DgRPC_BUILD_TESTS=OFF \
        -DCMAKE_INSTALL_PREFIX=${PREFIX} \
        -DCMAKE_BUILD_TYPE=Release \
    && cmake --build build -- install -j20 \
    && rm -rf src build

# Install triton
RUN git clone https://github.com/triton-inference-server/client.git \
    && cd client && mkdir build && cd build \
    && cmake ../src/c++ -DTRITON_ENABLE_CC_HTTP=OFF \
        -DTRITON_ENABLE_CC_GRPC=ON \
        -DTRITON_ENABLE_PYTHON_GRPC=ON \
        -DCMAKE_PREFIX_PATH="${PREFIX}/lib64/cmake;${PREFIX}/lib/cmake" \
        -DCMAKE_INSTALL_PREFIX=${PREFIX} \
        -DTRITON_USE_THIRD_PARTY=OFF  \
   && make -j20 && make install \
   && cd ../.. && rm -rf client
