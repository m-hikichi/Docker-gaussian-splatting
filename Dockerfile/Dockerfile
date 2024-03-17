FROM pytorch/pytorch:2.2.1-cuda11.8-cudnn8-devel

RUN apt-get update && apt-get install -y git

RUN git clone https://github.com/graphdeco-inria/gaussian-splatting --recursive
# WORKDIR /workspace/gaussian-splatting/submodules/diff-gaussian-rasterization
# RUN python setup.py install
# WORKDIR /workspace/gaussian-splatting/submodules/simple-knn
# RUN python setup.py install
RUN pip install plyfile
