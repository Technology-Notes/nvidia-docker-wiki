We now recommend our users to transition to nvidia-docker 2.0. Version 1.0 is now deprecated.\
In order to ease the transition, a set of scripts is provided for [backward compatibility](https://github.com/NVIDIA/nvidia-docker/wiki/Advanced-topics#backward-compatibility).

## Differences with 1.0
* Doesn't require wrapping the Docker CLI and doesn't need a separate daemon.
* Can be integrated seamlessly into the Docker ecosystem (e.g. Swarmkit, Compose).
* GPU isolation is now achieved with a container environment variable `NVIDIA_VISIBLE_DEVICES` (as opposed to `NV_GPU`).
* Can enable GPU support for any Docker image. Not just the ones based on our official CUDA images.
* Removes the [HTTP monitoring](https://github.com/NVIDIA/nvidia-docker/wiki/nvidia-docker-plugin#rest-api) feature. We recommend using other tools for this purpose (e.g. [DCGM](https://www.nvidia.com/object/data-center-gpu-manager.html))
* Package repositories are available for Ubuntu and CentOS.
* Uses a new implementation based on [libnvidia-container](https://github.com/NVIDIA/libnvidia-container).
* Addresses [known limitations](https://github.com/NVIDIA/nvidia-docker/wiki/nvidia-docker-plugin#known-limitations).
* Brings additional features and improvements (see [[Advanced topics]]).