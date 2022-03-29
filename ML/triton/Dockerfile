# docexoty/exatrkx:triton-rapids
ARG TRITON_VERSION=22.02-py3-sdk
FROM nvcr.io/nvidia/tritonserver:${TRITON_VERSION}

# ROOT core dependences
RUN apt-get update -y && apt-get -y install \
  libx11-dev libxpm-dev libxft-dev libxext-dev libssl-dev  \
  libeigen3-dev libboost-all-dev libtbb-dev rsync doxygen \
  && rm -rf /var/lib/apt/lists/*

ARG ROOT_VERSION=6.24.06
RUN cd /tmp && \
    wget https://root.cern/download/root_v${ROOT_VERSION}.source.tar.gz -O- | tar xz \
  	&& mkdir root_builder && cd root_builder \
	&& cmake -DCMAKE_INSTALL_PREFIX=/usr/local/root ../root-${ROOT_VERSION} \
		-Dminuit2=ON -Dgviz=ON -Drpath=ON -Dgnuinstall=OFF -DCMAKE_CXX_STANDARD=17 -Droot7=ON\
		-DCMAKE_CXX_COMPILER=`which g++` -DCMAKE_C_COMPILER=`which gcc` \
	&& cmake --build . --target install -- -j5 \
	&& cd  && rm -rf /tmp/* && \
    echo "source /usr/local/root/bin/thisroot.sh" >> ~/.bashrc

# HepMC2 and HepMC3
ARG HepMC3_VERSION=3.2.1
RUN cd /tmp \
    && wget http://hepmc.web.cern.ch/hepmc/releases/hepmc2.06.11.tgz -O- | tar xz \
    && cd HepMC-2.06.11 && mkdir build && cd build \
    && cmake .. -DCMAKE_INSTALL_PREFIX=/usr/local \
		-DCMAKE_C_COMPILER=`which gcc` \
		-DCMAKE_CXX_COMPILER=`which g++` \
        -Dmomentum="GEV" -Dlength="MM" -Dbuild_docs=OFF\
	&& make -j && make install \
    && cd /tmp && wget http://hepmc.web.cern.ch/hepmc/releases/HepMC3-${HepMC3_VERSION}.tar.gz -O- | tar xz \
    && cd /tmp/HepMC3-* && mkdir build && cd build  \
	&& cmake .. -DHEPMC3_ENABLE_ROOTIO=OFF \
		-DHEPMC3_ENABLE_PYTHON=OFF \	
		-DCMAKE_INSTALL_PREFIX=/usr/local \
		-DHEPMC3_BUILD_EXAMPLES=OFF \
		-DCMAKE_CXX_COMPILER=`which g++` -DCMAKE_C_COMPILER=`which gcc` \
	&& make -j && make install \
    && rm -rf /tmp/*

# fastjet3
RUN mkdir /code && cd /code \
	&& wget http://fastjet.fr/repo/fastjet-3.4.0.tar.gz -O- | tar xz \
	&& cd fastjet-3.4.0 && ./configure --prefix=/usr/local \
		--enable-allplugins && make && make install && rm -rf /code

# LHAPDF-6.2.3
RUN mkdir /code && cd /code \
    && wget https://lhapdf.hepforge.org/downloads/?f=LHAPDF-6.2.3.tar.gz -O- | tar xz \
    && cd LHAPDF-*/ && CC=`which g++` C=`which gcc` ./configure --prefix=/usr/local \
    && make -j10 && make install \
    && cd ../.. && rm -r /code

# RUN lhapdf install NNPDF23_lo_as_0130_qed

# Pythia 8.302
RUN mkdir /code && cd /code && \
    wget https://pythia.org/download/pythia83/pythia8302.tgz -O- | tar xz \
    && cd pythia*/ && ./configure --with-fastjet3=/usr/local \
					--with-hepmc2=/usr/local --with-lhapdf6=/usr/local \
					--with-root-bin=/usr/local/root/bin --with-root-lib=/usr/local/root/lib/root \
					--with-root-include=/usr/local/root/include/root \
					--with-python=/usr/include/python3.8 --with-gzip-include=/usr/include --with-gzip-lib=/usr/lib64 \
					--with-hepmc3-include=/usr/local/include --with-hepmc3-lib=/usr/local/lib64 --cxx=g++ --prefix=/usr/local \
    && make -j10 && make install \
    && cd ../.. && rm -r /code

ENV PYTHIA8DATA=/usr/local/share/Pythia8/xmldoc/

# ARG PYTORCH_VERSION=1.11.0 
# ARG CUDANAME=cu115

# RUN /usr/bin/python3 -m pip install --upgrade pip && pip install torch==${PYTORCH_VERSION}+${CUDANAME} \
#     -f https://download.pytorch.org/whl/${CUDANAME}/torch_stable.html

# RUN cd /tmp && \
#     git clone --recursive https://github.com/pytorch/kineto.git && \
#     cd kineto/libkineto &&  mkdir build && cd build && cmake .. \
#         -DCMAKE_C_COMPILER=`which gcc` \
#         -DCMAKE_CXX_COMPILER=`which g++` \
#         -DCMAKE_INSTALL_PREFIX=/usr/local && \
#     make -j4 && make install && rm -rf /tmp/*

COPY deps.tar.gz /usr/local 
RUN cd /usr/local && tar xzf deps.tar.gz && rm deps.tar.gz

RUN apt-get update -y && apt-get install -y \
    libhdf5-mpich-dev libomp-dev libmkl-full-dev ccache && \
	rm -rf /var/lib/apt/lists/* && \
	pip install Sphinx mkl-devel typing_extensions ninja

RUN /usr/sbin/update-ccache-symlinks
RUN mkdir /opt/ccache && ccache --set-config=cache_dir=/opt/ccache

RUN mkdir -p /opt/hpcx/ompi/lib/x86_64-linux-gnu && \
    cd /opt/hpcx/ompi/lib/x86_64-linux-gnu && \
	ln -s /opt/hpcx/ompi openmpi

RUN cd /tmp && \
	git clone --recursive https://github.com/pytorch/pytorch.git && cd pytorch && \
	git checkout -b r1.11 origin/release/1.11 && \
	git submodule sync && git submodule update --init --recursive --jobs 0 && \
	USE_CUDA=1 USE_OPENMP=1 USE_MPI=1 USE_TBB=1 USE_SYSTEM_TBB=1 USE_MKLDNN=1 \
	MKL_THREADING=TBB MKLDNN_CPU_RUNTIME=TBB BUILD_BINARY=1 BUILD_CUSTOM_PROTOBUF=OFF \
	CMAKE_PREFIX_PATH=/workspace/build/third-party/protobuf python setup.py install --prefix=/usr/local && \
	rm -rf /tmp/*

ENV PYTHONPATH=${PYTHONPATH}:/usr/local/lib/python3.8/site-packages