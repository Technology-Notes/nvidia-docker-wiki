## Contents
1. [Description](#description)
1. [GPU isolation](#gpu-isolation)
1. [Running it locally](#running-it-locally)
1. [Running it remotely](#running-it-remotely)

## Description

`nvidia-docker` is a thin wrapper on top of `docker` and act as a drop-in replacement for the `docker` command line interface. This binary is provided as a convenience to automatically detect and setup GPU containers leveraging NVIDIA hardware. Refer to the [internals](https://github.com/NVIDIA/nvidia-docker/wiki/Internals) section if you don't intend to use it.

Internally, `nvidia-docker` calls `docker` and relies on the [NVIDIA Docker plugin](Using nvidia-docker-plugin) to discover driver files and GPU devices. The command used by `nvidia-docker` can be overridden using the environment variable `NV_DOCKER`:
```sh
# Running nvidia-docker with a custom docker command
NV_DOCKER='sudo docker -D' nvidia-docker <docker-options> <docker-command> <docker-args>
```
Note that `nvidia-docker` only modifies the behavior of the `run` and `create` Docker commands. All the other commands are just pass-through to the docker command line interface. As a result, you can't execute GPU code when building a Docker image.

## GPU isolation

GPUs are exported through a list of comma-separated IDs using the environment variable `NV_GPU`. An ID is either the index or the UUID of a given device.  
Device indexes are similar to the ones reported by the `nvidia-docker-plugin` REST interface, `nvidia-smi`, or when running CUDA code with `CUDA_DEVICE_ORDER=PCI_BUS_ID`, it is however **different** from the default CUDA ordering. By default, all GPUs are exported.

```sh
# Running nvidia-docker isolating specific GPUs by index
NV_GPU='0,1' nvidia-docker <docker-options> <docker-command> <docker-args>
# Running nvidia-docker isolating specific GPUs by UUID
NV_GPU='GPU-836c0c09,GPU-b78a60a' nvidia-docker <docker-options> <docker-command> <docker-args>
```

## Running it locally

If the `nvidia-docker-plugin` is installed on your host and running locally, no additional step is needed. `nvidia-docker` will perform what is necessary by querying the plugin when containers using NVIDIA GPUs need to be launched.

## Running it remotely

Using `nvidia-docker` remotely requires `nvidia-docker-plugin` running on the remote host machine.  
The remote host target can be set using the environment variable `DOCKER_HOST` or `NV_HOST`.

The rules are as follows:
* If `NV_HOST` is set then it is used for contacting the plugin.
* If `NV_HOST` is **not** set but `DOCKER_HOST` is set then `NV_HOST` defaults to the `DOCKER_HOST` location  using the `http` protocol on port `3476` (more below)

The specification of `NV_HOST` is defined as:
```
[(http|ssh)://][<ssh-user>@][<host>][:<ssh-port>]:[<http-port>]
```

The `http` protocol requires the `nvidia-docker-plugin` to be listening on a reachable interface (by default `nvidia-docker-plugin` only listens on `localhost`). Opting for `ssh` however, only requires valid SSH credentials (either a password or a private key in your ssh-agent).

```sh
# Run CUDA on the remote host 10.0.0.1 using HTTP
DOCKER_HOST='10.0.0.1:' nvidia-docker run cuda

# Run CUDA on the remote host 10.0.0.1 using SSH
NV_HOST='ssh://10.0.0.1:' nvidia-docker -H 10.0.0.1: run cuda

# Run CUDA on the remote host 10.0.0.1 using SSH with custom user and ports
DOCKER_HOST='10.0.0.1:' NV_HOST='ssh://foo@10.0.0.1:22:80' nvidia-docker run cuda
```