ARG tag=18.04
FROM ubuntu:${tag}

LABEL maintainer="E. Schumann <e-schumann@online.de>"

RUN apt-get -qq update \
    && DEBIAN_FRONTEND=noninteractive apt-get -qq install -y --no-install-recommends \
       build-essential=12.4* \
       openjdk-8-jdk=8u162* \
       python2.7=2.7.15* \
       wget=1.19.* \
       zip=3.0* \
       unzip=6.0* \
       ca-certificates=20180409 \  
    && update-alternatives --install /usr/bin/python python /usr/bin/python2.7 100 \     
    && update-alternatives --set python /usr/bin/python2.7 \
    && wget --no-check-certificate --quiet https://github.com/bazelbuild/bazel/releases/download/0.13.0/bazel-0.13.0-dist.zip \
    && unzip bazel-0.13.0-dist.zip -d bazel \
    && cd bazel \
    && bash ./compile.sh \
    && cp output/bazel /usr/local/bin \
    && cd .. && rm -rf bazel \
    && apt-get -qq update \
    && DEBIAN_FRONTEND=noninteractive apt-get -qq install -y --no-install-recommends \
       binutils=2.30* \
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
       libmpfr-dev=4.0.* \
       libmpc-dev=1.1.* \
       libc6-dev=2.27* \
       libsasl2-dev=2.1.27* \
       libbz2-dev=1.0.6* \
       zlib1g-dev=1:1.2.11.* \
       nasm=2.13.* \
       dh-autoreconf=17 \
       valgrind=1:3.13.* \
       libffi-dev=3.2.* \
       libssl-dev=1.1.0* \
       pkg-config=0.29.1* \
       python2.7-dev=2.7.15* \       
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
    && pip install -q --no-cache-dir numpy scipy matplotlib pandas nose shapely fiona

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
# export NO_BUCKD=1  # Don't use daemon e.g. because it fails to start
# https://buckaroo.readthedocs.io/en/latest/installation.html#linux
# https://buckaroo.pm/
# https://stackoverflow.com/questions/40995131/what-is-the-best-way-to-integrate-a-cmake-project-into-buck# Docker & General Build Stuff
# https://stackoverflow.com/questions/41142890/can-i-specify-git-urls-as-dependencies-in-buck
# https://github.com/njlr/buck-cells-example <---- This one is important
#  
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
# http://hanxue-it.blogspot.com/2017/11/git-submodule-how-to-pull-update-and-delete-linked-git-repositories.html
# https://help.github.com/articles/fork-a-repo/
# https://stackoverflow.com/questions/18404272/running-ssh-agent-when-starting-git-bash-on-windows
#
# -----------------------------------------------------------------------------------------------------------
# Bazel:
# ------
# https://www.jayconrod.com/posts/98/migrating-to-bazel--part-2
# https://www.ratanparai.com/c++/writing-unit-tests-with-bazel/
# -----------------------------------------------------------------------------------------------------------

# RUN git clone --recursive https://github.com/sorin-ionescu/prezto.git "${ZDOTDIR:-$HOME}/.zprezto"
# COPY ./.zshrc /
# RUN chsh -s /bin/zsh
# RUN /bin/zsh -c "source /.zshrc"
# RUN sed -ri "s/theme 'sorin'/theme 'skwp'/g" ~/.zpreztorc
# RUN sed -ri "s/'prompt'/'prompt' 'syntax-highlighting' 'history-substring-search' 'git'/g" ~/.zpreztorc
# CMD [ "/bin/zsh" ]    
