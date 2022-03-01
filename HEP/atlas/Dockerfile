# docexoty/centos7-atlasos
# https://gitlab.cern.ch/atlas-sit/docker/-/blob/master/centos7-atlasos/Dockerfile
# CC7 OS capable of using/running the ATLAS software release(s).

FROM atlas/centos7-atlasos-dev

USER root
WORKDIR /root

RUN yum -y update && yum install -y zlib-devel vim \
    alsa-lib-devel atlas-devel boost-python compat-db compat-glibc-headers \
    compat-libf2c-34 compat-libgfortran-41 compat-libstdc++-33 compat-openldap \
    dbus-devel glibc-static glibmm24 gmp-devel gperf libatomic libcap-devel \
    libcap-ng-devel libtiff-devel libXdmcp-devel libXp-devel \
    libXScrnSaver-devel libXtst-devel motif-devel nss-devel openssl098e \
    pciutils-devel perl-ExtUtils-Embed texlive-dvipng-bin xcb-proto \
    xorg-x11-xtrans-devel zsh

# https://code.visualstudio.com/docs/setup/linux#_visual-studio-code-is-unable-to-watch-for-file-changes-in-this-large-workspace-error-enospc
#RUN echo fs.inotify.max_user_watches=524288 | tee -a /etc/sysctl.conf  && sysctl -p