# docexoty/exatrkx:cuda12-pytorch2.1

FROM nvidia/cuda:12.2.2-cudnn8-devel-ubuntu20.04

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
	valgrind

ENV CONDA_DIR /opt/conda
# Make RUN commands use `bash --login`
SHELL ["/bin/bash", "--login", "-c"]

RUN wget \
    https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh \
    && /bin/bash Miniconda3-latest-Linux-x86_64.sh -fb -p /opt/conda \
    && rm -f Miniconda3-latest-Linux-x86_64.sh 

ENV PATH=${CONDA_DIR}/bin:$PATH

ARG CONDA_ENV_NAME=gnn4itk
# create the environment
RUN source activate && conda create -y -n $CONDA_ENV_NAME python=3.9

RUN source activate $CONDA_ENV_NAME && conda update -n base -c defaults conda    

RUN source activate $CONDA_ENV_NAME && \
    conda install -y astunparse charset-normalizer pycparser numpy ninja pyyaml mkl mkl-include setuptools cmake \
        cffi typing_extensions future six requests dataclasses \
		sympy filelock networkx jinja2 fsspec psutil && \
    conda install -y -c pytorch magma-cuda121

# Get pytorch source and build so that it runs on different GPUs.
RUN source activate $CONDA_ENV_NAME && cd /tmp && \
	git clone --recursive https://github.com/pytorch/pytorch.git && cd pytorch && \
	git checkout -b r2.1 origin/release/2.1 && \
	git submodule sync && git submodule update --init --recursive --jobs 0 && \
	MAX_JOBS=4 USE_CUDA=1 BUILD_TEST=0 USE_FBGEMM=1 USE_QNNPACK=1 USE_DISTRIBUTED=1 BUILD_CAFFE2=1 DEBUG=0 \
	  CMAKE_PREFIX_PATH=${CONDA_PREFIX:-"$(dirname $(which conda))/../"} \
	  TORCH_CUDA_ARCH_LIST="6.1 7.0 7.5 8.0 8.6" python setup.py install && \
	rm -rf /tmp/pytorch

# FRNN
ENV PYTHONNOUSERSITE=True
RUN source activate $CONDA_ENV_NAME && cd /tmp/ \
	&& git clone https://github.com/asnaylor/prefix_sum.git \
    && git clone https://github.com/xju2/FRNN.git \
	&& cd prefix_sum \
	&& NVCC_FLAGS="--std=c++17 -gencode=arch=compute_61,code=sm_61 -gencode=arch=compute_70,code=sm_70 -gencode=arch=compute_75,code=sm_75 -gencode=arch=compute_80,code=sm_80 -gencode=arch=compute_86,code=sm_86" \
		python setup.py install \
    && cd /tmp/FRNN \
    && NVCC_FLAGS="--std=c++17 -gencode=arch=compute_61,code=sm_61 -gencode=arch=compute_70,code=sm_70 -gencode=arch=compute_75,code=sm_75 -gencode=arch=compute_80,code=sm_80 -gencode=arch=compute_86,code=sm_86" \
		python setup.py install && \
	rm -rf /tmp/prefix_sum && rm -rf /tmp/FRNN

ENV LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:/usr/local/cuda/lib64:/usr/lib64/mpich/lib:/usr/local/lib:/opt/conda/envs/gnn4itk/lib/python3.9/site-packages/torch/lib

# additional Python package needed for acorn
RUN source activate ${CONDA_ENV_NAME} && pip install git+https://github.com/LAL/trackml-library.git \
  pyyaml click pytest pytest-cov class-resolver scipy pandas matplotlib uproot tqdm ipykernel atlasify networkx seaborn wandb 

# Rapids AI
# cudf-cu12 dask-cudf-cu12 cuml-cu12 cugraph-cu12 cuspatial-cu12 cuproj-cu12 cuxfilter-cu12 cucim
RUN source activate $CONDA_ENV_NAME && pip install \
    --extra-index-url=https://pypi.nvidia.com \
    cudf-cu12 cugraph-cu12

ENV TORCH_CUDA_ARCH_LIST="6.1 7.0 7.5 8.0 8.6"

# # torch scatter
RUN source activate $CONDA_ENV_NAME && cd /tmp/ && mkdir src \
	&& wget https://github.com/rusty1s/pytorch_scatter/archive/refs/tags/2.1.2.tar.gz -O- | tar -xz --strip-components=1 --directory src \
	&& cd src && python setup.py install && rm -rf /tmp/src

# pyg and lightning
RUN source activate $CONDA_ENV_NAME \
  && pip install torch_geometric lightning

# # >> ROOT
# RUN source activate $CONDA_ENV_NAME && cd /tmp \
#   	&& wget https://root.cern/download/root_v6.28.06.source.tar.gz -O- | tar xz \
#   	&& mkdir root_builder && cd root_builder \
# 	&& cmake -DCMAKE_INSTALL_PREFIX=/usr/local/root ../root-6.28.06 \
# 		-Dminuit2=ON -Dgviz=ON -Drpath=ON -Dgnuinstall=OFF -DCMAKE_CXX_STANDARD=17 -Droot7=ON \
# 	&& cmake --build . --target install -- -j4 \
# 	&& cd  && rm -rf /tmp/root-6.28.06 && echo "source /usr/local/root/bin/thisroot.sh" >> ~/.bashrc
