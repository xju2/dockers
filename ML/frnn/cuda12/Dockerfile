# docexoty/exatrkx:frnn-cuda12
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

# # create the environment
RUN source activate && conda create -y -n frnn python=3.10

# Initialize conda in bash config files:
RUN conda init bash

ENV PYTHONNOUSERSITE=True
RUN source activate frnn && \
    conda install -y astunparse charset-normalizer pycparser numpy ninja pyyaml mkl mkl-include setuptools cmake \
        cffi typing_extensions future six requests dataclasses \
		sympy filelock networkx jinja2 fsspec psutil && \
    conda install -y -c pytorch magma-cuda121

# Get pytorch source and build
RUN source activate frnn && cd /tmp && \
	git clone --recursive https://github.com/pytorch/pytorch.git && cd pytorch && \
	git checkout -b r2.0 origin/release/2.0 && \
	git submodule sync && git submodule update --init --recursive --jobs 0 && \
	MAX_JOBS=4 USE_CUDA=1 CMAKE_PREFIX_PATH=${CONDA_PREFIX:-"$(dirname $(which conda))/../"} \
	TORCH_CUDA_ARCH_LIST="6.1 7.0 7.5 8.0 8.6" python setup.py install && \
	rm -rf /tmp/pytorch

RUN source activate frnn && cd /tmp/ \
	&& git clone https://github.com/xju2/prefix_sum.git \
    && git clone https://github.com/xju2/FRNN.git \
	&& cd prefix_sum \
	&& NVCC_FLAGS="-gencode=arch=compute_61,code=sm_61 -gencode=arch=compute_70,code=sm_70 -gencode=arch=compute_75,code=sm_75 -gencode=arch=compute_80,code=sm_80 -gencode=arch=compute_86,code=sm_86" \
		python setup.py install \
    && cd /tmp/FRNN \
    && NVCC_FLAGS="-gencode=arch=compute_61,code=sm_61 -gencode=arch=compute_70,code=sm_70 -gencode=arch=compute_75,code=sm_75 -gencode=arch=compute_80,code=sm_80 -gencode=arch=compute_86,code=sm_86" \
		python setup.py install && \
	rm -rf /tmp/prefix_sum && rm -rf /tmp/FRNN 

RUN source activate frnn && conda install conda-pack