# ARG CUDA_VERSION=10.2
# ARG CUDNN_VERSION=7.6.5
# ARG PYTHON_VERSION=3.6.9
# ARG OPENCV_VERSION=3.4.0
# ARG GCC_VERSION=7.4.0
# 18.04.1-Ubuntu

FROM nvcr.io/nvidia/caffe:20.03-py3

LABEL maintainer="https://github.com/ZiliPeng"

# Needed for string substitution
SHELL ["/bin/bash", "-c"]

# Upgrade CMake
RUN wget https://github.com/Kitware/CMake/releases/download/v3.14.7/cmake-3.14.7-Linux-x86_64.tar.gz && \
    tar -xvf cmake-3.14.7-Linux-x86_64.tar.gz && \
    mv cmake-3.14.7-Linux-x86_64 /opt && \
    echo "export PATH=/opt/cmake-3.14.7-Linux-x86_64/bin:$PATH" >> /etc/profile && \
    . /etc/profile && \
    rm cmake-3.14.7-Linux-x86_64.tar.gz

# Install Caffe
ENV PATH=/opt/cmake-3.14.7-Linux-x86_64/bin:$PATH
ENV CAFFE_ROOT=/opt/cmu_caffe
WORKDIR $CAFFE_ROOT

RUN  cd /opt && \
    git clone https://github.com/CMU-Perceptual-Computing-Lab/caffe.git && \
    cd $CAFFE_ROOT && \
    sed -i 's/set(python_version "2"/set(python_version "3"/g' CMakeLists.txt && \
    # sed -i 's/set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fPIC -Wall")/set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fPIC -Wall -std=c++11")/g' CMakeLists.txt && \
    # sed -i 's/cudnn.h CUDNN_VERSION_FILE_CONTENTS/cudnn_version.h CUDNN_VERSION_FILE_CONTENTS/g' cmake/Cuda.cmake && \
    mkdir build && cd build && \
    cmake -DUSE_CUDNN=1 -DUSE_NCCL=ON -DUSE_OPENMP=1 .. && \
    make -j"$(nproc)"  && \
    make pycaffe -j"$(nproc)"

ENV PYCAFFE_ROOT $CAFFE_ROOT/python
ENV PYTHONPATH $PYCAFFE_ROOT:$PYTHONPATH
ENV PATH $CAFFE_ROOT/build/tools:$PYCAFFE_ROOT:$PATH
RUN echo "$CAFFE_ROOT/build/lib" >> /etc/ld.so.conf.d/caffe.conf && ldconfig

WORKDIR /workspace
