# docexoty/exatrkx:tf2.5-torch1.9-cuda11.2-ubuntu20.04-rapids21.10-devel-hep
FROM docexoty/exatrkx:tf2.5-torch1.9-cuda11.2-ubuntu20.04-rapids21.10-py3.8-devel

RUN . /opt/conda/etc/profile.d/conda.sh && conda activate rapids \
    && mamba install -y -c conda-forge -c omnia tbb-devel=2020.2 eigen3 root

ENV INSTALL_PREFIX=/opt/conda/envs/rapids

RUN ln -s /lib/x86_64-linux-gnu/libpthread.so.0 /lib64/ && \
    ln -s /lib/x86_64-linux-gnu/libpthread.a  /usr/lib64/libpthread_nonshared.a && \
    ln -s /lib/x86_64-linux-gnu/libc.a /usr/lib64/libc_nonshared.a && \
    ln -s /lib/x86_64-linux-gnu/libc.so.6 /lib64/libc.so.6

RUN mkdir /code && cd /code \
    && . /opt/conda/etc/profile.d/conda.sh && conda activate rapids \
    && wget https://lhapdf.hepforge.org/downloads/?f=LHAPDF-6.2.3.tar.gz -O- | tar xz \
    && cd LHAPDF-6.2.3/ && ./configure --prefix=${INSTALL_PREFIX} \
    && make -j && make install \
    && cd ../.. && rm -r /code

# HepMC2-2.06.11
# HepMC3-3.2.1
RUN cd /tmp && . /opt/conda/etc/profile.d/conda.sh && conda activate rapids \
    && wget http://hepmc.web.cern.ch/hepmc/releases/hepmc2.06.11.tgz -O- | tar xz \
    && cd HepMC-2.06.11 && mkdir build && cd build \
    && cmake .. -DCMAKE_INSTALL_PREFIX=${INSTALL_PREFIX} \
		-DCMAKE_C_COMPILER=`which gcc` \
		-DCMAKE_CXX_COMPILER=`which g++` \
        -Dmomentum="GEV" -Dlength="MM" -Dbuild_docs=OFF\
	&& make -j && make install \
    && cd /tmp && wget http://hepmc.web.cern.ch/hepmc/releases/HepMC3-3.2.1.tar.gz -O- | tar xz \
    && cd /tmp/HepMC3-* && mkdir build && cd build  \
    && cmake .. -DHEPMC3_ENABLE_ROOTIO=OFF \
        -DHEPMC3_ENABLE_PYTHON=OFF \	
        -DCMAKE_INSTALL_PREFIX=${INSTALL_PREFIX} \
        -DHEPMC3_BUILD_EXAMPLES=OFF \
        -DCMAKE_C_COMPILER=`which gcc` \
        -DCMAKE_CXX_COMPILER=`which g++` \
	&& make -j && make install \
    && rm -rf /tmp/*


# clhep
RUN cd /tmp && . /opt/conda/etc/profile.d/conda.sh && conda activate rapids \
	&& wget http://proj-clhep.web.cern.ch/proj-clhep/dist1/clhep-2.4.1.3.tgz -O- | tar xz  \
	&& cd 2.4.1.3/CLHEP && mkdir build && cd build \
	&& cmake .. \
        -DCMAKE_INSTALL_PREFIX=${INSTALL_PREFIX} \
        -DCMAKE_C_COMPILER=`which gcc` \
        -DCMAKE_CXX_COMPILER=`which g++` \
	&& make -j4 && make install \
	&& rm -rf /tmp/*

# geant 4
RUN cd /tmp && . /opt/conda/etc/profile.d/conda.sh && conda activate rapids \
	&& wget http://geant4-data.web.cern.ch/geant4-data/releases/geant4.10.06.p02.tar.gz -O- | tar xz \
	&& cd geant4.10.06.p02 && mkdir build && cd build \
	&& cmake .. -DGEANT4_INSTALL_DATA=ON -DGEANT4_USE_GDML=ON \
				-DXERCESC_ROOT_DIR=${INSTALL_PREFIX} -DCLHEP_ROOT_DIR=${INSTALL_PREFIX} \
                -DCMAKE_INSTALL_PREFIX=${INSTALL_PREFIX} \
                -DCMAKE_C_COMPILER=`which gcc` \
                -DCMAKE_CXX_COMPILER=`which g++` \
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

RUN cd /tmp && . /opt/conda/etc/profile.d/conda.sh && conda activate rapids \
    && mamba install -y mesa-libgl-cos6-x86_64 libselinux-devel-cos6-x86_64 xorg-libxdamage libxxf86vm-devel-cos6-x86_64 \
    && export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/opt/conda/envs/rapids/x86_64-conda-linux-gnu/sysroot/usr/lib64 \
 	&& wget https://github.com/AIDASoft/DD4hep/archive/refs/tags/v01-16-01.tar.gz -O- | tar xz \
	&& cd DD4hep-* && sed -i 's/EXACT//g' cmake/DD4hepBuild.cmake  && mkdir build && cd build \ 
    && cmake .. -DDD4HEP_USE_XERCESC=ON -DDXERCESC_ROOT_DIR=${INSTALL_PREFIX} \
			-DDD4HEP_USE_GEANT4=ON -DGeant4_DIR=${INSTALL_PREFIX} \
			-DBUILD_TESTING=OFF \
			-DROOT_DIR=/opt/conda/envs/rapids \
			-DDD4HEP_USE_TBB=ON -DDD4HEP_USE_HEPMC3=ON \
            -DCMAKE_INSTALL_PREFIX=${INSTALL_PREFIX} \
            -DCMAKE_C_COMPILER=`which gcc` \
            -DCMAKE_CXX_COMPILER=`which g++` \
	&& make -j4 && make install \
    && rm -rf /tmp/*


# Fastjet 3.4.0
RUN cd /tmp && . /opt/conda/etc/profile.d/conda.sh && conda activate rapids \
    && wget http://fastjet.fr/repo/fastjet-3.4.0.tar.gz -O- | tar xz \
    && cd fastjet-3.4.0 && ./configure --prefix=${INSTALL_PREFIX} \
    && make -j4 && make install \
    && rm -rf /tmp/*


RUN rm /usr/lib/gcc/x86_64-linux-gnu/9/libstdc++.so \
    && ln -s /opt/conda/envs/rapids/lib/libstdc++.so.6.0.28 /usr/lib/gcc/x86_64-linux-gnu/9/libstdc++.so


# # Pythia 8.302
# RUN cd /tmp && . /opt/conda/etc/profile.d/conda.sh && conda activate rapids \
#     && wget https://pythia.org/download/pythia83/pythia8302.tgz -O- | tar xz \
#     && cd pythia*/ \
#     && ./configure --with-fastjet3=${INSTALL_PREFIX} \
#            --with-hepmc2=${INSTALL_PREFIX} --with-lhapdf6=${INSTALL_PREFIX} \
#            --with-root-bin=${INSTALL_PREFIX}/root/bin --with-root-lib=${INSTALL_PREFIX}/root/lib/root \
#            --with-root-include=${INSTALL_PREFIX}/root/include/root \
#            --with-gzip-include=/usr/include --with-gzip-lib=/usr/lib64 \
#            --with-hepmc3-include=${INSTALL_PREFIX}/include \
#            --with-hepmc3-lib=${INSTALL_PREFIX}/lib64 --cxx=g++ \
#            --prefix=${INSTALL_PREFIX} \
#     && make -j4 && make install \
#     && rm -rf /tmp/*