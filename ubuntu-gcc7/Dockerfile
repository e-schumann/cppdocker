FROM ubuntu:18.04

RUN \
  apt-get update && \
  apt-get install -y software-properties-common && \
  add-apt-repository ppa:ubuntu-toolchain-r/test && \
  apt-get update && \
  apt-get install -y gcc-7 g++-7 && \
  update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-7 60 && \
  update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-7 60
CMD /bin/bash
