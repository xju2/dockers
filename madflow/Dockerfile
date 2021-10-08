# docexoty/madflow:ubuntu20.04
ARG CUDA_TAG=11.0.3-cudnn8-runtime-ubuntu20.04
FROM nvidia/cuda:${CUDA_TAG}

RUN apt-get update -y && DEBIAN_FRONTEND="noninteractive" TZ="America/Los_Angeles"\
    apt-get -y install git wget vim rsync autoconf gfortran \
    automake python3-dev python3-pip python3-sphinx 

# point python to python3.
RUN ln -s /usr/bin/python3.8 /usr/bin/python

ENV PYTHONUSERBASE="/usr/local/python3.8"
ENV PATH=/usr/local/root/bin:/usr/local/python3.8/bin:$PATH
ENV LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:/usr/local/cuda/lib64:/usr/lib64/mpich/lib:/usr/local/lib
ENV PYTHONPATH=${PYTHONPATH}:/usr/local/lib/python3.8/site-packages
ENV PYTHONPATH=${PYTHONPATH}:/usr/lib/python3.8/site-packages:/usr/local/root/lib/root

# numpy needed for ROOT
RUN pip3 install --user --upgrade pip \
    && pip install --user tensorflow==2.4 tensorboard==2.4


# LHAPDF-6.2.3
RUN mkdir /code && cd /code \
    && wget https://lhapdf.hepforge.org/downloads/?f=LHAPDF-6.2.3.tar.gz -O- | tar xz \
    && cd LHAPDF-*/ && ./configure --prefix=/usr/local \
    && make -j10 && make install \
    && cd ../.. && rm -r /code

RUN lhapdf install NNPDF23_lo_as_0130_qed


RUN mkdir /workarea && cd workarea \
    && pip install --user madflow \ 
    && git clone https://github.com/N3PDF/madflow.git \
    && wget https://launchpad.net/mg5amcnlo/3.0/3.1.x/+download/MG5_aMC_v3.1.0.tar.gz \
    && tar xfz MG5_aMC_v3.1.0.tar.gz \
    && rm MG5_aMC_v3.1.0.tar.gz \
    && cd madflow \
    &&  ln -s /workarea/madflow/madgraph_plugin /workarea/MG5_aMC_v3_1_0/PLUGIN/pyout

ENV MADGRAPH_PATH=/workarea/MG5_aMC_v3_1_0
RUN lhapdf install NNPDF31_nnlo_as_0118