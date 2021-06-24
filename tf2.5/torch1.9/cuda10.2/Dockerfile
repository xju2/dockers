## this is for docexoty/exatrkx:tf2.5-torch1.9.0-cuda10.2-ubuntu18.04 
# python -c "import torch; print(torch.__version__)"
# python -c "import torch; print(torch.version.cuda)"
# python 3.6.5

FROM nvidia/cuda:10.2-cudnn8-devel-ubuntu18.04

RUN apt-get update -y  && DEBIAN_FRONTEND="noninteractive" TZ="America/Los_Angeles"  apt-get -y install \
	git vim libhdf5-mpich-dev cmake nodejs wget 

# the default python version is 3.6.5,
# install python 3.9.5 instead
RUN DEBIAN_FRONTEND="noninteractive" TZ="America/Los_Angeles" apt-get -y install build-essential checkinstall \
    libreadline-gplv2-dev libncursesw5-dev libssl-dev libsqlite3-dev \
    tk-dev libgdbm-dev libc6-dev libbz2-dev libffi-dev zlib1g-dev 

RUN mkdir /code && cd /code \
    && wget https://www.python.org/ftp/python/3.9.5/Python-3.9.5.tgz -O- | tar xz \
    && cd Python-3.9.5 && ./configure --enable-optimizations && make install -j6 \
    && cd / && rm -r /code 

# point python to python3.
RUN ln -s /usr/local/bin/python3.9 /usr/bin/python && ln -s /usr/local/bin/pip3.9 /usr/bin/pip

ENV PYTHONUSERBASE="/usr/local/python3.9"
ENV PATH=/usr/local/root/bin:/usr/local/python3.9/bin:$PATH
ENV PYTHONPATH=${PYTHONPATH}:/usr/lib/python3.9/site-packages
ENV LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:/usr/local/cuda/lib64

RUN pip install --user sympy numpy==1.19.5 scipy mpi4py matplotlib seaborn

RUN pip install --user tensorflow==2.5

RUN pip install --user torch==1.9.0+cu102 torchvision==0.10.0+cu102 \
    torchaudio==0.9.0 -f https://download.pytorch.org/whl/torch_stable.html

# pytorch-geometrics
RUN  pip install --user torch-scatter -f https://pytorch-geometric.com/whl/torch-1.9.0+cu102.html 
RUN  pip install --user torch-sparse  -f https://pytorch-geometric.com/whl/torch-1.9.0+cu102.html 
RUN  pip install --user torch-cluster -f https://pytorch-geometric.com/whl/torch-1.9.0+cu102.html \
	&& pip install --user torch-spline-conv -f https://pytorch-geometric.com/whl/torch-1.9.0+cu102.html \
	&& pip install --user torch-geometric

RUN HOROVOD_WITH_TENSORFLOW=1 HOROVOD_WITH_PYTORCH=1 HOROVOD_WITH_MPI=1 \
    HOROVOD_WITH_GLOO=1 HOROVOD_GPU_OPERATIONS=NCCL \
    pip install --no-cache-dir --user git+https://github.com/horovod/horovod.git@v0.21.3

# NV-system for profiling
# https://docs.nvidia.com/nsight-systems/InstallationGuide/index.html#cuda-support
RUN apt-get update -y && \
     DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
         apt-transport-https \
         ca-certificates \
         gnupg \
         wget && \
     rm -rf /var/lib/apt/lists/*
RUN wget -qO - https://developer.download.nvidia.com/devtools/repos/ubuntu1804/amd64/nvidia.pub | apt-key add - && \
     echo "deb https://developer.download.nvidia.com/devtools/repos/ubuntu1804/amd64/ /" >> /etc/apt/sources.list.d/nsight.list && \
     apt-get update -y && \
     DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
         nsight-systems-2021.2  && \
    rm -rf /var/lib/apt/lists/*

# nsight-compute-2020.1.0
# https://developer.nvidia.com/nsight-compute-history
# https://developer.nvidia.com/blog/using-nsight-compute-in-containers/
RUN  echo "deb https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64 /" > /etc/apt/sources.list.d/cuda.list && \
     wget -qO - https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub | apt-key add - && \
         apt-get update -y && \
     DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
         nsight-compute-2021.1 && \
     rm -rf /var/lib/apt/lists/*