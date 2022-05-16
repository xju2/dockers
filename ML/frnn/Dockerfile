# docexoty/exatrkx:frnn
FROM nvidia/cuda:11.6.2-cudnn8-devel-ubuntu20.04

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
RUN source activate && conda create -y -n frnn python=3.8 

# Initialize conda in bash config fiiles:
RUN conda init bash

RUN source activate frnn && \
    conda install -y astunparse numpy ninja pyyaml mkl mkl-include setuptools cmake \
        cffi typing_extensions future six requests dataclasses && \
    conda install -y -c pytorch magma-cuda116

# Get pytorch source and build
RUN source activate frnn && cd /tmp && \
	git clone --recursive https://github.com/pytorch/pytorch.git && cd pytorch && \
	git checkout -b r1.11 origin/release/1.11 && \
	git submodule sync && git submodule update --init --recursive --jobs 0 && \
	MAX_JOBS=4 USE_CUDA=1 CMAKE_PREFIX_PATH=${CONDA_PREFIX:-"$(dirname $(which conda))/../"} \
	TORCH_CUDA_ARCH_LIST="6.1 7.0 7.5 8.0 8.6" python setup.py install && \
	rm -rf /tmp/pytorch


# FRNN
ENV PYTHONNOUSERSITE=True
RUN source activate frnn && cd /tmp/ \
	&& git clone https://github.com/xju2/prefix_sum.git \
    && git clone https://github.com/murnanedaniel/FRNN.git \
	&& cd prefix_sum \
	&& NVCC_FLAGS="-gencode=arch=compute_61,code=sm_61 -gencode=arch=compute_70,code=sm_70 -gencode=arch=compute_75,code=sm_75 -gencode=arch=compute_80,code=sm_80 -gencode=arch=compute_86,code=sm_86" \
		python setup.py install \
    && cd /tmp/FRNN \
    && NVCC_FLAGS="-gencode=arch=compute_61,code=sm_61 -gencode=arch=compute_70,code=sm_70 -gencode=arch=compute_75,code=sm_75 -gencode=arch=compute_80,code=sm_80 -gencode=arch=compute_86,code=sm_86" \
		python setup.py install && \
	rm -rf /tmp/prefix_sum /tmp/FRNN