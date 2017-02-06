## Contents
1. [Prerequisites](#prerequisites)
1. [Installing from binaries](#installing-from-binary-packages)
1. [Building from sources](#building-from-sources)

## Prerequisites

The list of prerequisites for running `nvidia-docker` is described below.  
For information on how to install Docker for your Linux distribution, please refer to the [Docker documentation](https://docs.docker.com/engine/installation).

1. GNU/Linux x86_64 with kernel version > 3.10
1. Docker >= 1.9 (official `docker-engine` only)
1. NVIDIA GPU with Architecture > Fermi (2.1)
1. [NVIDIA drivers](http://www.nvidia.com/object/unix.html) >= 340.29 with binary `nvidia-modprobe`

Your driver version might limit your CUDA capabilities (see [CUDA requirements](CUDA#requirements))

## Installing from binary packages

Binary packages are available for download on the [release page](https://github.com/NVIDIA/nvidia-docker/releases).

The package installation will automatically setup the `nvidia-docker-plugin` and depending on your distribution, register it with the init system.

## Building from sources

At the root directory of the repository issue `make` or `make install` to build the binaries.  
The later will also take care of installing the binaries at the location set by the environment variable `prefix`(`/usr/local` by default). You must start `nvidia-docker-plugin` manually with this method:
```
sudo -b nohup nvidia-docker-plugin
```

Alternatively, you can use `make deb` or `make rpm` to generate deb/rpm packages for your distribution.  
The package installation will automatically setup the `nvidia-docker-plugin` and depending on your distribution, register it with the init system.

`make clean` and `make distclean` take care of cleaning the build and distribution environment respectively.