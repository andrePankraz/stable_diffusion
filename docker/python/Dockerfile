FROM nvidia/cuda:11.7.1-cudnn8-devel-ubuntu22.04 AS base
VOLUME /tmp
ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get update && \
    apt-get install -y sudo git wget python3-pip python3-venv libgl1 libglib2.0-0 && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
# Installed by stable-diffusion-webui prepare_environment():
# Must explicitely install PyTorch for CUDA 11:
# 'Installed CUDA version 11.8 does not match the version torch was compiled with 10.2, unable to compile cuda/cpp extensions without a matching cuda version.'
# RUN --mount=type=cache,id=custom-pip,target=/root/.cache/pip pip3 install --pre \
#     torch torchvision torchaudio --extra-index-url https://download.pytorch.org/whl/nightly/cu117

# Install optional XFormers for speedup (but uses old CUDA version - skip for now):
# Install miniconda for XFormers (see https://github.com/facebookresearch/xformers):
# ENV CONDA_DIR /opt/conda
# RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda.sh && \
#      /bin/bash ~/miniconda.sh -b -p /opt/conda
# Put conda in path so we can use conda activate
# ENV PATH=$CONDA_DIR/bin:$PATH
# RUN conda install xformers -c xformers/label/dev

COPY requirements.txt requirements.txt
RUN --mount=type=cache,id=custom-pip,target=/root/.cache/pip pip3 install -r requirements.txt

RUN useradd -ms /bin/bash docker && echo "docker:docker" | chpasswd && adduser docker sudo
USER docker
WORKDIR /opt/stable-diffusion-webui

RUN git clone https://github.com/AUTOMATIC1111/stable-diffusion-webui.git .
RUN python3 -c 'from launch import prepare_environment; prepare_environment()' --skip-torch-cuda-test

FROM base AS dev
WORKDIR /opt/stable_diffusion/
COPY requirements-dev.txt requirements-dev.txt
RUN --mount=type=cache,id=custom-pip,target=/root/.cache/pip pip3 install -r requirements-dev.txt

FROM base AS local
CMD ["python3", "-c", "from launch import start; start()", "--listen", "--no-half"]
