# this is for docexoty/exatrkx:tf2.5-torch1.9-cuda11.2-ubuntu20.04-rapids21.06-py3.7-runtime
FROM nvidia/cuda:11.2.0-runtime-ubuntu20.04

ENV LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:/usr/local/cuda/lib64

RUN apt-get update -y  && DEBIAN_FRONTEND="noninteractive" TZ="America/Los_Angeles" \
    apt-get -y install --no-install-recommends vim wget autoconf automake bzip2 \
        ca-certificates curl git locales patch tzdata unzip  \
    && rm -rf /var/lib/apt/lists/*

RUN mkdir /code
WORKDIR /code

RUN wget --quiet \
    https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh \
    && bash Miniconda3-latest-Linux-x86_64.sh -b -p /opt/conda\
    && rm -f Miniconda3-latest-Linux-x86_64.sh \
    && ln -s /opt/conda/etc/profile.d/conda.sh /etc/profile.d/conda.sh 

# COPY environment.yml /code/
# RUN . /opt/conda/etc/profile.d/conda.sh \
#     && conda env create --file=/code/environment.yml

RUN . /opt/conda/etc/profile.d/conda.sh && conda activate devel \
    conda install -c nvidia -c rapidsai -c numba -c conda-forge cugraph cudatoolkit=11.0 

RUN . /opt/conda/etc/profile.d/conda.sh && conda activate devel \
    && pip install torch==1.9.0+cu111 torchvision==0.10.0+cu111 torchaudio===0.9.0 -f https://download.pytorch.org/whl/torch_stable.html \
    && pip install --no-cache-dir torch-scatter -f https://pytorch-geometric.com/whl/torch-1.7.0+cu110.html \
    && pip install --no-cache-dir torch-sparse==0.6.9  -f https://pytorch-geometric.com/whl/torch-1.7.0+cu110.html \
    && pip install torch-cluster -f https://pytorch-geometric.com/whl/torch-1.7.0+cu110.html \
    && pip install torch-spline-conv -f https://pytorch-geometric.com/whl/torch-1.7.0+cu110.html \
    && pip install torch-geometric

# # it is runtime container, no nvcc, no horovod!
# RUN . /opt/conda/etc/profile.d/conda.sh && conda activate rapids &&  HOROVOD_WITH_TENSORFLOW=1 HOROVOD_WITH_PYTORCH=1 HOROVOD_WITH_MPI=1 HOROVOD_WITH_GLOO=1 \
#     HOROVOD_BUILD_CUDA_CC_LIST=70,75,80 HOROVOD_GPU_OPERATIONS=NCCL HOROVOD_NCCL_LINK=SHARED \
#     pip install --no-cache-dir --user git+https://github.com/horovod/horovod.git@v0.21.3

# # NV-system for profiling
# # https://docs.nvidia.com/nsight-systems/InstallationGuide/index.html#cuda-support
# RUN apt-get update -y && \
#      DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
#          apt-transport-https \
#          ca-certificates \
#          gnupg \
#          wget && \
#      rm -rf /var/lib/apt/lists/*
# RUN wget -qO - https://developer.download.nvidia.com/devtools/repos/ubuntu2004/amd64/nvidia.pub | apt-key add - && \
#      echo "deb https://developer.download.nvidia.com/devtools/repos/ubuntu2004/amd64/ /" >> /etc/apt/sources.list.d/nsight.list && \
#      apt-get update -y && \
#      DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
#          nsight-systems-2021.2  && \
#     rm -rf /var/lib/apt/lists/*

# # nsight-compute-2020.1.0
# # https://developer.nvidia.com/nsight-compute-history
# # https://developer.nvidia.com/blog/using-nsight-compute-in-containers/
# RUN  echo "deb https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64 /" > /etc/apt/sources.list.d/cuda.list && \
#      wget -qO - https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub | apt-key add - && \
#          apt-get update -y && \
#      DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
#          nsight-compute-2021.1 && \
#      rm -rf /var/lib/apt/lists/*


# pip install --no-cache-dir torch-scatter -f https://pytorch-geometric.com/whl/torch-1.7.0+cu110.html \
# && pip install --no-cache-dir torch-sparse==0.6.9  -f https://pytorch-geometric.com/whl/torch-1.7.0+cu110.html \
# && pip install torch-cluster -f https://pytorch-geometric.com/whl/torch-1.7.0+cu110.html \
# && pip install torch-spline-conv -f https://pytorch-geometric.com/whl/torch-1.7.0+cu110.html \
# && pip install torch-geometric