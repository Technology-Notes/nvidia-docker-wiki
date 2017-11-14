## Prerequisites

The list of prerequisites for running `nvidia-docker` is described below.  
For information on how to install Docker for your Linux distribution, please refer to the [Docker documentation](https://docs.docker.com/engine/installation).

1. GNU/Linux x86_64 with kernel version > 3.10
1. Docker >= 1.9 (official `docker-engine`, `docker-ce` or `docker-ee` only)
1. NVIDIA GPU with Architecture > Fermi (2.1)
1. [NVIDIA drivers](http://www.nvidia.com/object/unix.html) >= 340.29 with binary `nvidia-modprobe`

Your driver version might limit your CUDA capabilities (see [CUDA requirements](CUDA#requirements))

## Installing version 1.0

#### Ubuntu distributions

1. Install the repository for your distribution by following the instructions [here](http://nvidia.github.io/nvidia-docker/).
2. Install the `nvidia-docker` package
```
sudo apt-get install nvidia-docker
```

#### CentOS distributions
1. Install the repository for your distribution by following the instructions [here](http://nvidia.github.io/nvidia-docker/).
2. Install the `nvidia-docker` package
```
sudo yum install nvidia-docker
```
The package installation will automatically setup the `nvidia-docker-plugin` and depending on your distribution, register it with the init system.

## Basic usage
```
nvidia-docker run --rm nvidia/cuda nvidia-smi
```