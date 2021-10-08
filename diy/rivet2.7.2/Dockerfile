# docexoty/diy:ubuntu20.04
ARG RIVET_VERSION=2.7.2

FROM hepstore/rivet:${RIVET_VERSION}
# decided to fall back to the ubuntu 20.04.

# RUN dnf update -y && dnf -y install \
#     autoconf automake make gcc gcc-c++ gcc-gfortran redhat-rpm-config \
# 	wget tar less bzip2 findutils which zlib-devel \
# 	python python-devel python-requests python-matplotlib  python3-sphinx  \
# 	ghostscript ImageMagick  \
# 	cmake cmake3 git vim libtool rsync gzip pip patchelf \
# 	eigen3-devel xtl-devel boost boost-devel hdf5-mpich-devel \
# 	doxygen xerces-c expat-devel xerces-c-devel subversion\
# 	tbb tbb-devel  \
# 	binutils libX11-devel libXpm-devel libXft-devel libXext-devel \
# 	mysql-devel fftw-devel cfitsio-devel graphviz-devel \
# 	openldap-devel pcre-devel \
# 	file gsl-devel libxml2-devel libuuid-devel nodejs \
# 	mesa-libGL-devel mesa-libGLU-devel ftgl-devel glew-devel openssl-devel \
# 	&& dnf clean all


# # setup python
# # point python to python3.
# RUN rm /usr/bin/python && ln -s /usr/bin/python3.8 /usr/bin/python

# ENV RIVET_ANALYSIS_PATH="/usr/local/share/RivetAnalyses"
# RUN mkdir -p $RIVET_ANALYSIS_PATH
# ENV PYTHONUSERBASE="/usr/local/python3.8"
# ENV PATH=/usr/local/root/bin:/usr/local/python3.8/bin:$PATH
# ENV LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:/usr/lib64/mpich/lib:/usr/local/lib
# ENV PYTHONPATH=${PYTHONPATH}:/usr/local/lib/python3.8/site-packages
# ENV PYTHONPATH=${PYTHONPATH}:/usr/lib/python3.8/site-packages

# RUN pip install --user \
#         numpy==1.19 six==1.15.0 h5py==2.10 matplotlib seaborn \
#         pylint jax jaxlib iminuit cython\
#         sklearn scipy numba mpi4py pyDOE2 autograd tables tqdm Gpy>=1.9.9

# # install rivet-2.7.2
# not working here...
# decided to move on to 
# RUN cd /tmp \
#     && wget https://gitlab.com/hepcedar/rivetbootstrap/raw/2.7.2/rivet-bootstrap \
#     && chmod +x rivet-bootstrap \
#     && INSTALL_PREFIX=/usr/local INSTALL_GSL=0 INSTALL_RIVETDEV=0 MAKE="make -j4" ./rivet-bootstrap \
#     && echo "source /usr/local/share/Rivet/rivet-completion" > /etc/profile.d/rivet-completion.sh \
#     && echo "source /usr/local/share/YODA/yoda-completion" > /etc/profile.d/yoda-completion.sh \
#     && rm -rf /tmp/*


# # diy
# RUN cd /tmp \
# 	&& wget https://portal.nersc.gov/project/atlas/xju/diy.tar.gz -O- | tar xz \
# 	&& cd diy/include && mv diy /usr/local/include \
# 	&& cd && rm -rf /tmp/*

# # LHAPDF-6.2.3
# RUN mkdir /code && cd /code \
#     && wget https://lhapdf.hepforge.org/downloads/?f=LHAPDF-6.2.3.tar.gz -O- | tar xz \
#     && cd LHAPDF-*/ && ./configure --prefix=/usr/local \
#     && make -j10 && make install \
#     && cd ../.. && rm -r /code

# RUN lhapdf install NNPDF23_lo_as_0130_qed

# # HepMC3-3.2.1
# RUN mkdir /code && cd /code \
# 	&& wget http://hepmc.web.cern.ch/hepmc/releases/HepMC3-3.2.1.tar.gz -O- | tar xz \
# 	&& cd HepMC3-* && mkdir build && cd build \	
# 	&& cmake .. -DHEPMC3_ENABLE_ROOTIO=OFF \
# 		-DHEPMC3_ENABLE_PYTHON=OFF \	
# 		-DCMAKE_INSTALL_PREFIX=/usr/local \
# 		-DHEPMC3_BUILD_EXAMPLES=OFF \
# 		-DCMAKE_C_COMPILER=`which cc` \
# 		-DCMAKE_CXX_COMPILER=`which c++` \
# 	&& make -j10 && make install \
#     && cd ../../ && rm -r /code

# # Pythia 8.302
# RUN mkdir /code && cd /code \
#     && wget https://pythia.org/download/pythia83/pythia8302.tgz -O- | tar xz \
#     && cd pythia*/ && ./configure --with-fastjet3=/usr/local \
# 					--with-hepmc2=/usr/local --with-lhapdf6=/usr/local \
# 					--with-python=/usr/include/python3.8 \
#                     --with-gzip-include=/usr/include --with-gzip-lib=/usr/lib64 \
# 					--with-hepmc3-include=/usr/local/include \
#                     --with-hepmc3-lib=/usr/local/lib64 --cxx=c++ \
#                     --prefix=/usr/local \
#     && make -j10 && make install \
#     && cd ../.. && rm -r /code

# # xtensor-0.21.5
# RUN mkdir /code && cd /code \
# 	&& wget https://github.com/xtensor-stack/xtensor/archive/0.21.5.tar.gz -O- | tar xz \ 
# 	&& cd xtensor-0.21.5 \
# 	&& cmake . -DCMAKE_INSTALL_PREFIX=/usr/local \
# 		-DCMAKE_CXX_COMPILER=`which c++` -DCMAKE_C_COMPILER=`which cc`  \
# 	&& make install \
#     && cd ../.. && rm -r /code

# RUN apt-get update -y \
#     && apt-get -y install libboost-all-dev && rm -rf /var/lib/apt/lists/*

# # HighFive-master
# RUN mkdir /code && cd /code \
# 	&& git clone https://github.com/BlueBrain/HighFive.git \
# 	&& cd HighFive && mkdir build && cd build \
# 	&& cmake .. -DCMAKE_INSTALL_PREFIX=/usr/local \
# 		-DCMAKE_CXX_COMPILER=`which c++` -DCMAKE_C_COMPILER=`which cc` \
# 		-DHIGHFIVE_EXAMPLES=OFF -DHIGHFIVE_UNIT_TESTS=OFF \
# 		-DHIGHFIVE_USE_XTENSOR=ON -DHIGHFIVE_USE_EIGEN=ON \
# 	&& make -j10 && make install \
#     && cd ../.. && rm -r /code

# # LHE H5
# RUN cd /tmp/ \
#     && wget https://portal.nersc.gov/project/atlas/xju/lheh5-0.3.2.tar.gz \
#     && tar xzf lheh5-0.3.2.tar.gz && cd lheh5-0.3.2 \
#     && mkdir build && cd build \
#     && cmake .. -DCMAKE_INSTALL_PREFIX=/usr/local \
#         -DCMAKE_CXX_COMPILER=`which c++` -DCMAKE_C_COMPILER=`which cc` \
#         -DHIGHFIVE_DIR=/usr/local \
#     && make -j10 && make install \
#     && rm -rf /tmp/*

