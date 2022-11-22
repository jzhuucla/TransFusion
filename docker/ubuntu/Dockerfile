FROM nvidia/cuda:11.3.1-cudnn8-devel-ubuntu20.04

## Use root to install new libraries.
USER root

## Update source list.
RUN echo 'debconf debconf/frontend select Noninteractive' | debconf-set-selections
RUN apt update \
    && apt-get clean \
    && apt-get update

## Install common prerequisites.
RUN apt-get install apt-utils libssl-dev curl wget vim sudo git clang python3-dev python3-pip python3-tk yapf3 python3-yapf libomp-dev -y
RUN apt-get install ffmpeg libsm6 libxext6 ninja-build libglib2.0-0 libsm6 libxrender-dev libxext6 libopenblas-dev -y

## Install pytorch.
RUN pip3 install numpy pandas PyYAML scipy scikit-learn h5py plyfile tqdm matplotlib addict protobuf Markdown requests google-pasta typing_extensions dataclasses future gast grpcio opt-einsum termcolor Werkzeug wrapt ninja
RUN pip3 install torch==1.10.2+cu113 torchvision==0.11.3+cu113 torchaudio==0.10.2+cu113 -f https://download.pytorch.org/whl/cu113/torch_stable.html

## Install MMCV
RUN pip3 install mmcv-full==1.4.0 -f https://download.openmmlab.com/mmcv/dist/cu113/torch1.10/index.html

## Install mmdetection
RUN pip3 install mmdet

## Install mmsegmentation
RUN pip3 install mmsegmentation

## Install mmdetection3d
# Install Minkowski Enginey
RUN pip3 install git+https://github.com/NVIDIA/MinkowskiEngine -v --no-deps --install-option="--blas_include_dirs=/opt/conda/include" --install-option="--blas=openblas"
# Install dependencies
ENV FORCE_CUDA="1"
RUN pip3 install cumm-cu113 spconv-cu113

# Create and set the default user.
RUN groupadd -g 900 -r admin \
    && useradd -u 900 -g admin -r -m admin \
    && echo 'admin:admin' | chpasswd
USER admin

# Add eviroment variables.
SHELL ["/bin/bash", "-c"]
RUN echo 'export LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:/usr/lib:/usr/lib64:/usr/local/lib' >> ~/.bashrc \
    && echo 'export LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:/usr/local/cuda/lib64' >> ~/.bashrc \
    && echo 'alias python=python3' >> ~/.bashrc
RUN source ~/.bashrc
RUN python3 -c "import torch; print('pytorch: version={}, is_gpu_available={}'.format(torch.__version__, torch.cuda.is_available()))"

# Set version information.
MAINTAINER Jun Zhu
LABEL versin="mmdet3d-v0.12.0_v0.0.1"
LABEL description="Docker image for ubuntu Linux OS."

# Set work directory in the end.
WORKDIR /home/admin

#RUN mkdir /software \
#    && cd /software \
#    && git clone https://github.com/open-mmlab/mmdetection3d.git \
#    && cd /software/mmdetection3d \
#    && git checkout tags/v0.12.0 \
#    && pip3 install -r requirements/build.txt \
#    && pip3 install --no-cache-dir -v -e .
#    && rm -rf /software/mmdetection3d/build

# uninstall pycocotools installed by nuscenes-devkit and reinstall mmpycocotools
#RUN pip3 uninstall pycocotools --no-cache-dir -y
#RUN pip3 install mmpycocotools --no-cache-dir --force --no-deps
