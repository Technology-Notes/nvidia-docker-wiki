
#### Backward compatibility

To help transitioning code from 1.0 to 2.0, a bash script is provided in `/usr/bin/nvidia-docker` for backward compatibility.  
It will automatically inject the `--runtime=nvidia` argument and convert `NV_GPU` to `NVIDIA_VISIBLE_DEVICES`.

#### Existing `daemon.json`
If you have a custom `/etc/docker/daemon.json`, the `nvidia-docker2` package will override it.  
In this case, it is recommended to install [nvidia-container-runtime](https://github.com/nvidia/nvidia-container-runtime#installation) instead and register the new runtime manually.

#### Default runtime
The default runtime used by the Docker® Engine is [runc](https://github.com/opencontainers/runc), our runtime can become the default one by configuring the docker daemon with `--default-runtime=nvidia`.
Doing so will remove the need to add the `--runtime=nvidia` argument to `docker run`.
It is also the only way to have GPU access during `docker build`.

#### Environment variables
The behavior of the runtime can be modified through environment variables (such as `NVIDIA_VISIBLE_DEVICES`).   
Those environment variables are consumed by [nvidia-container-runtime](https://github.com/nvidia/nvidia-container-runtime) and are documented [here](https://github.com/nvidia/nvidia-container-runtime#environment-variables-oci-spec).  
Our official CUDA images use default values for these variables.