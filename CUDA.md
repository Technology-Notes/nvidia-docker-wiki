## Contents
1. [Description](#description)
1. [Requirements](#requirements)
1. [Examples](#examples)
1. [Tags available](#tags-available)

## Description

CUDA images come in two flavors and are available through the [NVIDIA public hub repository](https://hub.docker.com/r/nvidia/cuda).

```runtime```: a lightweight image containing the bare minimum to deploy a pre-built application which uses CUDA.  
```devel```: extends the runtime image by adding the compiler toolchain, the debugging tools and the development files for the standard CUDA libraries. Use this image to compile a CUDA application from source.

## Requirements

Running a CUDA container requires a machine with at least one CUDA-capable GPU and a driver compatible with the CUDA toolkit version you are using.  
The machine running the CUDA container **only requires the NVIDIA driver**, the CUDA toolkit doesn't have to be installed.

NVIDIA drivers are **backward-compatible** with CUDA toolkits versions

CUDA toolkit version   | Driver version  | GPU architecture
:---------------------:|:-----------------------:|:-------------------------:
  6.5                  | >= 340.29               | >= 2.0 (Fermi)
  7.0                  | >= 346.46               | >= 2.0 (Fermi)
  7.5                  | >= 352.39               | >= 2.0 (Fermi)
  8.0                  | == 361.93 or >= 375.51  | == 6.0 (P100)
  8.0                  | >= 367.48               | >= 2.0 (Fermi)
  9.0                  | >= 384.81               | >= 3.0 (Kepler)


## Examples
Take a look at the [samples section](Testing-the-samples) to find examples of Dockerfiles compiling simple CUDA applications.

```sh
# Running an interactive CUDA session isolating the first GPU
NV_GPU=0 nvidia-docker run -ti --rm nvidia/cuda

# Querying the CUDA 7.5 compiler version
nvidia-docker run --rm nvidia/cuda:7.5-devel nvcc --version
```

## Tags available
Check the [DockerHub](https://hub.docker.com/r/nvidia/cuda/)
