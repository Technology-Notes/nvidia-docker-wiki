## Prerequisites

The list of prerequisites for running nvidia-docker 2.0 is described below.  
For information on how to install Docker for your Linux distribution, please refer to the [Docker documentation](https://docs.docker.com/engine/installation).

1. GNU/Linux x86_64 with kernel version > 3.10
1. Docker >= 19.03
1. NVIDIA GPU with Architecture > Fermi (2.1)
1. [NVIDIA drivers](http://www.nvidia.com/object/unix.html) ~= 361.93 (untested on older versions)

Your driver version might limit your CUDA capabilities (see [CUDA requirements](CUDA#requirements))

## Installing version 2.0

**Make sure you have installed the [NVIDIA driver](https://github.com/NVIDIA/nvidia-docker/wiki/Frequently-Asked-Questions#how-do-i-install-the-nvidia-driver) and a [supported version](https://github.com/NVIDIA/nvidia-docker/wiki/Frequently-Asked-Questions#which-docker-packages-are-supported) of [Docker](https://docs.docker.com/engine/installation/) for your distribution (see [prerequisites](https://github.com/NVIDIA/nvidia-docker/wiki/Installation-(version-2.0)#prerequisites)).**


1. Install the repository for your distribution by following the instructions [here](http://nvidia.github.io/nvidia-docker/).
2. Install the `nvidia-container-toolkit` package:
```
$ sudo apt-get install -y nvidia-container-toolkit
$ sudo yum install -y nvidia-container-toolkit
```

## Usage
The NVIDIA Container Toolkit is integrated with the Docker CLI and it can be used pretty seemlessly
```
# Starting a GPU enabled container
$ docker run --gpus all nvidia/cuda nvidia-smi

# Start a GPU enabled container on two GPUs
$ docker run --gpus 2 nvidia/cuda nvidia-smi

# Starting a GPU enabled container on specific GPUs
$ docker run --gpus device=1,2 nvidia/cuda nvidia-smi
$ docker run --gpus device=UUID-ABCDEF,1 nvidia/cuda nvidia-smi

# Specifying a capability (graphics, compute, ...) for my container
# Note this is rarely if ever used this way
$ docker run --gpus all,capabilities=utilities nvidia/cuda nvidia-smi
```

#### Non-CUDA image
Setting `NVIDIA_VISIBLE_DEVICES` will enable GPU support for any container image:
```
docker run --gpus all,capabilities=utilities --rm debian:stretch nvidia-smi
```

#### Dockerfiles
If the environment variables are set inside the Dockerfile, you don't need to set them on the `docker run` command-line.

For instance, if you are creating your own custom CUDA container, you should use the following:
```dockerfile
ENV NVIDIA_VISIBLE_DEVICES all
ENV NVIDIA_DRIVER_CAPABILITIES compute,utility
```
These environment variables are already set in our official images pushed to [Docker Hub](https://gitlab.com/nvidia/cuda/blob/ubuntu16.04/9.0/base/Dockerfile#L31-32).

For a Dockerfile using the NVIDIA Video Codec SDK, you should use:
```dockerfile
ENV NVIDIA_VISIBLE_DEVICES all
ENV NVIDIA_DRIVER_CAPABILITIES compute,video,utility
```
