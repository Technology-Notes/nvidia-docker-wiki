## Benefits of GPU containerization

Containerizing GPU applications provides several benefits, among them:

* Ease of deployment
* Isolation of individual devices
* Run across heterogeneous driver/toolkit environments
* Requires only the NVIDIA driver to be installed
* Facilitate collaboration: reproducible builds, reproducible performance, reproducible results.

## Background
Docker® containers are often used to seamlessly deploy CPU-based applications on multiple machines. With this use case, containers are both *hardware-agnostic* and *platform-agnostic*. This is obviously not the case when using NVIDIA GPUs since it is using specialized hardware and it requires the installation of the NVIDIA driver. As a result, Docker Engine does not natively support NVIDIA GPUs with containers.

To solve this problem, one of the early solutions that emerged was to fully reinstall the NVIDIA driver inside the container and then pass the character devices corresponding to the NVIDIA GPUs (e.g. `/dev/nvidia0`) when starting the container. However, this solution was brittle: the version of the host driver had to exactly match driver version installed in the container. The Docker images could not be shared and had to be built locally on each machine, defeating one of the main advantages of Docker.

To make the Docker images portable while still leveraging NVIDIA GPUs, the container images must be agnostic of the NVIDIA driver. This repository provides utilities to enable GPU support inside the container runtime.
