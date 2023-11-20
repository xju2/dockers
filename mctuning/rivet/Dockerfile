# docexoty/heptools:rivet3.1.7
ARG RIVET_VERSION=3.1.7
FROM hepstore/rivet-pythia:${RIVET_VERSION}

RUN apt-get update -y && apt-get -y install libhdf5-mpich-dev vim libboost-all-dev \
  libeigen3-dev xtl-dev libxerces-c-dev libxerces-c3.2 autoconf automake nodejs doxygen \
    && rm -rf /var/lib/apt/lists/*

# python pakcages
RUN pip install --upgrade pip && \
    pip install pylint jupyterlab jax jaxlib h5py iminuit \
		vaex scikit-learn scipy numba mpi4py pyDOE2 autograd \
        tables tqdm Gpy>=1.9.9 matplotlib seaborn pandas numpy


# for rivet3.1.7 and further

# diy
RUN cd /tmp \
	&& wget https://portal.nersc.gov/project/atlas/xju/diy.tar.gz -O- | tar xz \
	&& cd diy/include && mv diy /usr/local/include \
	&& cd && rm -rf /tmp/*

# xtensor-0.21.5
RUN mkdir /code && cd /code \
	&& wget https://github.com/xtensor-stack/xtensor/archive/0.21.5.tar.gz -O- | tar xz \ 
	&& cd xtensor-0.21.5 \
	&& cmake . -DCMAKE_INSTALL_PREFIX=/usr/local \
		-DCMAKE_CXX_COMPILER=`which c++` -DCMAKE_C_COMPILER=`which cc`  \
	&& make install \
    && cd ../.. && rm -r /code

# LHE H5
RUN cd /tmp/ \
    && wget https://portal.nersc.gov/project/atlas/xju/lheh5-0.3.2.tar.gz \
    && tar xzf lheh5-0.3.2.tar.gz && cd lheh5-0.3.2 \
    && mkdir build && cd build \
    && cmake .. -DCMAKE_INSTALL_PREFIX=/usr/local \
        -DCMAKE_CXX_COMPILER=`which c++` -DCMAKE_C_COMPILER=`which cc` \
        -DHIGHFIVE_DIR=/usr/local \
    && make -j10 && make install \
    && rm -rf /tmp/*

# yodfh5
RUN mkdir /code && cd /code \
	&& git clone https://github.com/xju2/yodf5.git \
	&& cd yodf5/ && mkdir build && cd build \
	&& cmake ../src -DHDF5_INCLUDE_DIR=/usr/include/hdf5/mpich && make -j4 && make install \
	&& cd ../.. && rm -rf /code

# HEPMC2
RUN cd /tmp \
    && wget http://hepmc.web.cern.ch/hepmc/releases/hepmc2.06.11.tgz -O- | tar xz \
    && cd HepMC-2.06.11 && mkdir build && cd build \
    && cmake .. -DCMAKE_INSTALL_PREFIX=/usr/local \
		-DCMAKE_C_COMPILER=`which gcc` \
		-DCMAKE_CXX_COMPILER=`which g++` \
        -Dmomentum="GEV" -Dlength="MM" -Dbuild_docs=OFF\
	&& make -j && make install \
  && cd ../.. && rm -rf /tmp/*

# # pythia8-diy
# RUN mkdir /code && cd /code \
# 	&& wget https://portal.nersc.gov/project/atlas/xju/pythia8-diy.tar.gz -O- | tar xz \
# 	&& cd pythia8-diy && mkdir build && cd build \
# 	&& cmake .. -DHIGHFIVE_DIR=/usr/local \
# 				-DDIY_INCLUDE_DIRS=/usr/include/hdf5/mpich \
# 				-DYODA_DIR=/usr/local \
# 				-DRIVET_DIR=/usr/local \
# 				-DPYTHIA8_DIR=/usr/local \
# 				-DFASTJET_DIR=/usr/local \
# 				-DHEPMC_DIR=/usr/local \
# 				-DCMAKE_PREFIX_PATH=/usr/local/lib/cmake \
# 	&& make && make install \
# 	&& cd ../.. && rm -rf /code
