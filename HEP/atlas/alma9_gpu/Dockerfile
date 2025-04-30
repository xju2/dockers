# docexoty/el9-atlasos-dev-gpu:latest
# 

# Set up the NVIDIA CUDA 12.4.1 and cuDNN
FROM nvidia/cuda:12.8.1-cudnn-devel-rockylinux9

# Perform the installation as root:
USER root
WORKDIR /root

RUN yum -y install yum-utils procps-ng && yum-config-manager --enable crb  \
 && yum -y install https://linuxsoft.cern.ch/wlcg/el9/x86_64/wlcg-repo-1.0.0-1.el9.noarch.rpm \
           https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm \
 && yum -y update && yum -y install HEP_OSlibs && yum clean all
RUN yum -y erase zsh && yum -y update \
 && yum -y install yum-utils expat expat-devel man-db wget nodejs gcc-toolset-13-libstdc++-devel python3-pip \
 && yum clean all
RUN yum-config-manager --add-repo "https://storage-ci.web.cern.ch/storage-ci/eos/diopside-depend/el-9/x86_64/" \
 && yum-config-manager --add-repo "https://storage-ci.web.cern.ch/storage-ci/eos/diopside/tag/testing/el-9/x86_64/"
RUN yum -y --nogpgcheck install eos-client eos-fusex vim sudo && yum clean all


# Copy the environment setup script(s) in place:
COPY atlas_prompt.sh /etc/profile.d/

# Set up the ATLAS user, and give it super user rights.
RUN echo '%wheel	ALL=(ALL)	NOPASSWD: ALL' >> /etc/sudoers \
 && adduser atlas && chmod 777 /home/atlas \
 && usermod -aG wheel atlas \
 && usermod -aG root atlas \
 && mkdir /workdir && chown "atlas:atlas" /workdir \
 && chmod 777 /workdir

# LH: Make Images Grid / Singularity compatible
# Note that this is a hack as long as overlayfs
# is not widely on grid sites using singularity
# as a container runtime. For overlay-enabled
# runtimes (such as docker) missing mountpoints
# are created on demand. I hope we can remove this soon.
RUN mkdir -p /alrb /cvmfs /afs /eos

# Put the environment setup scripts in place.
COPY etc/*.sh /etc/profile.d/

RUN yum -y install python3.11 python3.11-pip 
RUN rm /usr/bin/pip /usr/bin/pip3.9 /usr/bin/pip3 /usr/bin/pip-3.9 /usr/bin/python3.9 /usr/bin/python3 \
  && ln -s /usr/bin/pip3.11 /usr/bin/pip \
  && ln -s /usr/bin/python3.11 /usr/bin/python3 \
  && pip install --upgrade --user pip

RUN cd /opt && \
    wget https://download.pytorch.org/libtorch/cu128/libtorch-cxx11-abi-shared-with-deps-2.7.0%2Bcu128.zip && \
    unzip libtorch-cxx11-abi-shared-with-deps-2.7.0+cu128.zip && \
    rm libtorch-cxx11-abi-shared-with-deps-2.7.0+cu128.zip

# Switch back to the ATLAS user.
USER atlas
WORKDIR /workdir

# Copy the updated motd
COPY etc/motd /etc/

RUN mkdir /home/atlas/python3.11
ENV PYTHONUSERBASE="/home/atlas/python3.11"
ENV PATH=${PYTHONUSERBASE}/bin:$PATH
ENV LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:${PYTHONUSERBASE}/lib:${PYTHONUSERBASE}/lib/python3.11/site-packages/lib
ENV PYTHONPATH=${PYTHONPATH}:${PYTHONUSERBASE}/lib/python3.11/site-packages

# RUN pip install --user wheel \
#     && pip install --user torch torchvision torchaudio tensorrt  torch-tensorrt

# Start the image with BASH by default, after having printed the message
# of the day.
CMD cat /etc/motd && /bin/bash
