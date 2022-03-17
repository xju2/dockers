# docexoty/mctuning:ubuntu20.04
FROM ubuntu:20.04

RUN apt-get update -y \
    && DEBIAN_FRONTEND="noninteractive" TZ="America/Los_Angeles" \
    apt-get -y install \
	dpkg-dev cmake g++ gcc binutils libx11-dev libxpm-dev \
	libxft-dev libxext-dev python libssl-dev \
    python3-dev python3-pip python3-sphinx \
	git libeigen3-dev xtl-dev libhdf5-mpich-dev \
	libxerces-c-dev libxerces-c3.2 rsync autoconf automake nodejs doxygen \
	valgrind vim wget \
    && rm -rf /var/lib/apt/lists/*


# point python to python3.
RUN rm /usr/bin/python && ln -s /usr/bin/python3.8 /usr/bin/python

ENV RIVET_ANALYSIS_PATH="/usr/local/share/RivetAnalyses"
RUN mkdir -p $RIVET_ANALYSIS_PATH
ENV PYTHONUSERBASE="/usr/local/python3.8"
ENV PATH=/usr/local/root/bin:/usr/local/python3.8/bin:$PATH
ENV LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:/usr/local/cuda/lib64:/usr/lib64/mpich/lib:/usr/local/lib
ENV PYTHONPATH=${PYTHONPATH}:/usr/local/lib/python3.8/site-packages
ENV PYTHONPATH=${PYTHONPATH}:/usr/lib/python3.8/site-packages:/usr/local/root/lib/root


# install rivet-3.1.0
RUN cd /tmp \
    && wget https://gitlab.com/hepcedar/rivetbootstrap/raw/3.1.0/rivet-bootstrap \
    && chmod +x rivet-bootstrap \
    && INSTALL_PREFIX=/usr/local INSTALL_GSL=0 INSTALL_RIVETDEV=0 MAKE="make -j4" ./rivet-bootstrap \
    && echo "source /usr/local/share/Rivet/rivet-completion" > /etc/profile.d/rivet-completion.sh \
    && echo "source /usr/local/share/YODA/yoda-completion" > /etc/profile.d/yoda-completion.sh \
    && rm -rf /tmp/*

RUN pip install --user \
        numpy==1.19 six==1.15.0 h5py==2.10 matplotlib seaborn \
        pylint jax jaxlib iminuit cython\
        sklearn scipy numba mpi4py pyDOE2 autograd tables tqdm Gpy>=1.9.9

RUN apt update && apt install -y texlive-full