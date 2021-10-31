# docexoty/exatrkx:tf2.5-torch1.9-cuda11.2-ubuntu20.04-rapids21.10-devel-hep
FROM docexoty/exatrkx:tf2.5-torch1.9-cuda11.2-ubuntu20.04-rapids21.10-py3.8-devel

RUN apt-get update -y && source activate rapids && apt-get -y install \
	dpkg-dev binutils libxpm-dev \
	libxft-dev libssl-dev \
	gfortran libpcre3-dev \
	xlibmesa-glu-dev libglew-dev libftgl-dev \
	libmysqlclient-dev libfftw3-dev \
	graphviz-dev \
	libldap2-dev libxml2-dev libkrb5-dev \
	libgsl0-dev libboost-all-dev \
	&& rm -rf /var/lib/apt/lists/*

RUN cd /rapids && source activate rapids \
  	&& wget https://root.cern/download/root_v6.20.08.source.tar.gz -O- | tar xz \
  	&& mkdir root_builder && cd root_builder \
	&& cmake ../root-6.20.08 -DCMAKE_INSTALL_PREFIX=${CONDA_PREFIX} \
		-Dminuit2=ON -Dgviz=ON -Drpath=ON -Dgnuinstall=ON -Dcxx17=ON \
		-DCMAKE_C_COMPILER=/usr/local/bin/gcc -DCMAKE_CXX_COMPILER=/usr/local/bin/g++ \
	&& cmake --build . --target install -- -j4

RUN cd /tmp && source activate rapids \
    && wget https://lhapdf.hepforge.org/downloads/?f=LHAPDF-6.2.3.tar.gz -O- | tar xz \
    && cd LHAPDF-6.2.3/ && CC=/usr/local/bin/gcc CXX=/usr/local/bin/g++ ./configure --prefix=${CONDA_PREFIX} \
    && make -j4 && make install && cd / && rm -r /tmp/*

# RUN ln -s /lib/x86_64-linux-gnu/libpthread.so.0 /lib64/ && \
#     ln -s /lib/x86_64-linux-gnu/libpthread.a  /usr/lib64/libpthread_nonshared.a && \
#     ln -s /lib/x86_64-linux-gnu/libc.a /usr/lib64/libc_nonshared.a && \
#     ln -s /lib/x86_64-linux-gnu/libc.so.6 /lib64/libc.so.6

# HepMC2-2.06.11
# HepMC3-3.2.1
RUN cd /tmp && source activate rapids \
    && wget http://hepmc.web.cern.ch/hepmc/releases/hepmc2.06.11.tgz -O- | tar xz \
    && cd HepMC-2.06.11 && mkdir build && cd build \
    && cmake .. -DCMAKE_INSTALL_PREFIX=${CONDA_PREFIX} \
		-DCMAKE_C_COMPILER=/usr/local/bin/gcc \
		-DCMAKE_CXX_COMPILER=/usr/local/bin/g++ \
        -Dmomentum="GEV" -Dlength="MM" -Dbuild_docs=OFF\
	&& make -j && make install \
    && cd /tmp && wget http://hepmc.web.cern.ch/hepmc/releases/HepMC3-3.2.1.tar.gz -O- | tar xz \
    && cd /tmp/HepMC3-* && mkdir build && cd build  \
    && cmake .. -DHEPMC3_ENABLE_ROOTIO=OFF \
        -DHEPMC3_ENABLE_PYTHON=OFF \	
        -DCMAKE_INSTALL_PREFIX=${CONDA_PREFIX} \
        -DHEPMC3_BUILD_EXAMPLES=OFF \
		-DCMAKE_C_COMPILER=/usr/local/bin/gcc \
		-DCMAKE_CXX_COMPILER=/usr/local/bin/g++ \
	&& make -j && make install \
    && rm -rf /tmp/*


# clhep
RUN cd /tmp && source activate rapids \
	&& wget http://proj-clhep.web.cern.ch/proj-clhep/dist1/clhep-2.4.1.3.tgz -O- | tar xz  \
	&& cd 2.4.1.3/CLHEP && mkdir build && cd build \
	&& cmake .. \
        -DCMAKE_INSTALL_PREFIX=${CONDA_PREFIX} \
		-DCMAKE_C_COMPILER=/usr/local/bin/gcc \
		-DCMAKE_CXX_COMPILER=/usr/local/bin/g++ \
	&& make -j4 && make install \
	&& rm -rf /tmp/*

# geant 4
RUN cd /tmp && source activate rapids \
	&& wget http://geant4-data.web.cern.ch/geant4-data/releases/geant4.10.06.p02.tar.gz -O- | tar xz \
	&& cd geant4.10.06.p02 && mkdir build && cd build \
	&& cmake .. -DGEANT4_INSTALL_DATA=ON -DGEANT4_USE_GDML=ON \
				-DXERCESC_ROOT_DIR=${CONDA_PREFIX} -DCLHEP_ROOT_DIR=${CONDA_PREFIX} \
                -DCMAKE_INSTALL_PREFIX=${CONDA_PREFIX} \
				-DCMAKE_C_COMPILER=/usr/local/bin/gcc \
				-DCMAKE_CXX_COMPILER=/usr/local/bin/g++ \
	&& make -j4 && make install \
    && rm -rf /tmp/*

ENV G4DATA=/opt/conda/envs/rapids/share/Geant4-10.6.2/data \
    G4ENSDFSTATEDATA=${G4DATA}/G4ENSDFSTATE2.2 \
    G4LEVELGAMMADATA=${G4DATA}/PhotonEvaporation5.5 \
    G4PARTICLEXSDATA=${G4DATA}/G4PARTICLEXS2.1 \
    G4RADIOACTIVEDATA=${G4DATA}/RadioactiveDecay5.4 \
    G4LEDATA=${G4DATA}/G4EMLOW7.9.1 \
    G4NEUTRONHPDATA=${G4DATA}/G4NDL4.6

# dd4hep 1.11.02 does not work
# https://github.com/AIDASoft/DD4hep/archive/v01-11-02.tar.gz
# 01-16-01 requires python exactly 3.8.12, hack the cmake
# https://github.com/AIDASoft/DD4hep/archive/refs/tags/v01-16-01.tar.gz

RUN apt-get update -y && apt-get install libtbb2 libtbb-dev

# TBB is completely messed up. 
# rapidsai contains one, ROOT installed one, and I just install another one above
# the code use find_packages(TBB required config), which did not find proper tbb config.
RUN cd /tmp && source activate rapids \
 	&& wget https://github.com/AIDASoft/DD4hep/archive/refs/tags/v01-16-01.tar.gz -O- | tar xz \
	&& cd DD4hep-* && sed -i 's/EXACT//g' cmake/DD4hepBuild.cmake  && mkdir build && cd build \ 
    && cmake .. -DDD4HEP_USE_XERCESC=ON -DDXERCESC_ROOT_DIR=${CONDA_PREFIX} \
			-DDD4HEP_USE_GEANT4=ON -DGeant4_DIR=${CONDA_PREFIX} \
			-DBUILD_TESTING=OFF \
			-DROOT_DIR=/opt/conda/envs/rapids \
			-DDD4HEP_USE_TBB=ON -DDD4HEP_USE_HEPMC3=ON \
           -DCMAKE_INSTALL_PREFIX=${CONDA_PREFIX} \
			-DCMAKE_C_COMPILER=/usr/local/bin/gcc \
			-DCMAKE_CXX_COMPILER=/usr/local/bin/g++ \
	&& make -j4 && make install \
    && rm -rf /tmp/*

# Fastjet 3.4.0
RUN cd /tmp && source activate rapids \
    && wget http://fastjet.fr/repo/fastjet-3.4.0.tar.gz -O- | tar xz \
    && cd fastjet-3.4.0 && CC=/usr/local/bin/gcc CXX=/usr/local/bin/g++ ./configure --prefix=${CONDA_PREFIX} \
    && make -j4 && make install \
    && rm -rf /tmp/*

RUN apt-get install -y rsync

# Pythia 8.302
RUN cd /tmp && source activate rapids \
    && wget https://pythia.org/download/pythia83/pythia8302.tgz -O- | tar xz \
    && cd pythia*/ \
    && ./configure --with-fastjet3=${CONDA_PREFIX} \
           --with-hepmc2=${CONDA_PREFIX} --with-lhapdf6=${CONDA_PREFIX} \
           --with-root-bin=${CONDA_PREFIX}/root/bin --with-root-lib=${CONDA_PREFIX}/root/lib/root \
           --with-root-include=${CONDA_PREFIX}/root/include/root \
           --with-gzip-include=/usr/include --with-gzip-lib=/usr/lib64 \
           --with-hepmc3-include=${CONDA_PREFIX}/include \
           --with-hepmc3-lib=${CONDA_PREFIX}/lib64 --cxx=/usr/local/bin/g++ \
           --prefix=${CONDA_PREFIX} \
    && make -j4 && make install \
    && rm -rf /tmp/*