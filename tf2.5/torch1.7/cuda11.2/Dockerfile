## this is for docexoty/exatrkx:tf2.5-torch1.7-cuda11.2-ubuntu20.04
# python -c "import torch; print(torch.__version__)"
# python -c "import torch; print(torch.version.cuda)"

FROM nvidia/cuda:11.2.2-cudnn8-devel-ubuntu20.04

RUN apt-get update -y  && DEBIAN_FRONTEND="noninteractive" TZ="America/Los_Angeles"  apt-get -y install \
	python3-dev python3-pip git vim libhdf5-mpich-dev cmake nodejs 

# point python to python3.
RUN ln -s /usr/bin/python3.8 /usr/bin/python && pip install --user --upgrade pip 

ENV PYTHONUSERBASE="/usr/local/python3.8"
ENV PATH=/usr/local/root/bin:/usr/local/python3.8/bin:$PATH
ENV PYTHONPATH=${PYTHONPATH}:/usr/lib/python3.8/site-packages:/usr/local/root/lib/root
ENV LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:/usr/local/cuda/lib64

RUN pip install --user sympy numpy==1.19.5 pandas==1.4.1 scipy numba mpi4py matplotlib seaborn

RUN pip install --user tensorflow==2.5 tensorboard==2.8.0

RUN pip install --user torch==1.7.1+cu110 -f https://download.pytorch.org/whl/torch_stable.html 

# pytorch-geometrics
RUN  pip install --user torch-scatter==2.0.7 -f https://pytorch-geometric.com/whl/torch-1.7.0+cu110.html 
RUN  pip install --user --no-cache-dir torch-sparse==0.6.9  -f https://pytorch-geometric.com/whl/torch-1.7.0+cu110.html 
RUN  pip install --user torch-cluster -f https://pytorch-geometric.com/whl/torch-1.7.0+cu110.html \
	&& pip install --user torch-spline-conv -f https://pytorch-geometric.com/whl/torch-1.7.0+cu110.html \
	&& pip install --user torch-geometric==1.7.1

# RUN HOROVOD_WITH_TENSORFLOW=1 HOROVOD_WITH_PYTORCH=1 HOROVOD_WITH_MPI=1 HOROVOD_WITH_GLOO=1 HOROVOD_GPU_OPERATIONS=NCCL pip install --no-cache-dir --user git+https://github.com/horovod/horovod.git@v0.21.3

# NV-system for profiling
# https://docs.nvidia.com/nsight-systems/InstallationGuide/index.html#cuda-support
RUN apt-get update -y && \
     DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
         apt-transport-https \
         ca-certificates \
         gnupg \
         wget && \
     rm -rf /var/lib/apt/lists/*
RUN wget -qO - https://developer.download.nvidia.com/devtools/repos/ubuntu2004/amd64/nvidia.pub | apt-key add - && \
     echo "deb https://developer.download.nvidia.com/devtools/repos/ubuntu2004/amd64/ /" >> /etc/apt/sources.list.d/nsight.list && \
     apt-get update -y && \
     DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
         nsight-systems-2021.2  && \
    rm -rf /var/lib/apt/lists/*

# nsight-compute-2020.1.0
# https://developer.nvidia.com/nsight-compute-history
# https://developer.nvidia.com/blog/using-nsight-compute-in-containers/
RUN  echo "deb https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64 /" > /etc/apt/sources.list.d/cuda.list && \
     wget -qO - https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub | apt-key add - && \
         apt-get update -y && \
     DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
         nsight-compute-2021.1 && \
     rm -rf /var/lib/apt/lists/*

RUN pip install --user xgboost && pip install -U "ray[tune]"

RUN pip install --user jupyterlab
RUN groupadd -g 504 crd && useradd -g 504 -u 47045 --create-home xju