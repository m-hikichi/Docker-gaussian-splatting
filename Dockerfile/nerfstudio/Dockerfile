FROM nvidia/cuda:11.8.0-cudnn8-devel-ubuntu22.04

RUN ln -sf /usr/share/zoneinfo/Asia/Tokyo /etc/localtime
RUN apt update
RUN apt install -y python3.10-dev python3-pip
RUN apt install -y git
RUN apt install -y ffmpeg libgl1-mesa-glx libglib2.0-0 libsm6 libxrender1 libxext6

WORKDIR /workspace
RUN git clone -b gaussian_splatting --recursive https://github.com/yzslab/nerfstudio.git

WORKDIR /workspace/nerfstudio
RUN pip install torch==2.0.1+cu118 torchvision==0.15.2+cu118 --index-url https://download.pytorch.org/whl/cu118
RUN pip install ninja==1.11.1.1
RUN pip install nerfstudio==1.1.4
RUN pip install plyfile==1.1
# RUN pip install ./submodules/diff-gaussian-ersterization
# RUN pip install ./submodules/simple-knn
# RUN pip install --upgrade pip setuptools
# RUN pip install -e .
