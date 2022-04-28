# docexoty/centos7-atlasos-gpu
# https://gitlab.cern.ch/atlas-sit/docker/-/blob/master/centos7-atlasos/Dockerfile
# CC7 OS capable of using/running the ATLAS software release(s).

FROM nvidia/cuda:11.4.2-devel-centos7

USER root
WORKDIR /root

COPY *.repo /etc/yum.repos.d/
# Copy the environment setup script(s) in place:
COPY atlas_prompt.sh cmake-3.21.3.sh /etc/profile.d/

# Install CMake
RUN yum -y update && yum -y install which git wget tar atlas-devel \
    redhat-lsb-core libX11-devel libXpm-devel libXft-devel libXext-devel \
    openssl-devel glibc-devel rpm-build nano sudo  zlib-devel vim \
    alsa-lib-devel atlas-devel compat-db compat-glibc-headers \
    compat-libf2c-34 compat-libgfortran-41 compat-libstdc++-33 compat-openldap \
    dbus-devel glibc-static glibmm24 gmp-devel gperf libatomic libcap-devel \
    libcap-ng-devel libtiff-devel libXdmcp-devel libXp-devel \
    libXScrnSaver-devel libXtst-devel motif-devel nss-devel openssl098e \
    pciutils-devel perl-ExtUtils-Embed texlive-dvipng-bin xcb-proto \
    xorg-x11-xtrans-devel \
    && yum clean all \
    && wget https://cmake.org/files/v3.21/cmake-3.21.3-linux-x86_64.tar.gz \
    && mkdir -p /opt/cmake/3.21.3/Linux-x86_64 \
    && tar -C /opt/cmake/3.21.3/Linux-x86_64 --strip-components=1 \
      --no-same-owner -xvf cmake-*-linux-x86_64.tar.gz \
    && rm cmake-*-linux-x86_64.tar.gz \
    && wget https://github.com/ninja-build/ninja/releases/download/v1.10.2/ninja-linux.zip \
    && mkdir -p /opt/ninja/1.10.2/Linux-x86_64 \
    && unzip ninja-linux.zip -d /opt/ninja/1.10.2/Linux-x86_64 \
    && chmod 755 /opt/ninja/*/Linux-x86_64/ninja \
    && rm ninja-linux.zip

# Set up the ATLAS user, and give it super user rights.
RUN echo '%wheel	ALL=(ALL)	NOPASSWD: ALL' >> /etc/sudoers && \
    adduser atlas && chmod 755 /home/atlas && \
    usermod -aG wheel atlas && \
    usermod -aG root atlas && \
    mkdir /workdir && chown "atlas:atlas" /workdir && \
    chmod 755 /workdir

# LH: Make Images Grid / Singularity compatible
# Note that this is a hack as long as overlayfs
# is not widely on grid sites using singularity
# as a container runtime. For overlay-enabled
# runtimes (such as docker) missing mountpoints
# are created on demand. I hope we can remove this soon.
RUN mkdir -p /alrb /cvmfs /afs /eos

# Put the environment setup scripts in place.
COPY etc/*.sh /etc/profile.d/

# Switch back to the ATLAS user.
USER atlas
WORKDIR /workdir


# Install the AtlasSetup configuration file.
COPY etc/.asetup /home/atlas/

CMD /bin/bash

# https://code.visualstudio.com/docs/setup/linux#_visual-studio-code-is-unable-to-watch-for-file-changes-in-this-large-workspace-error-enospc
#RUN echo fs.inotify.max_user_watches=524288 | tee -a /etc/sysctl.conf  && sysctl -p

RUN mkdir /home/atlas/python3.9
ENV PYTHONUSERBASE="/home/atlas/python3.9"
ENV PATH=${PYTHONUSERBASE}/bin:$PATH
ENV LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:/opt/onnxruntime/lib64:/home/atlas/python3.9/lib/python3.9/site-packages/torch/lib:/home/atlas/python3.9/lib:${PYTHONUSERBASE}/lib/python3.9/site-packages/lib
ENV PYTHONPATH=${PYTHONPATH}:${PYTHONUSERBASE}/lib/python3.9/site-packages/