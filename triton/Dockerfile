# docexoty/exasvc:ubuntu20.04
FROM nvidia/cuda:11.3.1-cudnn8-devel-ubuntu20.04

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
    python3-dev python3-pip python3-sphinx python3-numpy-dev \
	git libeigen3-dev xtl-dev libhdf5-mpich-dev \
	libxerces-c-dev libxerces-c3.2 rsync autoconf automake nodejs doxygen \
	valgrind  && \
    rm -rf /var/lib/apt/lists/*


# point python to python3.
RUN rm /usr/bin/python && ln -s /usr/bin/python3.8 /usr/bin/python

ENV PYTHONUSERBASE="/usr/local/python3.8"
ENV PATH=/usr/local/root/bin:/usr/local/python3.8/bin:$PATH
ENV LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:/usr/local/cuda/lib64:/usr/lib64/mpich/lib:/usr/local/lib
ENV PYTHONPATH=${PYTHONPATH}:/usr/local/lib/python3.8/site-packages
ENV PYTHONPATH=${PYTHONPATH}:/usr/lib/python3.8/site-packages:/usr/local/root/lib/root


ARG PYTORCH_VERSION=1.11.0
ARG CUDANAME=cu113
RUN pip install --user torch==${PYTORCH_VERSION}+${CUDANAME} \
    torchvision==0.12.0+${CUDANAME} \
    torchaudio===0.11.0 -f https://download.pytorch.org/whl/cu113/torch_stable.html 

# # >> ROOT
RUN cd /tmp && \
    wget https://root.cern/download/root_v6.26.00.source.tar.gz -O- | tar xz \
  	&& mkdir root_builder && cd root_builder \
	&& cmake -DCMAKE_INSTALL_PREFIX=/usr/local/root ../root-6.26.00 \
		-Dminuit2=ON -Dgviz=ON -Drpath=ON -Dgnuinstall=OFF -DCMAKE_CXX_STANDARD=17 -Droot7=ON\
		-DCMAKE_CXX_COMPILER=`which g++` -DCMAKE_C_COMPILER=`which gcc` \
	&& cmake --build . --target install -- -j4 \
	&& cd  && rm -rf /tmp/* && echo "source /usr/local/root/bin/thisroot.sh" >> ~/.bashrc

    # && pip install --user torch-scatter -f https://pytorch-geometric.com/whl/torch-${PYTORCH_VERSION}+${CUDANAME}.html \
    # && pip install --user torch-sparse  -f https://pytorch-geometric.com/whl/torch-${PYTORCH_VERSION}+${CUDANAME}.html \
    # && pip install --user torch-cluster -f https://pytorch-geometric.com/whl/torch-${PYTORCH_VERSION}+${CUDANAME}.html \
    # && pip install --user torch-spline-conv -f https://pytorch-geometric.com/whl/torch-${PYTORCH_VERSION}+${CUDANAME}.html \
    # && pip install --user torch-geometric \
    # && pip install --user tensorflow_gpu==2.5 \
	# && pip install --user pytorch-lightning \
    # 		git+https://github.com/LAL/trackml-library.git \
    # 		git+https://github.com/deepmind/graph_nets.git \
    # 		matplotlib sklearn pyyaml>=5.1 tqdm networkx pytest onnx \
	# && pip install --user -U tf2onnx \
    # && pip install --user faiss-gpu