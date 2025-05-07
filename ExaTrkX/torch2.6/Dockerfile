# docexoty/exatrkx:cuda12-pytorch2.6
FROM nvidia/cuda:12.6.3-cudnn-devel-ubuntu20.04

# ROOT core dependences.
RUN apt-get update -y && DEBIAN_FRONTEND="noninteractive" TZ="America/Los_Angeles" apt-get -y install \
	dpkg-dev cmake g++ gcc binutils libx11-dev libxpm-dev \
	libxft-dev libxext-dev python libssl-dev 

# # ROOT optional packages
RUN apt-get --fix-missing -y install \
	gfortran libpcre3-dev \
	xlibmesa-glu-dev libglew-dev libftgl-dev \
	libmysqlclient-dev libfftw3-dev libcfitsio-dev \
	graphviz-dev \
	libldap2-dev python-dev libxml2-dev libkrb5-dev \
	libgsl0-dev wget \
	libtbb2 libtbb-dev libboost-all-dev vim \
	&& rm -rf /var/lib/apt/lists/*

RUN apt-get update -y  && apt-get -y install python3-dev python3-pip python3-sphinx \
	git libeigen3-dev xtl-dev libhdf5-mpich-dev \
	libxerces-c-dev libxerces-c3.2 rsync autoconf automake nodejs doxygen \
	valgrind \
	&& rm -rf /var/lib/apt/lists/*

ENV CONDA_DIR /opt/conda
ENV PYTHONNOUSERSITE=True
# Make RUN commands use `bash --login`
SHELL ["/bin/bash", "--login", "-c"]

RUN wget \
    https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh \
    && /bin/bash Miniconda3-latest-Linux-x86_64.sh -fb -p /opt/conda \
    && rm -f Miniconda3-latest-Linux-x86_64.sh 

ENV PATH=${CONDA_DIR}/bin:$PATH

ENV CONDA_ENV_NAME=gnn4itk
# create the environment
RUN source activate && conda create -y -n $CONDA_ENV_NAME python=3.12  

RUN source activate $CONDA_ENV_NAME \
	&& pip install torch==2.6.0 torchvision==0.21.0 torchaudio==2.6.0 torch-tensorrt tensorrt  --index-url https://download.pytorch.org/whl/cu126 \
	&& pip install pyg_lib torch_scatter torch_sparse torch_cluster torch_spline_conv -f https://data.pyg.org/whl/torch-2.6.0+cu126.html \
	&& pip install torch_geometric lightning\
	&& pip install --extra-index-url=https://pypi.nvidia.com cudf-cu12 cugraph-cu12 nx-cugraph-cu12 \
	&& pip install git+https://github.com/LAL/trackml-library.git \
	pyyaml click pytest pytest-cov class-resolver scipy pandas matplotlib uproot \
	tqdm ipykernel atlasify networkx seaborn wandb onnx onnxruntime pre-commit snakemake

RUN source activate $CONDA_ENV_NAME && conda install conda-forge::root


# FRNN
ENV PYTHONNOUSERSITE=True
RUN source activate $CONDA_ENV_NAME && cd /tmp/ \
	&& git clone https://github.com/asnaylor/prefix_sum.git \
    && git clone https://github.com/xju2/FRNN.git \
	&& cd prefix_sum \
	&& NVCC_FLAGS="--std=c++17" python setup.py install \
    && cd /tmp/FRNN \
    && NVCC_FLAGS="--std=c++17" python setup.py install && \
	rm -rf /tmp/prefix_sum && rm -rf /tmp/FRNN

