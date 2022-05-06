# docexoty/exatrkx:triton-server-rapids
ARG TRITON_VERSION=22.02-py3
FROM nvcr.io/nvidia/tritonserver:${TRITON_VERSION}


RUN cd /tmp && \
	git clone --recursive https://github.com/pytorch/pytorch.git && cd pytorch && \
	git checkout -b r1.11 origin/release/1.11 && \
	git submodule sync && git submodule update --init --recursive --jobs 0 && \
	MAX_JOBS=6 USE_CUDA=1 USE_OPENMP=1 USE_MPI=0 USE_TBB=1 USE_SYSTEM_TBB=1 USE_MKLDNN=1 \
	MKL_THREADING=TBB MKLDNN_CPU_RUNTIME=TBB BUILD_BINARY=0 BUILD_CUSTOM_PROTOBUF=OFF \
	CMAKE_PREFIX_PATH=/workspace/build/third-party/protobuf python setup.py build --cmake-only && \
	cmake --build build --config Release --target install && rm -rf /tmp/pytorch

# ENV PYTHONPATH=${PYTHONPATH}:/usr/local/lib/python3.8/site-packages 