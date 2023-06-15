# docexoty/acts-triton:latest
# Based on https://github.com/acts-project/machines/blob/master/ubuntu2004_exatrkx/Dockerfile

from ghcr.io/acts-project/ubuntu2004_exatrkx

# Install grpc
RUN git clone --recurse-submodules -b v1.49.1 --depth 1 https://github.com/grpc/grpc src\
    && cmake -B build -S src -DgRPC_INSTALL=ON \
        -DgRPC_BUILD_TESTS=OFF \
        -DCMAKE_INSTALL_PREFIX=${PREFIX} \
        -DCMAKE_BUILD_TYPE=Release \
    && cmake --build build -- install \
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
   && make -j4 && make install \
   && cd ../.. && rm -rf client

# Install Torch scatter
RUN mkdir src \
  && ${GET} https://github.com/rusty1s/pytorch_scatter/archive/refs/tags/2.0.9.tar.gz \
    | ${UNPACK_TO_SRC} \
  && cmake -B build -S src -GNinja \
    -DCMAKE_BUILD_TYPE=Release \
    -DCMAKE_CXX_STANDARD=17 \
    -DCMAKE_INSTALL_PREFIX=${PREFIX} \
    -DWITH_CUDA=ON \
    -DCMAKE_CUDA_FLAGS=-D__CUDA_NO_HALF_CONVERSIONS__ \
    -DCMAKE_CUDA_ARCHITECTURES=${CUDA_ARCH} \
  && cmake --build build -- install \
  && rm -rf build src

# Pythia8
# requires rsync; installation uses `rsync` instead of `install`
RUN mkdir src \
  && ${GET} https://pythia.org/download/pythia83/pythia8309.tgz\
    | ${UNPACK_TO_SRC} \
  && cd src \
  && ./configure --enable-shared --prefix=${PREFIX} \
  && make -j$(nproc) install \
  && cd .. \
  && rm -rf src

# HepMC3
RUN mkdir src \
  && ${GET} https://gitlab.cern.ch/hepmc/HepMC3/-/archive/3.2.5/HepMC3-3.2.5.tar.gz \
    | ${UNPACK_TO_SRC} \
  && cmake -B build -S src -GNinja \
    -DCMAKE_BUILD_TYPE=Release \
    -DCMAKE_INSTALL_PREFIX=${PREFIX} \
    -DHEPMC3_BUILD_STATIC_LIBS=OFF \
    -DHEPMC3_ENABLE_PYTHON=ON \
    -DHEPMC3_ENABLE_ROOTIO=OFF \
    -DHEPMC3_ENABLE_SEARCH=OFF \
    -DHEPMC3_BUILD_EXAMPLES=OFF \
  && cmake --build build -- install \
  && rm -rf build src

