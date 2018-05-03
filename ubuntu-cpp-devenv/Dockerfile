ARG tag=18.04
FROM ubuntu:${tag}

LABEL maintainer="Eike Schumann <eike.schumann@gmx.de>"

RUN apt-get -qq update \
    && DEBIAN_FRONTEND=noninteractive apt-get -qq install -y --no-install-recommends \
       software-properties-common=0.96.24.* \
       python2.7-dev=2.7.15* \
       sudo=1.8.* \
       build-essential=12.4* \
       binutils=2.30* \
       wget=1.19.* \
       curl=7.58.* \
       git=1:2.17.* \
       vim=2:8.0.* \
       zsh=5.4.2* \
       g++=4:7.3.* \
       clang=1:6.0* \
       clang-format=1:6.0* \
       clang-tools=1:6.0* \
       clang-tidy=1:6.0* \
       lld=1:6.0* \
       openjdk-8-jdk=8u162* \
       openjdk-8-jre-headless=8u162* \
       ant=1.10* \
       libmpfr-dev=4.0.* \
       libmpc-dev=1.1.* \
       libc6-dev=2.27* \
       libsasl2-dev=2.1.27* \
       libbz2-dev=1.0.6* \
       zlib1g-dev=1:1.2.11.* \
       nasm=2.13.* \
       dh-autoreconf=17 \
       valgrind=1:3.13.* \
       inotify-tools=3.14* \
       libinotifytools0-dev=3.14* \
       ninja-build=1.8.2* \
       libffi-dev=3.2.* \
       libssl-dev=1.1.0* \
       pkg-config=0.29.1-0ubuntu2 \
       ca-certificates=20180409 \
    && update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-7 100 \
    && update-alternatives --install /usr/bin/c++ c++ /usr/bin/g++-7 100 \
    && update-alternatives --install /usr/bin/cc cc /usr/bin/gcc 100 \
    && update-alternatives --install /usr/bin/cc cc /usr/bin/clang 50 \
    && update-alternatives --install /usr/bin/c++ c++ /usr/bin/clang++ 50 \    
    && update-alternatives --install /usr/bin/ld ld /usr/bin/ld.lld-6.0 100 \
    && update-alternatives --install /usr/bin/ld ld /usr/bin/ld.gold 50 \
    && update-alternatives --install /usr/bin/ld ld /usr/bin/ld.bfd 10 \    
    && update-alternatives --set c++ /usr/bin/g++-7 \
    && update-alternatives --set cc /usr/bin/gcc \
    && update-alternatives --set ld /usr/bin/ld.lld-6.0 \
    && rm -rf /var/lib/apt/lists/* \
    && wget --no-check-certificate --quiet https://bootstrap.pypa.io/get-pip.py \
    && python get-pip.py \
    && rm get-pip.py \
    && pip install -q -U pip \
    && pip install -q --no-cache-dir numpy scipy matplotlib pandas nose shapely fiona \
    && git clone https://github.com/facebook/watchman.git \
    && cd watchman \
    && git checkout v4.9.0 \
    && ./autogen.sh \
    && ./configure \
    && make -j $(nproc) && make install \
    && cd .. && rm -rf watchman \
    && git clone https://github.com/facebook/buck.git \
    && cd buck && ant \
    && ./bin/buck build --show-output buck \
    && buck-out/gen/programs/buck.pex --help 

ENV PATH="${PWD}/buck/bin:${PATH}"

# Start from a Bash prompt
CMD [ "/bin/bash" ]    

# -----------------------------------------------------------------------------------------------------------
# For buck:
# -------------------
# https://buckbuild.com/rule/prebuilt_cxx_library.html
# https://buckbuild.com/command/common_parameters.html
# https://github.com/facebookexperimental/buckit 
# https://www.youtube.com/watch?v=h8UoYG4dvH8
# https://hackernoon.com/how-to-create-a-buck-based-c-c-project-38b85273d6a6
# https://hackernoon.com/lessons-learned-from-porting-300-projects-to-buck-build-ff6463b65142
# buck build @config/gcc //:demo#strip-all
# ./buck-out/gen/demo
# https://buckaroo.readthedocs.io/en/latest/installation.html#linux
# https://buckaroo.pm/
# 
# Docker & General Build Stuff
# ----------------------------------
# https://stackoverflow.com/questions/26103966/how-can-i-statically-link-standard-library-to-my-c-program
# https://medium.com/kokster/fun-with-multi-stage-dockerfiles-7da7f11403d2
# https://gist.github.com/seal789ie/978e30769211311effbb Docker ZSH Prezto
# https://lld.llvm.org/ LLVM Linker
# https://gist.github.com/daniel-j-h/ed3bf9e5cb458b012b5a Linker Selection
# https://hackernoon.com/create-a-private-local-docker-registry-5c79ce912620 Private Docker Registry
# https://medium.com/@valgaze/from-zero-to-a-private-docker-registry-b42abad46fef Private Docker Registry in AWS
# https://docs.docker.com/registry/deploying/#more-advanced-authentication
# https://robots.thoughtbot.com/keeping-a-github-fork-updated
# 
# docker image ls
# docker system prune 
# docker images -f "dangling=true"
# docker build -t ubuntu-cpp-devenv:v1 .
# docker run -i -t ubuntu:12.04 /bin/bash
# docker run -i -t 8dbd9e392a96 /bin/bash
# docker run -t -i -v d:/projects/buck_tests:/develop ubuntu-cpp-devenv:v1 /bin/bash
#
# -----------------------------------------------------------------------------------------------------------

# RUN git clone --recursive https://github.com/sorin-ionescu/prezto.git "${ZDOTDIR:-$HOME}/.zprezto"
# COPY ./.zshrc /
# RUN chsh -s /bin/zsh
# RUN /bin/zsh -c "source /.zshrc"
# RUN sed -ri "s/theme 'sorin'/theme 'skwp'/g" ~/.zpreztorc
# RUN sed -ri "s/'prompt'/'prompt' 'syntax-highlighting' 'history-substring-search' 'git'/g" ~/.zpreztorc
# CMD [ "/bin/zsh" ]    