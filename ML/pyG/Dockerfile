# docexoty/mltools:pyG-1.11
FROM nvidia/cuda:11.7.1-cudnn8-devel-ubuntu20.04

RUN apt-get update -y  && DEBIAN_FRONTEND="noninteractive" TZ="America/Los_Angeles"  apt-get -y install \
	git vim wget rsync curl build-essential && rm -rf /var/lib/apt/lists/*

ENV CONDA_DIR /opt/conda
# Make RUN commands use `bash --login`
SHELL ["/bin/bash", "--login", "-c"]

RUN wget \
    https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh \
    && /bin/bash Miniconda3-latest-Linux-x86_64.sh -fb -p /opt/conda \
    && rm -f Miniconda3-latest-Linux-x86_64.sh 

ENV PATH=${CONDA_DIR}/bin:$PATH
ENV PYTHONNOUSERSITE=True

# Initialize conda in bash config files:
RUN conda init bash

# Install conda env
RUN source activate && \
	conda create --name gnn python=3.9
    
RUN source activate gnn && \
    conda install pytorch==1.11.0 torchvision==0.12.0 torchaudio==0.11.0 cudatoolkit=11.3 -c pytorch && \
    conda install pyg -c pyg

RUN source activate gnn && pip install --upgrade pip &&  pip install matplotlib scipy seaborn pandas && \
    pip install -U scikit-learn

RUN groupadd -g 504 crd && useradd -g 504 -u 47045 --create-home xju

RUN conda activate gnn && conda install -c conda-forge jupyterlab nodejs