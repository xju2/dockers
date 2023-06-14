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
   