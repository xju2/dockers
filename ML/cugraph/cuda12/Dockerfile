# docexoty/exatrkx:cugraph-cuda12
FROM nvidia/cuda:12.1.1-cudnn8-devel-ubuntu20.04

RUN apt-get update -y  && DEBIAN_FRONTEND="noninteractive" TZ="America/Los_Angeles"  apt-get -y install \
	git vim wget rsync curl build-essential && rm -rf /var/lib/apt/lists/*

ENV CONDA_DIR /opt/conda
# Make RUN commands use `bash --login`
SHELL ["/bin/bash", "--login", "-c"]

RUN wget \
    https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh \
    && /bin/bash Miniconda3-latest-Linux-x86_64.sh -fb -p /opt/conda \
    && rm -f Miniconda3-latest-Linux-x86_64.sh 

ENV PATH=${CONDA_DIR}/bin:$PATH
ENV PYTHONNOUSERSITE=True

# Initialize conda in bash config files:
RUN conda init bash

# Install cugraph
ARG MAX_CORE=4
RUN cd /tmp && export CUGRAPH_HOME=$(pwd)/cugraph && \
	git clone --recursive -b branch-23.06 https://github.com/rapidsai/cugraph.git && cd cugraph && \
	source activate && \
	conda env create --name cugraph --file conda/environments/all_cuda-118_arch-x86_64.yaml && \
	source activate cugraph && \
	./build.sh clean && \
	PARALLEL_LEVEL=${MAX_CORE} ./build.sh libcugraph --allgpuarch --skip_cpp_tests && \
	PARALLEL_LEVEL=${MAX_CORE} ./build.sh libcugraph_etl --allgpuarch --skip_cpp_tests && \
	PARALLEL_LEVEL=${MAX_CORE} ./build.sh pylibcugraph --allgpuarch --skip_cpp_tests && \
	PARALLEL_LEVEL=${MAX_CORE} ./build.sh cugraph --allgpuarch --skip_cpp_tests && \
	rm -rf /tmp/cugraph 

RUN source activate cugraph && \
	conda install ctools/label/dev::conda-pack && \
	conda install -y -c esrf-bcu libnuma