## Contents
1. [Description](#description)
1. [Requirements](#requirements)
1. [Examples](#examples)
1. [Tags available](#tags-available)

## Description
Docker image based on [DIGITS](https://github.com/NVIDIA/DIGITS).

## Requirements
See the requirements for [CUDA](CUDA#requirements) since the DIGITS image is based on a cuDNN runtime image.

## Examples

```sh
# Run DIGITS on host port 5000
nvidia-docker run --name digits -d -p 5000:34448 nvidia/digits
```
If you want to use a dataset stored in a host directory, you will need to import it inside the container using a volume:
```sh
# Run DIGITS with the mnist dataset stored on the host under /opt/mnist
nvidia-docker run --name digits -d -p 5000:34448 -v /opt/mnist:/data/mnist nvidia/digits
```

If you want to persist jobs across multiple DIGITS containers, you can use a named volume. For DIGITS 3.0:
```sh
# Run DIGITS storing jobs in a host volume named digits-jobs

# For DIGITS 3.0
nvidia-docker run --name digits -d -p 5000:34448 -v digits-jobs:/usr/share/digits/digits/jobs nvidia/digits

# For DIGITS 3.3 and above
nvidia-docker run --name digits -d -p 5000:34448 -v digits-jobs:/jobs nvidia/digits
```

## Tags available
Check the [DockerHub](https://hub.docker.com/r/nvidia/digits/)

