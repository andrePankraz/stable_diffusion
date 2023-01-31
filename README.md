<!---
This file was created by ]init[ AG 2023.
-->

# Stable Diffusion Web-UI in a Docker Container

Setup Docker container for [Stable Diffusion Web UI](https://github.com/AUTOMATIC1111/stable-diffusion-webui).
This container encapsulates all necessary Python and CUDA components in a Docker Container.

The tests are performed in a Docker container that also works in the Windows Subsystem for Linux (WSL).
An NVIDIA graphics card with at least 8 GB VRAM is recommended, depending on the models used.
CUDA is part of the Docker image, only the NVIDIA graphics driver needs to be installed.

Docker must have CUDA enabled (e.g. for WSL see https://docs.nvidia.com/cuda/wsl-user-guide/index.html).

## Prepare

- Clone https://github.com/andrePankraz/stable_diffusion

      $ git clone git@github.com:andrePankraz/stable_diffusion.git
      $ cd stable_diffusion

- Download a Stable Diffusion Model into folder 'stable_diffusion/models/Stable-diffusion'
  - e.g.: https://huggingface.co/stabilityai/stable-diffusion-2-1/blob/main/v2-1_768-ema-pruned.ckpt

## Start for usage

- Start container:

      $ export DOCKER_BUILDKIT=1
      $ docker compose up

  - Will take some time at first start (images & packages are downloaded, >10 GB)
  - Wait & check if up and running
- Go to URL: http://localhost:7860/
  - Some functions will take some time at first usage (models are downloaded, several GB)

## Start for development

- Start container in development mode:

      $ export DOCKER_BUILDKIT=1
      $ docker compose --env-file docker/.envs/dev.env up

  - Will take some time at first start (images & packages are downloaded, >10 GB)
  - Wait & check if up and running
- Install [VS Code](https://code.visualstudio.com/)
  - Install Extension
    - Dev Containers
    - Docker
    - Markdown All in One
- Attach VS Code to Docker Container
  - Attach to running containers... (Lower left edge in VS Code)
    - select stable_diffusion-python-1
  - Explorer Open folder -> /opt/stable_diffusion
  - Run / Start Debug
    - VS Code Extension Python will be installed the first time (Wait and another Start Debug)
    - Select Python Interpreter
- Start Stable Diffusion Web-UI via Terminal:

      $ cd /opt/stable-diffusion-webui
      $ python3 -c 'from launch import start; start()' --listen --no-half
- Go to URL: http://localhost:7860/
  - Some functions will take some time at first usage (models are downloaded, several GB)
