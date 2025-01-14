# docexoty/alma9-atlasos-dev:latest
# AlmaLinux OS 9 capable of using/running the ATLAS software release(s).
# 

# Set up the Alma9 "ATLAS OS":
FROM cern/alma9-base:latest

# Perform the installation as root:
USER root
WORKDIR /root

# Put the repository configuration file(s) in place:
COPY *.repo /etc/yum.repos.d/

# check the CI config.
# https://gitlab.cern.ch/atlas/atlasexternals/-/blob/main/.gitlab-ci.yml?ref_type=heads#L12-13

RUN yum --refresh -y install \
      --disablerepo=tdaq* --disablerepo=atlas* --disablerepo=lcg-x86_64-el9* \
      which git wget atlas-devel make \
      libX11-devel libXpm-devel libXft-devel libXext-devel motif-devel \
      libnsl openssl-devel glibc-devel rpm-build nano vim sudo \
      libXi-devel gmp-devel \
      mesa-libGL-devel mesa-libGLU-devel mesa-libEGL-devel \
      libcurl-devel man-db libuuid-devel \
      libaio-devel expat-devel libtirpc-devel \
      libjpeg-turbo libpng libtiff libzstd-devel \
      glib2-devel libxkbcommon-devel xz-devel \
 && ln -s /usr/lib64/libsystemd.so.0.35.0 /usr/lib64/libsystemd.so \
 && yum clean all 

RUN yum -y install --disablerepo=tdaq* --disablerepo=atlas* --disablerepo=lcg-x86_64-el9* \
    https://linuxsoft.cern.ch/wlcg/el9/x86_64/wlcg-repo-1.0.0-1.el9.noarch.rpm \
    && yum -y install --disablerepo=tdaq* --disablerepo=atlas* --disablerepo=lcg-x86_64-el9* \
    HEP_OSlibs

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

# Switch back to the ATLAS user.
USER atlas
WORKDIR /workdir

# Install the AtlasSetup configuration file.
COPY etc/.asetup /home/atlas/

# Copy the updated motd
COPY etc/motd /etc/

RUN mkdir /home/atlas/python3.11
ENV PYTHONUSERBASE="/home/atlas/python3.11"
ENV PATH=${PYTHONUSERBASE}/bin:$PATH
ENV LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:${PYTHONUSERBASE}/lib:${PYTHONUSERBASE}/lib/python3.11/site-packages/lib
ENV PYTHONPATH=${PYTHONPATH}:${PYTHONUSERBASE}/lib/python3.11/site-packages/

# Start the image with BASH by default, after having printed the message
# of the day.
CMD cat /etc/motd && /bin/bash
