**This section was kept for archiving reasons, it doesn't necessarily reflect nvidia-docker 2.0.**

In this section, we will dive into the internals of nvidia-docker 1.0 to explain the problems we are solving, and how. We will also present other options for solving these problems when possible.  

## Enabling GPU support in your application

This section should give you enough knowledge to be able to integrate NVIDIA GPU support for your own container deployment application, for instance if you don’t want to rely on the nvidia-docker 1.0 CLI wrapper. If this is your goal, be aware that it's actually already possible to avoid relying on the `nvidia-docker` wrapper. Using the [REST API](nvidia-docker-plugin#rest-api) of the `nvidia-docker-plugin` daemon you can launch GPU containers using only `docker`:
```
$ curl -s http://localhost:3476/docker/cli
--device=/dev/nvidiactl --device=/dev/nvidia-uvm --device=/dev/nvidia3 --device=/dev/nvidia2 --device=/dev/nvidia1 --device=/dev/nvidia0 --volume-driver=nvidia-docker --volume=nvidia_driver_361.48:/usr/local/nvidia:ro
$ docker run -ti --rm `curl -s http://localhost:3476/docker/cli` nvidia/cuda nvidia-smi
```
However, this method will not be able to check if the image you are using is compatible with your installed driver, as described in the [image inspection](Image-inspection) subsection. [Remote deployment](Remote-deployment) will also be more challenging.

Another option is to leverage our [`nvidia` package](https://github.com/NVIDIA/nvidia-docker/tree/master/src/nvidia) written in the Go programming language. Go is currently the language of choice for containers, many projects should be able to interface with our package seamlessly.

## In-depth topics
In this section we provide details on the three major services provided by `nvidia-docker`:

* [NVIDIA driver](https://github.com/NVIDIA/nvidia-docker/wiki/NVIDIA-driver-%28version-1.0%29)
* [GPU isolation](https://github.com/NVIDIA/nvidia-docker/wiki/GPU-isolation-%28version-1.0%29)
* [Image inspection](https://github.com/NVIDIA/nvidia-docker/wiki/Image-inspection-%28version-1.0%29)
