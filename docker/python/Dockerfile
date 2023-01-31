FROM nvidia/cuda:11.7.1-cudnn8-devel-ubuntu22.04 AS base
VOLUME /tmp
ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get update && \
  apt-get install -y sudo git python3-pip wget python3-venv libgl1 libglib2.0-0
# Must explicitely install PyTorch for CUDA 11:
# 'Installed CUDA version 11.8 does not match the version torch was compiled with 10.2, unable to compile cuda/cpp extensions without a matching cuda version.'
RUN --mount=type=cache,id=custom-pip,target=/root/.cache/pip pip3 install --pre \
  torch torchvision torchaudio xformer --extra-index-url https://download.pytorch.org/whl/nightly/cu117

RUN useradd -ms /bin/bash docker && echo "docker:docker" | chpasswd && adduser docker sudo && \
    mkdir /opt/stable-diffusion-webui && \
    chown docker /opt/stable-diffusion-webui
USER docker

RUN git clone https://github.com/AUTOMATIC1111/stable-diffusion-webui.git /opt/stable-diffusion-webui
WORKDIR /opt/stable-diffusion-webui
RUN python3 -c 'from launch import prepare_environment; prepare_environment()' --skip-torch-cuda-test
# COPY https://huggingface.co/stabilityai/stable-diffusion-2-1/blob/main/v2-1_768-ema-pruned.ckpt

COPY requirements.txt requirements.txt
RUN --mount=type=cache,id=custom-pip,target=/root/.cache/pip pip3 install -r requirements.txt

FROM base AS dev
COPY requirements-dev.txt requirements-dev.txt
RUN --mount=type=cache,id=custom-pip,target=/root/.cache/pip pip3 install -r requirements-dev.txt
WORKDIR /opt/stable_diffusion/

FROM base AS local
WORKDIR /opt/stable-diffusion-webui
CMD ["python3", "-c", "from launch import start; start()", "--listen", "--no-half"]
