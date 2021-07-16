# this is for docexoty/exatrkx:tf2.4-torch1.7-cuda11.0-ubuntu20.04-rapids21.06-py3.8-runtime
FROM rapidsai/rapidsai:21.06-cuda11.0-runtime-ubuntu20.04-py3.8

ENV LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:/usr/local/cuda/lib64

RUN apt-get update -y  && DEBIAN_FRONTEND="noninteractive" TZ="America/Los_Angeles"  apt-get -y install vim

# COPY environments.yml /rapids/
# RUN conda env update --name rapids --file=/rapids/environments.yml

RUN . /opt/conda/etc/profile.d/conda.sh && conda activate rapids \
    && pip install tensorflow==2.4 \
    && pip install tensorboard==2.4 \
    && pip install torch==1.7.1+cu110 torchvision==0.8.2+cu110 torchaudio==0.7.2 -f https://download.pytorch.org/whl/torch_stable.html \
    && pip install torch-scatter -f https://pytorch-geometric.com/whl/torch-1.7.0+cu110.html \
    && pip install --no-cache-dir torch-sparse==0.6.9  -f https://pytorch-geometric.com/whl/torch-1.7.0+cu110.html \
    && pip install torch-cluster -f https://pytorch-geometric.com/whl/torch-1.7.0+cu110.html \
    && pip install torch-spline-conv -f https://pytorch-geometric.com/whl/torch-1.7.0+cu110.html \
    && pip install torch-geometric \
    && conda install -c conda-forge faiss-gpu

# NV-system for profiling
# https://docs.nvidia.com/nsight-systems/InstallationGuide/index.html#cuda-support
RUN apt-get update -y && \
     DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
         apt-transport-https \
         ca-certificates \
         gnupg \
         wget && \
     rm -rf /var/lib/apt/lists/*

# nsight-compute-2020.1.0
# https://developer.nvidia.com/nsight-compute-history
# https://developer.nvidia.com/blog/using-nsight-compute-in-containers/
RUN  echo "deb https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64 /" > /etc/apt/sources.list.d/cuda.list && \
     wget -qO - https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub | apt-key add - && \
         apt-get update -y && \
     DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
         nsight-systems-2021.2 nsight-compute-2021.1 && \
     rm -rf /var/lib/apt/lists/*


# pip install --no-cache-dir torch-scatter -f https://pytorch-geometric.com/whl/torch-1.7.0+cu110.html \
# && pip install --no-cache-dir torch-sparse==0.6.9  -f https://pytorch-geometric.com/whl/torch-1.7.0+cu110.html \
# && pip install torch-cluster -f https://pytorch-geometric.com/whl/torch-1.7.0+cu110.html \
# && pip install torch-spline-conv -f https://pytorch-geometric.com/whl/torch-1.7.0+cu110.html \
# && pip install torch-geometric