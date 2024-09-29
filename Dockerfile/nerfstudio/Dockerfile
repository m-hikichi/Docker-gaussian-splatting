FROM pytorch/pytorch:2.2.1-cuda11.8-cudnn8-devel

RUN apt update
RUN apt install -y git
RUN apt install -y ffmpeg libsm6 libxext6

WORKDIR /workspace
RUN git clone -b gaussian_splatting --recursive https://github.com/yzslab/nerfstudio.git

WORKDIR /workspace/nerfstudio
RUN pip install ninja==1.11.1.1
RUN pip install nerfstudio==1.1.4
RUN pip install plyfile==1.1
# RUN pip install ./submodules/diff-gaussian-ersterization
# RUN pip install ./submodules/simple-knn
# RUN pip install -e .
