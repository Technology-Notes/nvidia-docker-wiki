## Description
Docker image based on [NVIDIA/caffe](https://github.com/NVIDIA/caffe), NVIDIA's fork of [BVLC/caffe](https://github.com/BVLC/caffe).

## Requirements
See the requirements for [CUDA](CUDA#requirements) since the Caffe image is based on a cuDNN runtime image.

## Examples
This image can be used as a base image for other images like [[DIGITS]], or it can be used directly to train networks:
```sh
# Run a Caffe training job
docker run --rm --runtime=nvidia nvidia/caffe caffe train --solver <args>
```

## Tags available
Check the [DockerHub](https://hub.docker.com/r/nvidia/caffe/)