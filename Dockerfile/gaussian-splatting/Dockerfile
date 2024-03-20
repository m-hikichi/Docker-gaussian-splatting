FROM pytorch/pytorch:2.2.1-cuda11.8-cudnn8-devel

# install colmap (https://colmap.github.io/install.html)
RUN apt-get update && apt-get install -y\
    git \
    cmake \
    ninja-build \
    build-essential \
    libboost-program-options-dev \
    libboost-filesystem-dev \
    libboost-graph-dev \
    libboost-system-dev \
    libeigen3-dev \
    libflann-dev \
    libfreeimage-dev \
    libmetis-dev \
    libgoogle-glog-dev \
    libgtest-dev \
    libsqlite3-dev \
    libglew-dev \
    qtbase5-dev \
    libqt5opengl5-dev \
    libcgal-dev \
    libceres-dev
RUN git clone https://github.com/colmap/colmap.git
WORKDIR /workspace/colmap/build
# RUN cmake .. -GNinja && \
#     ninja && \
#     ninja install

# install gaussian-splatting
WORKDIR /workspace
RUN pip install plyfile==1.0.3
RUN git clone https://github.com/graphdeco-inria/gaussian-splatting --recursive
# WORKDIR /workspace/gaussian-splatting/submodules/diff-gaussian-rasterization
# RUN python setup.py install
# WORKDIR /workspace/gaussian-splatting/submodules/simple-knn
# RUN python setup.py install
