# Use the latest stable Ubuntu version (currently 16.04)
FROM ubuntu:16.04

# Make sure the image is updated, install some prerequisites,
# Download the latest version of Clang (official binary) for Ubuntu
# Extract the archive and add Clang to the PATH
RUN apt update && DEBIAN_FRONTEND=noninteractive apt install -y \
  xz-utils \
  build-essential \
  curl \
  && rm -rf /var/lib/apt/lists/* \
  && curl -SL http://releases.llvm.org/6.0.0/clang+llvm-6.0.0-x86_64-linux-gnu-ubuntu-16.04.tar.xz \
  | tar -xJC . && \
  mv clang+llvm-6.0.0-x86_64-linux-gnu-ubuntu-16.04 /clang6 && \
  echo 'export PATH=/usr/local/bin:$PATH' >> ~/.bashrc && \
  echo 'export LD_LIBRARY_PATH=/usr/local/:LD_LIBRARY_PATH' >> ~/.bashrc && \
  update-alternatives --install /usr/bin/cc cc /clang6/bin/clang 100 && \
  update-alternatives --install /usr/bin/c++ c++ /clang6/bin/clang++ 100

# Start from a Bash prompt
CMD [ "/bin/bash" ]
