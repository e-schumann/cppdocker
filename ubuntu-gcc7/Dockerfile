ARG tag=18.04
FROM ubuntu:${tag}

LABEL maintainer="Eike Schumann <eike.schumann@gmx.de>"

ENV CXXFLAGS="-std=c++17"

RUN apt-get -qq update \
    && DEBIAN_FRONTEND=noninteractive apt-get -qq install -y --no-install-recommends \
       software-properties-common=0.96.24.* \
       python3-dev=3.6.* \
       sudo=1.8.* \
       build-essential=12.4* \
       binutils=2.30* \
       wget=1.19.* \
       curl=7.58.* \
       git=1:2.17.* \
       vim=2:8.0.* \
       g++-7=7.3.* \
       libgmp-dev=2:6.1.* \
       libmpfr-dev=4.0.* \
       libmpc-dev=1.1.* \
       libc6-dev=2.27* \
       libsasl2-dev=2.1.27* \
       libbz2-dev=1.0.6* \
       zlib1g-dev=1:1.2.11.* \
       nasm=2.13.* \
       dh-autoreconf=17 \
       valgrind=1:3.13.* \
       ninja-build=1.8.2* \
       libffi-dev=3.2.* \
       libssl-dev=1.1.0* \
       pkg-config=0.29.1-0ubuntu2 \
       ca-certificates=20180409 \
    && update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-7 100 \
    && update-alternatives --install /usr/bin/c++ c++ /usr/bin/g++-7 100 \
    && update-alternatives --install /usr/bin/python python /usr/bin/python3.6 100 \
    && rm -rf /var/lib/apt/lists/* \
    && wget --no-check-certificate --quiet https://cmake.org/files/v3.11/cmake-3.11.1-Linux-x86_64.tar.gz \
    && tar -xzf cmake-3.11.1-Linux-x86_64.tar.gz \
    && cp -fR cmake-3.11.1-Linux-x86_64/* /usr \
    && rm -rf cmake-3.11.1-Linux-x86_64 \
    && rm cmake-3.11.1-Linux-x86_64.tar.gz \
    && wget --no-check-certificate --quiet https://bootstrap.pypa.io/get-pip.py \
    && python get-pip.py \
    && rm get-pip.py \
    && pip install -q -U pip \
    && pip install -q --no-cache-dir numpy scipy matplotlib pandas nose 

#RUN git clone https://github.com/llvm-project/lld llvm-project \
#    && mkdir build \
#    && cd build \
#    && cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr/local ../llvm-project \
#    && make install \
#    && cd .. \
#    && rm -rf build/ \
#    && rm -rf llvm-project/ \
#    && ln -s /usr/local/bin/lld /usr/bin/ld

# ----------------------------------------------------
# -- Build Boost Libraries 
# ----------------------------------------------------

ARG BOOST_VERSION=1.67.0
ARG BOOST_DIR=1_67_0
ENV BOOST_VERSION=${BOOST_VERSION}
ENV BOOST_ROOT=/usr/include/boost
RUN wget https://dl.bintray.com/boostorg/release/${BOOST_VERSION}/source/boost_${BOOST_DIR}.tar.gz \
    && tar xfz boost_${BOOST_DIR}.tar.gz \
    && rm boost_${BOOST_DIR}.tar.gz \
    && cd boost_${BOOST_DIR} \
    && ./bootstrap.sh \
    && ./b2 --prefix=/usr -j $(nproc) link=shared runtime-link=shared install \
    && cd .. && rm -rf boost_${BOOST_DIR} && ldconfig

# ----------------------------------------------------
# -- Build C Bindings of Kafka 
# ----------------------------------------------------
    
RUN git clone https://github.com/edenhill/librdkafka.git \
    && cd librdkafka \
    && git checkout 13befa7 \
    && ./configure --prefix=/usr/local \
    && make -j $(nproc)\
    && make install \
    && cd .. && rm -rf ./librdkafka && ldconfig

# ----------------------------------------------------
# -- Build C++ Bindings of Kafka  
# ----------------------------------------------------

RUN git clone https://github.com/mfontanini/cppkafka.git \
    && mkdir cppkafka/build \
    && cd cppkafka \
    && git checkout ae74814 \
    && cd build \
    && cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr/local/ .. \
    && make -j $(nproc) \
    && make install \
    && cd ../.. && rm -rf cppkafka && ldconfig

# ----------------------------------------------------
# -- Build Logging Library spdlog  
# ----------------------------------------------------

RUN git clone https://github.com/gabime/spdlog.git \    
    && mkdir spdlog/build \
    && cd spdlog \
    && git checkout 71e93a4f2d52fb9f9dafdeea7386bf0c780981e3 \
    && cd build \
    && cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr/local/ .. \
    && make -j $(nproc) \
    && make install \
    && cd ../.. && rm -rf spdlog && ldconfig

# ----------------------------------------------------
# -- Build C++ YAML Parser  
# ----------------------------------------------------

RUN git clone https://github.com/jbeder/yaml-cpp.git \    
    && mkdir yaml-cpp/build \
    && cd yaml-cpp \
    && git checkout 124ae4760062e2f5827a6238c122ccbf66eb4e6e \
    && cd build \
    && cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr/local/ .. \
    && make -j $(nproc) \
    && make install \
    && cd ../.. && rm -rf yaml-cpp && ldconfig

# ----------------------------------------------------
# -- Build Google Test And Mock 
# ----------------------------------------------------

RUN git clone https://github.com/google/googletest.git \    
    && mkdir googletest/build \
    && cd googletest \
    && git checkout dc043e1ca6bd509a92452ed54e817b6979869372 \
    && cd build \
    && cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr/local/ .. \
    && make -j $(nproc) \
    && make install \
    && cd ../.. && rm -rf googletest && ldconfig

# ----------------------------------------------------
# -- Install Oracle Java8 For Google Bazel  
# ----------------------------------------------------

RUN add-apt-repository ppa:webupd8team/java \ 
    && echo "oracle-java8-installer shared/accepted-oracle-license-v1-1 select true" | debconf-set-selections \
    && apt-get -qq update \ 
    && apt-get -qq install -y --no-install-recommends \
      oracle-java8-installer \
      oracle-java8-set-default \
      gpg-agent=2.2.4*

# ----------------------------------------------------
# -- Install Googles Build System Bazel 
# ----------------------------------------------------

RUN echo "deb [arch=amd64] http://storage.googleapis.com/bazel-apt stable jdk1.8" | sudo tee /etc/apt/sources.list.d/bazel.list \
    && curl https://bazel.build/bazel-release.pub.gpg | sudo apt-key add - \
    && apt-get update -qq && apt-get -qq install -y --no-install-recommends bazel 

# ----------------------------------------------------
# -- Build C++ Better Enums Header  
# ----------------------------------------------------

RUN git clone https://github.com/aantron/better-enums.git /usr/local/include/better-enums/

# ----------------------------------------------------
# -- Build C++ Performance Meassurement Library Nonius 
# ----------------------------------------------------

RUN git clone https://github.com/rmartinho/nonius.git /usr/local/nonius/

# ----------------------------------------------------
# -- Build CGAL ( For graph-tool )
# ----------------------------------------------------

RUN git clone https://github.com/CGAL/cgal.git \
    && cd cgal && mkdir -p build/release \
    && git checkout releases/CGAL-4.12 \
    && cd build/release \
    && cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr/local/ ../.. \
    && make -j $(nproc) \
    && make install \
    && cd ../../.. && rm -rf cgal && ldconfig

# ----------------------------------------------------
# -- Build Python graph-tool
# ----------------------------------------------------
RUN apt-get -qq update \ 
    && apt-get -qq install -y --no-install-recommends \
      libcairomm-1.0-dev=1.12.2* \
      libsparsehash-dev=2.0.2* \
    && pip install -q --no-cache-dir pycairo 
             
RUN git clone https://git.skewed.de/count0/graph-tool.git \
    && cd graph-tool \
    && git checkout release-2.26 \
    && ./autogen.sh \
    && ./configure --prefix=/usr/local \
    && make -j 2 \
    && make install \
    && cd .. && rm -rf graph-tool && ldconfig

# ----------------------------------------------------
# -- Build C++ Dependency Injection Library Google Fruit
# ----------------------------------------------------
   
RUN git clone https://github.com/google/fruit.git \
    && cd fruit \
    && git checkout v3.1.1 \
    && mkdir build && cd build \ 
    && cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr/local/ .. \
    && make -j $(nproc) \
    && make install \
    && cd ../.. && rm -rf fruit && ldconfig

# ----------------------------------------------------
# -- Build C++ Vector Library Eigen
# ----------------------------------------------------
   
RUN git clone https://github.com/eigenteam/eigen-git-mirror.git eigen \
    && cd eigen \
    && git checkout 3.3.4 \
    && mkdir build && cd build \ 
    && cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr/local/ .. \
    && make -j $(nproc) \
    && make install \
    && cd ../.. && rm -rf eigen       

# ----------------------------------------------------
# -- Install Sqlite Spatialite GDAL 
# better:https://trac.osgeo.org/gdal/wiki/BuildHints
# pip install shapely etc
# pip install python gdal ?
# ----------------------------------------------------

RUN apt-get -qq update \ 
    && apt-get -qq install -y --no-install-recommends \
      libspatialite-dev=4.3.0a* \
      spatialite-bin=4.3.0* \
      python-pyspatialite=3.0.1* \
      gdal-bin=2.2.3* \
      libgdal-dev=2.2.3* \
      python3-gdal=2.2.3* \
      libassimp-dev=4.1.0* \
      libode-dev=2:0.14*  
          
# ----------------------------------------------------
# -- Build Open Motion Planning Library
# ----------------------------------------------------

#RUN git clone https://github.com/danfis/libccd.git \
#    && cd libccd && mkdir build && cd build \
#    && cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr/local/ .. \
#    && make -j $(nproc) && make install \
#    && cd ../.. && rm -rf libccd \ 
#    && git clone https://github.com/flexible-collision-library/fcl.git \
#    && cd fcl && git checkout 0.5.0 \
#    && mkdir build && cd build \
#    && cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr/local/ .. \
#    && make -j $(nproc) && make install \
#    && cd ../.. && rm -rf fcl \
#    && git clone https://github.com/OctoMap/octomap.git \
#    && cd octomap && mkdir build && cd build \
#    && CXXFLAGS="-std=c++14" cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr/local/ .. \
#    && make -j $(nproc) && make install \
#    && cd ../.. && rm -rf octomap \
#    && git clone https://github.com/ompl/omplapp.git \
#    && cd omplapp && git checkout 1.3.1\
#    && git clone https://github.com/ompl/ompl.git \
#    && cd ompl && git checkout 1.3.1\
#    && mkdir build && cd build \
#    && cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr/local/ .. \
#    && make -j $(nproc) && make install \
#    && cd ../.. \
#    && mkdir build && cd build \
#    && cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr/local/ .. \
#    && make -j $(nproc) && make install \
#    && cd ../.. && rm -rf omplapp

RUN git clone https://github.com/danfis/libccd.git \
    && cd libccd && mkdir build && cd build \
    && cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr/local/ .. \
    && make -j $(nproc) && make install \
    && cd ../.. && rm -rf libccd && ldconfig \ 
    && git clone https://github.com/flexible-collision-library/fcl.git \
    && cd fcl && git checkout 0.5.0 \
    && mkdir build && cd build \
    && cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr/local/ .. \
    && make -j $(nproc) && make install \
    && cd ../.. && rm -rf fcl \
    && git clone https://github.com/OctoMap/octomap.git \
    && cd octomap && mkdir build && cd build \
    && CXXFLAGS="-std=c++14" cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr/local/ .. \
    && make -j $(nproc) && make install \
    && cd ../.. && rm -rf octomap && ldconfig \
    && git clone https://github.com/ompl/ompl.git \
    && cd ompl && git checkout 1.3.1\
    && mkdir build && cd build \
    && cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr/local/ .. \
    && make -j $(nproc) && make install \
    && cd ../.. && rm -rf ompl && ldconfig


# ----------------------------------------------------
# -- Build Google Flatbuffers
# ----------------------------------------------------
      
RUN git clone https://github.com/google/flatbuffers.git \
    && cd flatbuffers \
    && git checkout v1.9.0 \
    && mkdir build && cd build \ 
    && cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr/local/ .. \
    && make -j $(nproc) \
    && make install \
    && cd ../.. && rm -rf flatbuffers
  
# ----------------------------------------------------
# -- Build Point Cloud Library
# ----------------------------------------------------
RUN apt-get -qq update \ 
    && apt-get -qq install -y --no-install-recommends \
      libflann-dev=1.9.1* \  
      libpcap-dev=1.8.1* \   
      freeglut3-dev=2.8.1* \
    && git clone https://github.com/PointCloudLibrary/pcl.git \
    && cd pcl \
    && git checkout pcl-1.8.1 \
    && mkdir build && cd build \ 
    && cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr/local/ .. \
    && make -j $(nproc) \
    && make install \
    && cd ../.. && rm -rf pcl && ldconfig
        
CMD bash    

#    && groupadd 1000 -g 1000 \
#    && useradd -ms /bin/bash thor -g 1001 -G 1000,2000,999 \
#    && printf "thor:thor" | chpasswd \
#    && adduser thor thor \
#    && printf "thor ALL= NOPASSWD: ALL\\n" >> /etc/sudoers

#USER thor
#WORKDIR /home/thor
# RUN mkdir -p /home/thor/.conan
