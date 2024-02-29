# syntax=docker/dockerfile:1
# docexoty/llm:latest
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

# Install Python 3.9
RUN apt-get update -y && apt install -y software-properties-common && add-apt-repository ppa:deadsnakes/ppa \
    && apt-get update -y && apt install -y python3.9 python3.9-dev

# Other dependences
RUN apt-get update -y  && apt-get -y install \
	git libeigen3-dev xtl-dev libhdf5-mpich-dev \
	libxerces-c-dev libxerces-c3.2 rsync autoconf automake nodejs doxygen \
	valgrind

# point python to python3.9
RUN rm /usr/bin/python && ln -s /usr/bin/python3.9 /usr/bin/python \
    && wget https://bootstrap.pypa.io/get-pip.py && python get-pip.py \
    && ln -s /usr/local/bin/pip /usr/bin/pip && pip install --user --upgrade pip \
    && rm get-pip.py


ARG ROOT_VERSION=6.28.12
RUN cd /tmp \
  	&& wget https://root.cern/download/root_v${ROOT_VERSION}.source.tar.gz -O- | tar xz \
  	&& mkdir root_builder && cd root_builder \
	&& cmake -DCMAKE_INSTALL_PREFIX=/usr/local/root ../root-${ROOT_VERSION} \
		-Dminuit2=ON -Dgviz=ON -Drpath=ON -Dgnuinstall=OFF -DCMAKE_CXX_STANDARD=17 -Droot7=ON -Dxrootd=OFF -DPYTHON_EXECUTABLE=`which python` \
	&& cmake --build . --target install -- -j4 \
	&& cd  && rm -rf /tmp/root-* && echo "source /usr/local/root/bin/thisroot.sh" >> ~/.bashrc

# install python packages
RUN pip install hydra-core hydra-colorlog lightning torch numpy transformers datasets tiktoken wandb tqdm pyrootutils jupyterlab tensorboard wandb ruff pre-commit
RUN pip install matplotlib seaborn
