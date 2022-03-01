# docexoty/heptools:ubuntu20.04-CPU
FROM ubuntu:20.04

# ROOT core dependences.
RUN apt-get update -y && DEBIAN_FRONTEND="noninteractive" TZ="America/Los_Angeles" \
    apt-get -y install \
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

# point python to python3.
RUN rm /usr/bin/python && ln -s /usr/bin/python3.8 /usr/bin/python

ENV RIVET_ANALYSIS_PATH="/usr/local/share/RivetAnalyses"
RUN mkdir -p $RIVET_ANALYSIS_PATH
ENV PYTHONUSERBASE="/usr/local/python3.8"
ENV PATH=/usr/local/root/bin:/usr/local/python3.8/bin:$PATH
ENV LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:/usr/local/cuda/lib64:/usr/lib64/mpich/lib:/usr/local/lib
ENV PYTHONPATH=${PYTHONPATH}:/usr/local/lib/python3.8/site-packages
ENV PYTHONPATH=${PYTHONPATH}:/usr/lib/python3.8/site-packages:/usr/local/root/lib/root


RUN pip3 install --user --upgrade pip \
	&& pip install --user matplotlib seaborn \
	jupyterlab h5py numpy pandas sklearn scipy mpi4py pyDOE2 tables tqdm \
    uproot awkward nose pytest && pip install --user -U "ray[tune]"

# # >> ROOT
RUN mkdir /code && cd /code \
  	&& wget https://root.cern/download/root_v6.24.06.source.tar.gz -O- | tar xz \
  	&& mkdir root_builder && cd root_builder \
	&& cmake -DCMAKE_INSTALL_PREFIX=/usr/local/root ../root-6.24.06 \
		-Dminuit2=ON -Dgviz=ON -Drpath=ON -Dgnuinstall=OFF -DCMAKE_CXX_STANDARD=17 -Droot7=ON\
		-DCMAKE_CXX_COMPILER=`which g++` -DCMAKE_C_COMPILER=`which gcc` \
	&& cmake --build . --target install -- -j4 \
	&& cd  && rm -rf /code && echo "source /usr/local/root/bin/thisroot.sh" >> ~/.bashrc

# LHAPDF-6.2.3
RUN mkdir /code && cd /code \
    && wget https://lhapdf.hepforge.org/downloads/?f=LHAPDF-6.2.3.tar.gz -O- | tar xz \
    && cd LHAPDF-*/ && CC=`which g++` C=`which gcc` ./configure --prefix=/usr/local \
    && make -j10 && make install \
    && cd ../.. && rm -r /code

RUN lhapdf install NNPDF23_lo_as_0130_qed

# xtensor-0.21.5
RUN mkdir /code && cd /code \
	&& wget https://github.com/xtensor-stack/xtensor/archive/0.21.5.tar.gz -O- | tar xz \ 
	&& cd xtensor-0.21.5 \
	&& cmake . -DCMAKE_INSTALL_PREFIX=/usr/local \
		-DCMAKE_CXX_COMPILER=`which g++` -DCMAKE_C_COMPILER=`which gcc` \
	&& make install \
    && cd ../.. && rm -r /code

# HighFive-master
RUN mkdir /code && cd /code \
	&& git clone https://github.com/BlueBrain/HighFive.git \
	&& cd HighFive && mkdir build && cd build \
	&& cmake .. -DCMAKE_INSTALL_PREFIX=/usr/local \
		-DCMAKE_CXX_COMPILER=`which g++` -DCMAKE_C_COMPILER=`which gcc` \
		-DHIGHFIVE_EXAMPLES=OFF -DHIGHFIVE_UNIT_TESTS=OFF \
		-DHIGHFIVE_USE_XTENSOR=ON -DHIGHFIVE_USE_EIGEN=ON \
	&& make -j10 && make install \
    && cd ../.. && rm -r /code

# # rivet-3.1.0
# RUN mkdir /code && cd /code \
#     && wget https://gitlab.com/hepcedar/rivetbootstrap/raw/3.1.0/rivet-bootstrap \
#     && chmod +x rivet-bootstrap \
#     && INSTALL_PREFIX=/usr/local INSTALL_GSL=0 INSTALL_RIVETDEV=0 MAKE="make -j4" ./rivet-bootstrap \
#     && echo "source /usr/local/share/Rivet/rivet-completion" > /etc/profile.d/rivet-completion.sh \
#     && echo "source /usr/local/share/YODA/yoda-completion" > /etc/profile.d/yoda-completion.sh \
#     && rm -rf /code


# HepMC2-2.06.11
# HepMC3-3.2.1
RUN cd /tmp \
    && wget http://hepmc.web.cern.ch/hepmc/releases/hepmc2.06.11.tgz -O- | tar xz \
    && cd HepMC-2.06.11 && mkdir build && cd build \
    && cmake .. -DCMAKE_INSTALL_PREFIX=/usr/local \
		-DCMAKE_C_COMPILER=`which gcc` \
		-DCMAKE_CXX_COMPILER=`which g++` \
        -Dmomentum="GEV" -Dlength="MM" -Dbuild_docs=OFF\
	&& make -j && make install \
    && cd /tmp && wget http://hepmc.web.cern.ch/hepmc/releases/HepMC3-3.2.1.tar.gz -O- | tar xz \
    && cd /tmp/HepMC3-* && mkdir build && cd build  \
	&& cmake .. -DHEPMC3_ENABLE_ROOTIO=OFF \
		-DHEPMC3_ENABLE_PYTHON=OFF \	
		-DCMAKE_INSTALL_PREFIX=/usr/local \
		-DHEPMC3_BUILD_EXAMPLES=OFF \
		-DCMAKE_CXX_COMPILER=`which g++` -DCMAKE_C_COMPILER=`which gcc` \
	&& make -j && make install \
    && rm -rf /tmp/*


ENV PYTHIA8DATA=/usr/local/share/Pythia8/xmldoc/

# diy
RUN mkdir /code && cd /code \
	&& wget https://portal.nersc.gov/project/atlas/xju/diy.tar.gz -O- | tar xz \
	&& cd diy/include && mv diy /usr/local/include \
	&& cd && rm -rf /code

# clhep
RUN mkdir /code && cd /code \
	&& wget http://proj-clhep.web.cern.ch/proj-clhep/dist1/clhep-2.4.1.3.tgz -O- | tar xz  \
	&& cd 2.4.1.3/CLHEP && mkdir build && cd build \
	&& cmake .. -DCMAKE_INSTALL_PREFIX=/usr/local \
		-DCMAKE_CXX_COMPILER=`which g++` -DCMAKE_C_COMPILER=`which gcc` \
	&& make -j10 && make install \
	&& cd ../../.. && rm -r /code

# geant 4
RUN mkdir /code && cd /code \
	&& wget http://geant4-data.web.cern.ch/geant4-data/releases/geant4.10.06.p02.tar.gz -O- | tar xz \
	&& cd geant4.10.06.p02 && mkdir build && cd build \
	&& cmake .. -DCMAKE_INSTALL_PREFIX=/usr/local -DGEANT4_INSTALL_DATA=ON -DGEANT4_USE_GDML=ON \
				-DXERCESC_ROOT_DIR=/usr -DCLHEP_ROOT_DIR=/usr/local \
				-DCMAKE_CXX_COMPILER=`which g++` -DCMAKE_C_COMPILER=`which gcc` \
	&& make -j10 && make install \
    && cd ../.. && rm -r /code

# dd4hep 1.11.02 does not work
# https://github.com/AIDASoft/DD4hep/archive/v01-11-02.tar.gz
# 01-16-01 works!
# https://github.com/AIDASoft/DD4hep/archive/refs/tags/v01-16-01.tar.gz
RUN mkdir /code && cd /code \
 	&& wget https://github.com/AIDASoft/DD4hep/archive/refs/tags/v01-16-01.tar.gz -O- | tar xz \
	&& cd DD4hep-* && mkdir build && cd build \ 
	&& cmake .. -DDD4HEP_USE_XERCESC=ON -DDXERCESC_ROOT_DIR=/usr \
			-DDD4HEP_USE_GEANT4=ON -DGeant4_DIR=/usr/local/lib/Geant4-10.6.2 \
			-DBUILD_TESTING=OFF \
			-DCMAKE_INSTALL_PREFIX=/usr/local \
			-DROOT_DIR=/usr/local/root \
			-DDD4HEP_USE_TBB=ON -DDD4HEP_USE_HEPMC3=ON \
			-DCMAKE_CXX_COMPILER=`which g++` -DCMAKE_C_COMPILER=`which gcc` \
	&& make -j10 && make install \
    && cd ../.. && rm -r /code
	
# Tauola-1.1.8
RUN mkdir /code && cd /code \
	&& wget http://tauolapp.web.cern.ch/tauolapp/resources/TAUOLA.1.1.8/TAUOLA.1.1.8-LHC.tar.gz -O- | tar xz \
	&& cd TAUOLA && CXX=g++ ./configure --with-hepmc=/usr/local --with-hepmc3=/usr/local \
			--with-lhapdf=/usr/local --prefix=/usr/local \
	&& make -j10 && make install && cd ../.. && rm -r /code 

# Photos
RUN mkdir /code && cd /code \
	&& wget http://photospp.web.cern.ch/photospp/resources/PHOTOS.3.64/PHOTOS.3.64.tar.gz -O- | tar xz \
	&& cd PHOTOS && CXX=g++ ./configure --with-hepmc=/usr/local --with-hepmc3=/usr/local \
		--with-tauola=/usr/local --prefix=/usr/local \
	&& make -j10 && make install && cd ../.. && rm -r /code

# fastjet3
RUN mkdir /code && cd /code \
	&& wget http://fastjet.fr/repo/fastjet-3.4.0.tar.gz -O- | tar xz \
	&& cd fastjet-3.4.0 && ./configure --prefix=/usr/local \
		--enable-allplugins && make && make install

# Pythia 8.302
RUN cd /code \
    && wget https://pythia.org/download/pythia83/pythia8302.tgz -O- | tar xz \
    && cd pythia*/ && ./configure --with-fastjet3=/usr/local \
					--with-hepmc2=/usr/local --with-lhapdf6=/usr/local \
					--with-root-bin=/usr/local/root/bin --with-root-lib=/usr/local/root/lib/root \
					--with-root-include=/usr/local/root/include/root \
					--with-python=/usr/include/python3.8 --with-gzip-include=/usr/include --with-gzip-lib=/usr/lib64 \
					--with-hepmc3-include=/usr/local/include --with-hepmc3-lib=/usr/local/lib64 --cxx=g++ --prefix=/usr/local \
    && make -j10 && make install

# Tauola-1.1.8
RUN cd /code \
	&& wget http://tauolapp.web.cern.ch/tauolapp/resources/TAUOLA.1.1.8/TAUOLA.1.1.8-LHC.tar.gz -O- | tar xz \
	&& cd TAUOLA && CXX=g++ ./configure --with-hepmc=/usr/local --with-hepmc3=/usr/local \
			--with-lhapdf=/usr/local --with-pythia8=/usr/local --prefix=/usr/local \
	&& make -j10 && make install

# # EvtGen 2.0?
RUN cd /code \
	&& wget https://evtgen.hepforge.org/downloads?f=EvtGen-02.00.00.tar.gz -O- | tar xz \
	&& cd EvtGen/R02-00-00 && mkdir build && cd build \
	&& cmake .. -DCMAKE_INSTALL_PREFIX=/usr/local -DEVTGEN_PHOTOS=ON -DEVTGEN_TAUOLA=ON -DEVTGEN_PYTHIA=ON \
			-DCMAKE_CXX_COMPILER=`which g++` -DCMAKE_C_COMPILER=`which gcc` \
	&& make && make install


# Delphes 3.5.0
RUN cd /code \
	&&    wget http://cp3.irmp.ucl.ac.be/downloads/Delphes-3.5.0.tar.gz -O- | tar xz \
	&& cd Delphes-3.5.0 && mkdir build && cd build \
	&& cmake .. -DCMAKE_INSTALL_PREFIX=/usr/local \
			-DCMAKE_CXX_COMPILER=`which g++` -DCMAKE_C_COMPILER=`which gcc` \
	&& make && make install

# Ray Taskfarmer
RUN cd /code \
	&& git clone https://gitlab.cern.ch/berkeleylab/raytaskfarmer.git \
	&& cd raytaskfarmer && pip install .