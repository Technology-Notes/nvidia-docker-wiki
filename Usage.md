#### NVIDIA runtime
nvidia-docker registers a new container runtime to the Docker daemon.  
You must select the `nvidia` runtime when using `docker run`:
```
docker run --runtime=nvidia --rm nvidia/cuda nvidia-smi
```

#### GPU isolation
Set the environment variable `NVIDIA_VISIBLE_DEVICES` in the container:
```
docker run --runtime=nvidia -e NVIDIA_VISIBLE_DEVICES=0 --rm nvidia/cuda nvidia-smi
```

#### Non-CUDA image
Setting `NVIDIA_VISIBLE_DEVICES` will enable GPU support for any container image:
```
docker run --runtime=nvidia -e NVIDIA_VISIBLE_DEVICES=all --rm debian:stretch nvidia-smi
```

#### Dockerfiles
If the environment variables are set inside the Dockerfile, you don't need to set them on the `docker run` command-line.

For instance, if you are creating a custom CUDA container from scratch: 
```dockerfile
ENV NVIDIA_VISIBLE_DEVICES all
ENV NVIDIA_DRIVER_CAPABILITIES compute,utility
```
These environment variables are already set in our official images pushed to [Docker Hub](https://gitlab.com/nvidia/cuda/blob/ubuntu16.04/9.0/base/Dockerfile#L31-32).

