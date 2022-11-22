ARG PYTORCH="1.6.0"
ARG CUDA="10.1"
ARG CUDNN="7"

FROM pytorch/pytorch:${PYTORCH}-cuda${CUDA}-cudnn${CUDNN}-devel

## Use root to install new libraries.
USER root

ENV TORCH_CUDA_ARCH_LIST="6.0 6.1 7.0+PTX"
ENV TORCH_NVCC_FLAGS="-Xfatbin -compress-all"
ENV CMAKE_PREFIX_PATH="$(dirname $(which conda))/../"

RUN apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys A4B469963BF863CC
RUN apt-get update \
    && apt-get install -y apt-utils libssl-dev curl wget vim sudo git clang libomp-dev \
    && apt-get install -y ffmpeg libsm6 libxext6 git ninja-build libglib2.0-0 libsm6 libxrender-dev libxext6 \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Install MMCV
RUN pip install mmcv-full==1.2.7 -f https://download.openmmlab.com/mmcv/dist/cu101/torch1.6/index.html

# Install MMDetection
RUN pip install mmdet==2.10.0

# Create and set the default user.
RUN conda clean --all
RUN groupadd -g 900 -r admin \
    && useradd -u 900 -g admin -r -m admin \
    && echo 'admin:admin' | chpasswd

# Install MMDetection3d
RUN cd /home/admin \
    && git clone https://github.com/open-mmlab/mmdetection3d.git \
    && cd /home/admin/mmdetection3d \
    && git checkout tags/v0.12.0
ENV FORCE_CUDA="1"
RUN cd /home/admin/mmdetection3d \
    && pip install -r requirements/build.txt \
    && pip install --no-cache-dir -v -e . \
    && rm -rf /home/admin/mmdetection3d/build

# uninstall pycocotools installed by nuscenes-devkit and reinstall mmpycocotools
RUN pip uninstall pycocotools --no-cache-dir -y \
    && pip install mmpycocotools --no-cache-dir --force --no-deps

# Add eviroment variables.
SHELL ["/bin/bash", "-c"]
RUN echo 'export LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:/usr/lib:/usr/lib64:/usr/local/lib' >> ~/.bashrc \
    && echo 'export LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:/usr/local/cuda/lib64' >> ~/.bashrc \
    && echo 'alias python=python3' >> ~/.bashrc
RUN source ~/.bashrc
RUN python3 -c "import torch; print('pytorch: version={}, is_gpu_available={}'.format(torch.__version__, torch.cuda.is_available()))"

# Set version information.
MAINTAINER Jun Zhu
LABEL versin="trans_fusion:v001"
LABEL description="cu101_cudnn7_torch160_mmcv-v1.2.7_mmdet-v2.10.0_mmdet3d-v0.12.0"

# Set work directory in the end.
USER admin
WORKDIR /home/admin