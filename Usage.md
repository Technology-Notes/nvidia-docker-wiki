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

#### Non-CUDA image:
Setting `NVIDIA_VISIBLE_DEVICES` will enable GPU support for any container image:
```
docker run --runtime=nvidia -e NVIDIA_VISIBLE_DEVICES=all --rm debian:stretch nvidia-smi
```
