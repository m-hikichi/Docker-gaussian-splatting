FROM pytorch/pytorch:2.2.1-cuda11.8-cudnn8-devel

# パッケージインストール時に、ユーザーにyes/noや選択肢を求めるプロンプトを一切表示させず、自動的にデフォルト回答を選ぶ
ENV DEBIAN_FRONTEND=noninteractive

# install colmap (https://colmap.github.io/install.html)
RUN apt-get update && apt-get install -y\
    git \
    cmake \
    ninja-build \
    build-essential \
    libboost-program-options-dev \
    libboost-graph-dev \
    libboost-system-dev \
    libeigen3-dev \
    libfreeimage-dev \
    libmetis-dev \
    libgoogle-glog-dev \
    libgtest-dev \
    libgmock-dev \
    libsqlite3-dev \
    libglew-dev \
    qtbase5-dev \
    libqt5opengl5-dev \
    libcgal-dev \
    libceres-dev \
    libcurl4-openssl-dev \
    libmkl-full-dev
RUN apt install -y libtiff-dev
ENV LDFLAGS="-ltiff"
RUN git clone https://github.com/colmap/colmap.git -b 3.12.0
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
