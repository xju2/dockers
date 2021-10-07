# docexoty/diy:ubuntu20.04
FROM docexoty/mctuning:ubuntu20.04

# diy
RUN cd /tmp \
	&& wget https://portal.nersc.gov/project/atlas/xju/diy.tar.gz -O- | tar xz \
	&& cd diy/include && mv diy /usr/local/include \
	&& cd && rm -rf /tmp/*

# LHAPDF-6.2.3
RUN mkdir /code && cd /code \
    && wget https://lhapdf.hepforge.org/downloads/?f=LHAPDF-6.2.3.tar.gz -O- | tar xz \
    && cd LHAPDF-*/ && ./configure --prefix=/usr/local \
    && make -j10 && make install \
    && cd ../.. && rm -r /code

RUN lhapdf install NNPDF23_lo_as_0130_qed

# HepMC3-3.2.1
RUN mkdir /code && cd /code \
	&& wget http://hepmc.web.cern.ch/hepmc/releases/HepMC3-3.2.1.tar.gz -O- | tar xz \
	&& cd HepMC3-* && mkdir build && cd build \	
	&& cmake .. -DHEPMC3_ENABLE_ROOTIO=OFF \
		-DHEPMC3_ENABLE_PYTHON=OFF \	
		-DCMAKE_INSTALL_PREFIX=/usr/local \
		-DHEPMC3_BUILD_EXAMPLES=OFF \
		-DCMAKE_C_COMPILER=`which cc` \
		-DCMAKE_CXX_COMPILER=`which c++` \
	&& make -j10 && make install \
    && cd ../../ && rm -r /code

# Pythia 8.302
RUN mkdir /code && cd /code \
    && wget https://pythia.org/download/pythia83/pythia8302.tgz -O- | tar xz \
    && cd pythia*/ && ./configure --with-fastjet3=/usr/local \
					--with-hepmc2=/usr/local --with-lhapdf6=/usr/local \
					--with-python=/usr/include/python3.8 \
                    --with-gzip-include=/usr/include --with-gzip-lib=/usr/lib64 \
					--with-hepmc3-include=/usr/local/include \
                    --with-hepmc3-lib=/usr/local/lib64 --cxx=c++ \
                    --prefix=/usr/local \
    && make -j10 && make install \
    && cd ../.. && rm -r /code