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


# Geant4
RUN mkdir src \
  && ${GET} https://gitlab.cern.ch/geant4/geant4/-/archive/v11.1.1/geant4-v11.1.1.tar.gz \
    | ${UNPACK_TO_SRC} \
  && cmake -B build -S src -GNinja \
    -DCMAKE_BUILD_TYPE=Release \
    -DCMAKE_INSTALL_PREFIX=${PREFIX} \
    -DGEANT4_BUILD_TLS_MODEL=global-dynamic \
    -DGEANT4_INSTALL_DATA=ON \
    -DGEANT4_USE_GDML=ON \
    -DGEANT4_USE_SYSTEM_EXPAT=ON \
    -DGEANT4_USE_SYSTEM_ZLIB=ON \
  && cmake --build build -- install \
  && rm -rf build src

ENV G4DATA=/usr/local/share/Geant4/data
ENV G4ENSDFSTATEDATA=${G4DATA}/G4ENSDFSTATE2.3 \
    G4LEVELGAMMADATA=${G4DATA}/PhotonEvaporation5.7 \
    G4PARTICLEXSDATA=${G4DATA}/G4PARTICLEXS4.0 \
    G4RADIOACTIVEDATA=${G4DATA}/RadioactiveDecay5.6 \
    G4LEDATA=${G4DATA}/G4EMLOW8.2 \
    G4NEUTRONHPDATA=${G4DATA}/G4NDL4.7

# environment variables needed to find ROOT libraries
ENV LD_LIBRARY_PATH /usr/local/lib
ENV PYTHON_PATH /usr/local/lib

# nlohmann's JSON
RUN mkdir src \
  && ${GET} https://github.com/nlohmann/json/archive/refs/tags/v3.11.2.tar.gz \
    | ${UNPACK_TO_SRC} \
  && cmake -B build -S src -GNinja \
    -DCMAKE_BUILD_TYPE=Release \
    -DJSON_BuildTests=OFF \
  && cmake --build build -- install \
  && rm -rf build src

# podio
RUN mkdir src \
  && ${GET} https://github.com/AIDASoft/podio/archive/refs/tags/v00-16-03.tar.gz \
    | ${UNPACK_TO_SRC} \
  && cmake -B build -S src -GNinja \
    -DCMAKE_BUILD_TYPE=Release \
    -DCMAKE_INSTALL_PREFIX=${PREFIX} \
    -DBUILD_TESTING=OFF \
    -USE_EXTERNAL_CATCH2=OFF \
  && cmake --build build -- install \
  && rm -rf build src

# EDM4hep
RUN pip3 install jinja2 pyyaml \
  && mkdir src \
  && ${GET} https://github.com/key4hep/EDM4hep/archive/refs/tags/v00-07-02.tar.gz \
    | ${UNPACK_TO_SRC} \
  && cmake -B build -S src -GNinja \
    -DCMAKE_BUILD_TYPE=Release \
    -DCMAKE_INSTALL_PREFIX=${PREFIX} \
    -DBUILD_TESTING=OFF \
    -DUSE_EXTERNAL_CATCH2=OFF \
  && cmake --build build -- install \
  && rm -rf build src

# DD4hep
# requires Geant4 and ROOT and must come last
RUN mkdir src \
  && ${GET} https://github.com/AIDASoft/DD4hep/archive/v01-25-01.tar.gz \
    | ${UNPACK_TO_SRC} \
  && cmake -B build -S src -GNinja \
    -DCMAKE_BUILD_TYPE=Release \
    -DCMAKE_CXX_STANDARD=17 \
    -DCMAKE_INSTALL_PREFIX=${PREFIX} \
    -DCMAKE_PREFIX_PATH=${PREFIX} \
    -DBUILD_TESTING=OFF \
    -DDD4HEP_BUILD_PACKAGES="DDG4 DDDetectors DDRec UtilityApps" \
    -DDD4HEP_IGNORE_GEANT4_TLS=ON \
    -DDD4HEP_USE_GEANT4=ON \
    -DDD4HEP_USE_XERCESC=ON \
    -DDD4HEP_USE_EDM4HEP=ON \
  && cmake --build build -- install \
  && rm -rf build src


# Require cmake 3.21.2 or higher. The previous version is 3.21.2
RUN mkdir src \
  && ${GET} https://github.com/Kitware/CMake/releases/download/v3.26.4/cmake-3.26.4-Linux-x86_64.tar.gz \
    | ${UNPACK_TO_SRC} \
  && rsync -ruv src/ ${PREFIX} \
  && cd .. \
  && rm -rf src

RUN apt-get install -y libblas-dev liblapack-dev swig
RUN ln -s /usr/bin/python3 /usr/bin/python

RUN pip3 install numpy
# Faiss for fast similarity search
RUN mkdir src \
  && ${GET} https://github.com/facebookresearch/faiss/archive/refs/tags/v1.7.4.tar.gz \
    | ${UNPACK_TO_SRC} \
    && cd src && mkdir build && cd build \
    && cmake .. -DFAISS_ENABLE_GPU=ON -DFAISS_ENABLE_PYTHON=ON \
        -DFAISS_ENABLE_C_API=ON \
        -DCMAKE_CUDA_ARCHITECTURES="60-real;70-real;75-real;80" \
        -DPython_EXECUTABLE=/usr/bin/python -DPython_LIBRARIES=/usr/lib/python3.8 \
        -DCMAKE_INSTALL_PREFIX=${PREFIX} \
    && make -j faiss && make -j swigfaiss \
    && cd faiss/python && pip3 install . \
    && cd ../.. && make install && cd .. \
    && rm -rf src

RUN apt-get install -y libmkl-full-dev && pip3 install mkl-devel
