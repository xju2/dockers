# docexoty/root:latest

from rootproject/root:6.32.04-ubuntu24.04

RUN apt-get update -y && apt-get -y install python3-pip python3-sphinx python3.12-venv

RUN cd /opt && python -m venv venv
RUN mkdir /opt/code
RUN cd /opt/code && . /opt/venv/bin/activate \
  && git clone https://github.com/xju2/root_plot_utils.git \
  && cd root_plot_utils  &&  pip install . 
