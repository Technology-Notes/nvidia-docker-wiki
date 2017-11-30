## Prerequisites

The list of prerequisites for running nvidia-docker 2.0 is described below.  
For information on how to install Docker for your Linux distribution, please refer to the [Docker documentation](https://docs.docker.com/engine/installation).

1. GNU/Linux x86_64 with kernel version > 3.10
1. Docker >= 1.12
1. NVIDIA GPU with Architecture > Fermi (2.1)
1. [NVIDIA drivers](http://www.nvidia.com/object/unix.html) ~= 361.93 (untested on older versions)

Your driver version might limit your CUDA capabilities (see [CUDA requirements](CUDA#requirements))

## Removing nvidia-docker 1.0

Version 1.0 of the nvidia-docker package must be cleanly removed before continuing.  
You must stop and remove **all** containers started with nvidia-docker 1.0.

#### Ubuntu distributions
```sh
docker volume ls -q -f driver=nvidia-docker | xargs -r -I{} -n1 docker ps -q -a -f volume={} | xargs -r docker rm -f
sudo apt-get purge nvidia-docker
```

#### CentOS distributions

```
docker volume ls -q -f driver=nvidia-docker | xargs -r -I{} -n1 docker ps -q -a -f volume={} | xargs -r docker rm -f
sudo yum remove nvidia-docker
```

## Installing version 2.0

**Make sure you have installed the [NVIDIA driver](https://github.com/NVIDIA/nvidia-docker/wiki/Frequently-Asked-Questions#how-do-i-install-the-nvidia-driver) and a [supported version](https://github.com/NVIDIA/nvidia-docker/wiki/Frequently-Asked-Questions#which-docker-packages-are-supported) of [Docker](https://docs.docker.com/engine/installation/) for your distribution (see [prerequisites](https://github.com/NVIDIA/nvidia-docker/wiki/Installation-(version-2.0)#prerequisites)).**

**If you have a custom `/etc/docker/daemon.json`, the `nvidia-docker2` package might override it.**  

#### Ubuntu distributions

1. Install the repository for your distribution by following the instructions [here](http://nvidia.github.io/nvidia-docker/).
2. Install the `nvidia-docker2` package and reload the Docker daemon configuration:
```
sudo apt-get install nvidia-docker2
sudo pkill -SIGHUP dockerd
```

#### CentOS distributions
1. Install the repository for your distribution by following the instructions [here](http://nvidia.github.io/nvidia-docker/).
2. Install the `nvidia-docker2` package and reload the Docker daemon configuration:
```
sudo yum install nvidia-docker2
sudo pkill -SIGHUP dockerd
```

#### Older versions of Docker
You must pin the versions of both `nvidia-docker2` and `nvidia-container-runtime` when installing, for instance:
```
sudo apt-get install -y nvidia-docker2=2.0.1+docker1.12.6-1 nvidia-container-runtime=1.1.0+docker1.12.6-1
```
Use `apt-cache madison nvidia-docker2 nvidia-container-runtime` or `yum search --showduplicates nvidia-docker2 nvidia-container-runtime` to list the available versions.

## Basic usage
nvidia-docker registers a new container runtime to the Docker daemon.  
You must select the `nvidia` runtime when using `docker run`:
```
docker run --runtime=nvidia --rm nvidia/cuda nvidia-smi
```
