## Description

CUDA images come in three flavors and are available through the [NVIDIA public hub repository](https://hub.docker.com/r/nvidia/cuda).

* ```base```: starting from CUDA 9.0, contains the bare minimum (libcudart) to deploy a pre-built CUDA application.  
Use this image if you want to manually select which CUDA packages you want to install. 
* ```runtime```: extends the `base` image by adding all the shared libraries from the CUDA toolkit.  
Use this image if you have a pre-built application using multiple CUDA libraries.
* ```devel```: extends the `runtime` image by adding the compiler toolchain, the debugging tools, the headers and the static libraries.  
Use this image to compile a CUDA application from sources.

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
  9.1                  | >= 387.26               | >= 3.0 (Kepler)
  9.2                  | >= 396.26               | >= 3.0 (Kepler)
  10.0                 | >= 384.111, < 385.00    | [Tesla GPUs](https://docs.nvidia.com/cuda/cuda-c-best-practices-guide/index.html#flexible-upgrade-path)
  10.0                 | >= 410.48               | >= 3.0 (Kepler)
  10.1                 | >= 384.111, < 385.00    | [Tesla GPUs](https://docs.nvidia.com/cuda/cuda-c-best-practices-guide/index.html#flexible-upgrade-path)
  10.1                 | >=410.72, < 411.00      | [Tesla GPUs](https://docs.nvidia.com/cuda/cuda-c-best-practices-guide/index.html#flexible-upgrade-path)
  10.1                 | >= 418.39               | >= 3.0 (Kepler)

## Examples

```sh
# Running an interactive CUDA session isolating the first GPU
docker run -ti --rm --runtime=nvidia -e NVIDIA_VISIBLE_DEVICES=0 nvidia/cuda

# Querying the CUDA 7.5 compiler version
docker run --rm --runtime=nvidia nvidia/cuda:7.5-devel nvcc --version
```

## Tags available
Check the [DockerHub](https://hub.docker.com/r/nvidia/cuda/)
