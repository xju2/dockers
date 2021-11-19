# docexoty/centos7-atlasos
# https://gitlab.cern.ch/atlas-sit/docker/-/blob/master/centos7-atlasos/Dockerfile
# CC7 OS capable of using/running the ATLAS software release(s).

FROM atlas/centos7-atlasos-dev

USER root
WORKDIR /root

RUN yum -y update && yum install -y zlib-devel

# https://code.visualstudio.com/docs/setup/linux#_visual-studio-code-is-unable-to-watch-for-file-changes-in-this-large-workspace-error-enospc
#RUN echo fs.inotify.max_user_watches=524288 | tee -a /etc/sysctl.conf  && sysctl -p

RUN yum install -y vim
