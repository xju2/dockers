## this is for docexoty/exatrkx:tf2.4-torch1.7.1-cuda11.0.3-ubuntu20.04
# python -c "import torch; print(torch.__version__)"
# python -c "import torch; print(torch.version.cuda)"

FROM nvidia/cuda:11.0.3-cudnn8-devel-ubuntu20.04

RUN apt-get update -y  && DEBIAN_FRONTEND="noninteractive" TZ="America/Los_Angeles"  apt-get -y install \
	python3-dev python3-pip git vim libhdf5-mpich-dev cmake nodejs 

# point python to python3.
RUN ln -s /usr/bin/python3.8 /usr/bin/python && pip install --user --upgrade pip 

ENV PYTHONUSERBASE="/usr/local/python3.8"
ENV PATH=/usr/local/root/bin:/usr/local/python3.8/bin:$PATH
ENV PYTHONPATH=${PYTHONPATH}:/usr/lib/python3.8/site-packages:/usr/local/root/lib/root
ENV LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:/usr/local/cuda/lib64

RUN pip install --user sympy numpy scipy numba mpi4py matplotlib seaborn

RUN pip install --user tensorflow==2.4 tensorboard==2.4

RUN pip install --user torch==1.7.1+cu110 -f https://download.pytorch.org/whl/torch_stable.html 

# pytorch-geometrics
RUN  pip install --user torch-scatter -f https://pytorch-geometric.com/whl/torch-1.7.0+cu110.html 
RUN  pip install --user --no-cache-dir torch-sparse==0.6.9  -f https://pytorch-geometric.com/whl/torch-1.7.0+cu110.html 
RUN  pip install --user torch-cluster -f https://pytorch-geometric.com/whl/torch-1.7.0+cu110.html \
	&& pip install --user torch-spline-conv -f https://pytorch-geometric.com/whl/torch-1.7.0+cu110.html \
	&& pip install --user torch-geometric

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


# RUN apt-get update -y && apt-get -y install libssl-dev 
# # install cmake 3.20.
# RUN mkdir /code && cd /code \
#     && wget https://github.com/Kitware/CMake/releases/download/v3.20.5/cmake-3.20.5.tar.gz -O- | tar xz \
#     && cd cmake-3.20.5 && cmake . && make -j && make install \
#     && cd / && rm -rf /code 

# # From cuDF DockerFile
# # https://github.com/rapidsai/cudf/blob/main/docker_build/Dockerfile
# RUN apt install -y --no-install-recommends \
#       build-essential \
#       libboost-all-dev \
#       libboost-filesystem-dev \
#       libboost-system-dev \
#       libboost-regex-dev \
#       autoconf \
#       bison \
#       flex \
#       libjemalloc-dev \
#       protobuf-compiler && \
#     apt-get autoremove -y && \
#     apt-get clean && \
#     rm -rf /var/lib/apt/lists/*

# ENV CUDA_HOME=/usr/local/cuda
# ENV CUDA_PATH=$CUDA_HOME
# ENV PATH=${CUDA_HOME}/lib64/:${PATH}:${CUDA_HOME}/bin

# # Build env variables for arrow
# ENV CMAKE_BUILD_TYPE=release
# ENV PYARROW_WITH_PARQUET=1
# ENV PYARROW_WITH_CUDA=1
# ENV PYARROW_WITH_ORC=1
# ENV PYARROW_WITH_DATASET=1
# ENV ARROW_HOME=/usr/local


# # Build env variables for rmm
# ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda/extras/CUPTI/lib64:/usr/local/lib
# RUN pip install --user cython
# # # Install arrow from source
# RUN git clone https://github.com/apache/arrow.git /repos/arrow \
#     && cd /repos/arrow && git checkout apache-arrow-1.0.1 && git submodule init \
#     && git submodule update && export PARQUET_TEST_DATA="${PWD}/cpp/submodules/parquet-testing/data" \
#     && export ARROW_TEST_DATA="${PWD}/testing/data" \
#     && cd /repos/arrow/cpp && mkdir release && cd /repos/arrow/cpp/release \
#     && cmake .. -DCMAKE_INSTALL_PREFIX=${ARROW_HOME} -DCMAKE_INSTALL_LIBDIR=lib -DARROW_FLIGHT=ON \
#         -DARROW_GANDIVA=OFF -DARROW_ORC=ON -DARROW_WITH_BZ2=ON -DARROW_WITH_ZLIB=ON \
#         -DARROW_WITH_ZSTD=ON -DARROW_WITH_LZ4=ON -DARROW_WITH_SNAPPY=ON -DARROW_WITH_BROTLI=ON \
#         -DARROW_PARQUET=ON -DARROW_PYTHON=ON -DARROW_PLASMA=ON \
#         -DARROW_BUILD_TESTS=OFF -DARROW_CUDA=ON -DARROW_DATASET=ON \
#         -DCMAKE_LIBRARY_PATH=/usr/local/cuda/lib64/stubs \
#     && make -j && make install && cd /repos/arrow/python/ \
#     && python setup.py build_ext --build-type=release bdist_wheel \
#     && pip install --user /repos/arrow/python/dist/*.whl 


# # # Install rmm from source
# RUN pip install --user numba
# RUN cd /repos/ && git clone https://github.com/rapidsai/rmm.git \
#     && cd /repos/rmm/ && mkdir build && cd build \
#     && cmake .. -DCMAKE_INSTALL_PREFIX=/usr/local \
#               -DCUDA_STATIC_RUNTIME=ON \
#               -DPER_THREAD_DEFAULT_STREAM=OFF \
#               -DCMAKE_BUILD_TYPE=Release \
#               -DCMAKE_LIBRARY_PATH=/usr/local/cuda/lib64/stubs \
#     && make -j && make install && cd /repos/rmm/python \
#     && pip install --user .
    
# # # Build env for CUDF build
# ENV CUDF_HOME=/repos/cudf/
# ENV CUDF_ROOT=/repos/cudf/cpp/build/

# # # Install cudf from source
# # cuFile is not install
# # https://docs.nvidia.com/gpudirect-storage/troubleshooting-guide/index.html#unique_1907865909
# # disable the AWS S3 filesystem

# RUN cd /repos && git clone https://github.com/rapidsai/cudf.git \
#     && cd /repos/cudf/ && git submodule update --init --recursive \
#     && mkdir build && cd build \
#     && cmake ../cpp -DCMAKE_INSTALL_PREFIX=/usr/local \
#         -DUSE_NVTX=ON \
#         -DBUILD_TESTS=OFF \
#         -DBUILD_BENCHMARKS=OFF \
#         -DDISABLE_DEPRECATION_WARNING=ON \
#         -DPER_THREAD_DEFAULT_STREAM=OFF \
#         -DCMAKE_BUILD_TYPE=Release \
#         -DCUDF_ENABLE_ARROW_S3=OFF \
#         -DCMAKE_LIBRARY_PATH=/usr/local/cuda/lib64/stubs \
#     && make -j && make install && cd /repos/cudf/python/cudf \
#     && /usr/bin/python -m pip install --user .

# RUN apt-get -y install libmkl-full-dev

# # cugraph
# RUN mkdir /repos && cd /repos \
#     && wget https://github.com/rapidsai/cugraph/archive/refs/tags/v21.06.00.tar.gz -O- | tar xz \
#     && mv cugraph-21.06.00 cugraph \
#     && mkdir build && cd build \
#     && cmake ../cpp -DCMAKE_INSTALL_PREFIX=/usr/local \
#           -DCMAKE_CUDA_ARCHITECTURES="ALL" \
#           -DDISABLE_DEPRECATION_WARNING=ON \
#           -DCMAKE_BUILD_TYPE=Release \
#           -DBUILD_STATIC_FAISS=ON \
#           -DBUILD_CUGRAPH_MG_TESTS=ON \
#           -DCMAKE_LIBRARY_PATH=/usr/local/cuda/lib64/stubs \
#     && cmake --build . --target install -- -j \
#     && cd ../python \
#     && CUGRAPH_BUILD_PATH=/repos/cugraph/build python setup.py build_ext --inplace --library-dir=/repos/cugraph/build \
#     && CUGRAPH_BUILD_PATH=/repos/cugraph/build python setup.py install --user 