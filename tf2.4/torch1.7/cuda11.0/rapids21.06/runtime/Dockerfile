# this is for docexoty/exatrkx:tf2.4-torch1.7-cuda11.0-ubuntu20.04-rapids21.06-py3.8-runtime
FROM rapidsai/rapidsai:21.06-cuda11.0-runtime-ubuntu20.04-py3.8

ENV LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:/usr/local/cuda/lib64

RUN apt-get update -y  && DEBIAN_FRONTEND="noninteractive" TZ="America/Los_Angeles"  apt-get -y install vim

COPY environments.yml /rapids/
RUN . /opt/conda/etc/profile.d/conda.sh && conda activate rapids && conda env update --file=/rapids/environments.yml

# RUN HOROVOD_WITH_TENSORFLOW=1 HOROVOD_WITH_PYTORCH=1 HOROVOD_WITH_MPI=1 HOROVOD_WITH_GLOO=1 \
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