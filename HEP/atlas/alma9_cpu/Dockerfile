# docexoty/alma9-atlasos-dev:latest
# AlmaLinux OS 9 capable of using/running the ATLAS software release(s).

# Set up the Alma9 "ATLAS OS":
FROM cern/alma9-base:latest

# Perform the installation as root:
USER root
WORKDIR /root

# Put the repository configuration file(s) in place:
COPY *.repo /etc/yum.repos.d/

# The LCG GCC package(s) to install as part of the image.
ARG GCCPACKAGES="gcc_13.1.0_x86_64_el9 binutils_2.40_x86_64_el9"
ARG GCCVERSION=13.1.0

# 1. Install extra EL9 and LCG packages needed by the ATLAS release
# 2. Install CMake
RUN dnf --refresh -y install \
      --disablerepo=tdaq* --disablerepo=atlas* --disablerepo=lcg-x86_64-el9* \
      which git wget atlas-devel make \
      libX11-devel libXpm-devel libXft-devel libXext-devel \
      libaio libnsl openssl-devel glibc-devel rpm-build nano sudo \
      ${GCCPACKAGES} \
 && ln -s /opt/lcg/gcc/${GCCVERSION}/x86_64-el9 /opt/lcg/gcc/x86_64-el9 \
 && dnf clean all \
 && wget https://cmake.org/files/v3.27/cmake-3.27.5-linux-x86_64.tar.gz \
 && mkdir -p /opt/cmake/3.27.5/Linux-x86_64 \
 && tar -C /opt/cmake/3.27.5/Linux-x86_64 --strip-components=1 \
        --no-same-owner -xvf cmake-*-linux-x86_64.tar.gz \
 && rm cmake-*-linux-x86_64.tar.gz

# Copy the environment setup script(s) in place:
COPY atlas_prompt.sh cmake-3.27.5.sh /etc/profile.d/

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

# 1. Install all the packages needed for ATLAS software development.
# 2. Install ninja
RUN dnf --refresh -y install libXi-devel gmp-devel \
      mesa-libGL-devel mesa-libGLU-devel mesa-libEGL-devel \
      libcurl-devel man-db libuuid-devel \
      libaio-devel expat-devel libtirpc-devel \
      libjpeg-turbo libpng libtiff \
 && dnf clean all \
 && wget https://github.com/ninja-build/ninja/releases/download/v1.11.1/ninja-linux.zip \
 && mkdir -p /opt/ninja/1.11.1/Linux-x86_64 \
 && unzip ninja-linux.zip -d /opt/ninja/1.11.1/Linux-x86_64 \
 && chmod 755 /opt/ninja/*/Linux-x86_64/ninja \
 && rm ninja-linux.zip

# Put the environment setup scripts in place.
COPY etc/*.sh /etc/profile.d/

# Switch back to the ATLAS user.
USER atlas
WORKDIR /workdir

# Install the AtlasSetup configuration file.
COPY etc/.asetup /home/atlas/

# Copy the updated motd
COPY etc/motd /etc/

RUN mkdir /home/atlas/python3.9
ENV PYTHONUSERBASE="/home/atlas/python3.9"
ENV PATH=${PYTHONUSERBASE}/bin:$PATH
ENV LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:/home/atlas/python3.9/lib:${PYTHONUSERBASE}/lib/python3.9/site-packages/lib
ENV PYTHONPATH=${PYTHONPATH}:${PYTHONUSERBASE}/lib/python3.9/site-packages/

# Start the image with BASH by default, after having printed the message
# of the day.
CMD cat /etc/motd && /bin/bash