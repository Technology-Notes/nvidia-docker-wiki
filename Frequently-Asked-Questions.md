## Setting up

#### How do I register the new runtime to the Docker daemon?
Refer to the documentation of [nvidia-container-runtime](https://github.com/nvidia/nvidia-container-runtime#docker-engine-setup)

#### Which Docker packages are supported?
* All the stable releases of [`docker-ce`](https://docs.docker.com/release-notes/docker-ce/).
* Edge releases are not supported.
* The legacy official package `docker-engine`.
* The package provided by Canonical: `docker.io`.
* The package provided by Red Hat: `docker`.

#### What is the minimum supported Docker version?
Docker 1.12 which adds support for [custom container runtimes](https://github.com/moby/moby/pull/22983).

#### How do I install the NVIDIA driver?
The recommended way is to use your [package manager](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#package-manager-installation) and install the `cuda-drivers` package (or equivalent).\
When no packages are available, you should use an official [“runfile”](http://www.nvidia.com/object/unix.html).

## Platform support

#### Is macOS supported?
No, we do not support macOS (regardless of the version), however you can use the native macOS Docker client to deploy your containers remotely (refer to the [dockerd documentation](https://docs.docker.com/engine/reference/commandline/dockerd/#description)).

#### Is Microsoft Windows supported?
No, we do not support Microsoft Windows (regardless of the version), however you can use the native Microsoft Windows Docker client to deploy your containers remotely (refer to the [dockerd documentation](https://docs.docker.com/engine/reference/commandline/dockerd/#description)).

#### Do you support Microsoft native container technologies (e.g. Windows server, Hyper-v)?
No, we do not support native Microsoft container technologies.

#### Do you support Optimus (i.e. NVIDIA dGPU + Intel iGPU)?
Yes, from the CUDA perspective there is no difference as long as your dGPU is powered-on and you are following the official driver instructions.

#### Do you support Tegra platforms (arm64)?
No, we do not support Tegra platforms and can’t easily port the code to it.\
The driver stack on arm64 is radically different and would require a complete architecture overhaul.

#### What distributions are officially supported?
For your host distribution, the list of supported platforms is available [here](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#system-requirements).\
For your container images, both the [Docker Hub](https://github.com/NVIDIA/nvidia-docker/wiki/Docker-Hub) and [NGC registry](https://github.com/NVIDIA/nvidia-docker/wiki/NGC) images are officially supported.

#### Do you support PowerPC64 (ppc64)?
Not yet for 2.0 but are actively working with IBM on this.

#### How do I use this in on my Cloud service provider (e.g. AWS, Azure, GCP)?
We have a tutorial for [AWS](https://github.com/NVIDIA/nvidia-docker/wiki/Deploy-on-Amazon-EC2) and a tutorial for [Azure](https://github.com/NVIDIA/nvidia-docker/wiki/Deploy-on-Azure).
They haven’t been updated for 2.0 yet but we are working on it and we plan to release a similar tutorial for GCP soon.\
Alternatively, you can leverage [NGC](https://github.com/NVIDIA/nvidia-docker/wiki/NGC) to deploy optimized container images on AWS.

## Container Runtime

#### Does it have a performance impact on my GPU workload? 
No, usually the impact should be in the order of magnitude less than 1% and hardly noticeable.\
However be aware of the following (list non exhaustive):
* _GPU topology and CPU affinity_ \
You can query it using `nvidia-smi topo` and use [Docker CPU sets](https://docs.docker.com/engine/admin/resource_constraints/#cpu) to pin CPU cores.
* _Compiling your code for your device architecture_\
Your container might be compiled for the wrong achitecture and could fallback to the JIT compilation of PTX code (refer to the [official documentation](http://docs.nvidia.com/cuda/cuda-compiler-driver-nvcc/index.html#gpu-compilation) for more information).\
Note that you can express [these constraints](https://github.com/nvidia/nvidia-container-runtime#nvidia_require_) in your container image.
* _Container I/O overhead_\
By default Docker containers rely on an overlay filesystem and bridged/NATed networking.\
Depending on your workload this can be a bottleneck, we recommend using [Docker volumes](https://docs.docker.com/engine/admin/volumes/volumes/) and experiment with different [Docker networks](https://docs.docker.com/engine/userguide/networking/).
* _Linux kernel accounting and security overhead_\
In rare cases, you may notice than some kernel subsystems induce overhead.\
This will likely depend on your kernel version and can include things like: cgroups, LSMs, seccomp filters, netfilter...

#### Is OpenGL supported?
No, OpenGL is not supported at the moment and there is no plan to support OpenGL+GLX in the near future.\
OpenGL+EGL however will be supported and [this issue](https://github.com/NVIDIA/nvidia-docker/issues/11) will be updated accordingly.\
If you are a [NGC](https://github.com/NVIDIA/nvidia-docker/wiki/NGC) subscriber and require GLX for your workflow, please fill out a [feature request](https://devtalk.nvidia.com/default/board/221/feature-requests/) for support consideration.

#### How do I fix `unsatisfied condition: cuda >= X.Y`?
Your CUDA container image is incompatible with your driver version.\
Upgrade your driver or choose an [image tag](https://hub.docker.com/r/nvidia/cuda/) which is supported by your driver (see also [CUDA requirements](https://github.com/NVIDIA/nvidia-docker/wiki/CUDA#requirements))

#### Do you support CUDA Multi Process Service (a.k.a. MPS)?
No, MPS is not supported at the moment. However we plan on supporting this feature in the future, and [this issue](https://github.com/NVIDIA/nvidia-docker/issues/419) will be updated accordingly.

#### Do you support running a GPU-accelerated X server inside the container?
No, running a X server inside the container is not supported at the moment and there is no plan to support it in the near future (see also [OpenGL support](#is-opengl-supported)).

#### I have multiple GPU devices, how can I isolate them between my containers?
GPU isolation is achieved through a container environment variable called `NVIDIA_VISIBLE_DEVICES`.\
Devices can be referenced by index (following the PCI bus order) or by UUID (refer to the [documentation](https://github.com/nvidia/nvidia-container-runtime#nvidia_visible_devices)).

#### Why is `nvidia-smi` inside the container not listing the running processes?
`nvidia-smi` and NVML are not compatible with [PID namespaces](http://man7.org/linux/man-pages/man7/pid_namespaces.7.html).\
We recommend monitoring your processes on the host or inside a container using `--pid=host`.

#### Can I share a GPU between multiple containers?
Yes. This is no different than sharing a GPU between multiple processes outside of containers.\
Scheduling and compute preemption vary from one GPU architecture to another (e.g. CTA-level, instruction-level).

#### Can I limit the GPU resources (e.g. bandwidth, memory, CUDA cores) taken by a container?
No. Your only option is to set the GPU clocks at a lower frequency before starting the container.

#### Can I enforce exclusive access for a GPU?
This is not currently supported but you can enforce it:
* At the container orchestration layer (Kubernetes, Swarm, Mesos, Slurm…) since this is tied to resource allocation.
* At the driver level by setting the [compute mode](http://docs.nvidia.com/cuda/cuda-c-programming-guide/index.html#compute-modes) of the GPU.

#### Why is my container slow to start with 2.0?
You probably need to enable [persistence mode](http://docs.nvidia.com/deploy/driver-persistence/index.html) to keep the kernel modules loaded and the GPUs initialized.\
The recommended way is to start the `nvidia-persistenced` daemon on your host.

#### Can I use it with Docker-in-Docker (a.k.a. DinD)?
If you are running a Docker client inside a container: simply mount the Docker socket proceed as usual.\
If you are running a Docker daemon inside a container: this case is untested.

#### Why is my application inside the container slow to initialize?
Your application was probably not compiled for the compute architecture of your GPU and thus the driver must [JIT](https://devblogs.nvidia.com/parallelforall/cuda-pro-tip-understand-fat-binaries-jit-caching/) all the CUDA kernels from PTX.
In addition to a slow start, the JIT compiler might generate less efficient code than directly targeting your compute architecture (see also [performance impact](#does-it-have-a-performance-impact-on-my-gpu-workload)).

#### Is the JIT cache shared between containers?
No. You would have to handle this manually with [Docker volumes](https://docs.docker.com/engine/admin/volumes/volumes/).

#### What is causing the CUDA `invalid device function` error?
Your application was not compiled for the compute architecture of your GPU, and no PTX was generated during build time. Thus, JIT compiling is impossible (see also [slow to initialize](#why-is-my-application-inside-the-container-slow-to-initialize)).

#### Why do I get `Insufficient Permissions` for some `nvidia-smi` operations?
Some device management operations require extra privileges (e.g. setting clocks frequency).\
After learning about the security implications of doing so, you can add extra [capabilities](https://docs.docker.com/engine/security/security/#linux-kernel-capabilities) to your container using `--cap-add` on the command-line (`--cap-add=SYS_ADMIN` will allow most operations).

#### Can I profile and debug my GPU code inside a container?
Yes but as stated above, you might need extra privileges, meaning extra [capabilities](https://docs.docker.com/engine/security/security/#linux-kernel-capabilities) like `CAP_SYS_PTRACE` or tweak the [seccomp profile](https://docs.docker.com/engine/security/seccomp/) used by Docker to allow certain syscalls.

#### Is OpenCL supported?
No, OpenCL is not supported at the moment. However we plan on supporting this feature in the future.

#### Is Vulkan supported?
No, Vulkan is not supported at the moment. However we plan on supporting this feature in the future.

## Container images

#### What do I have to install in my container images?
Library dependencies vary from one application to another. In order to make things easier for developers, we provide a set of [official images](#do-you-provide-official-docker-images) to base your images on.

#### Do you provide official Docker images?
Yes, container images are available on [Docker Hub](https://github.com/NVIDIA/nvidia-docker/wiki/Docker-Hub) and on the [NGC registry](https://github.com/NVIDIA/nvidia-docker/wiki/NGC).

#### Can I use the GPU during a container build (i.e. `docker build`)?
Yes, as long as you [configure your Docker daemon](https://github.com/NVIDIA/nvidia-docker/wiki/Advanced-topics#default-runtime) to use the `nvidia` runtime as the default, you will be able to have build-time GPU support. However, be aware that this can render your images non-portable (see also [invalid device function](#what-is-causing-the-cuda-invalid-device-function-error)).

#### Are my container images built for version 1.0 compatible with 2.0?
Yes, for most cases. The main difference being that we don’t mount all driver libraries by default in 2.0. You might need to set the `CUDA_DRIVER_CAPABILITIES` environment variable in your Dockerfile or when starting the container. Check the documentation of [nvidia-container-runtime](https://github.com/nvidia/nvidia-container-runtime#environment-variables-oci-spec).

#### How do I link against driver APIs at build time (e.g. `libcuda.so` or `libnvidia-ml.so`)?
Use the library stubs provided in `/usr/local/cuda/lib64/stubs/`. Our official images already take care of setting [`LIBRARY_PATH`](https://gitlab.com/nvidia/cuda/blob/ubuntu16.04/9.0/devel/Dockerfile#L12).
However, do not set `LD_LIBRARY_PATH` to this folder, the stubs must not be used at runtime.

#### The official CUDA images are too big, what do I do?
The `devel` [image tags](https://hub.docker.com/r/nvidia/cuda/) are large since the CUDA toolkit ships with many libraries, a compiler and various command-line tools.\
As a general rule of thumb, you shouldn’t ship your application with its build-time dependencies. We recommend to use [multi-stage builds](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) for this purpose. Your final container image should use our `runtime` or `base` images.\
Also, as of CUDA 9.0 we now ship a `base` [image tag](https://hub.docker.com/r/nvidia/cuda/) which bundles the strict minimum of dependencies.

## Ecosystem enablement

#### Do you support Docker Swarm mode?
Not currently, support for Swarmkit is still being worked on in the upstream Moby project. You can track our progress [here](https://github.com/moby/moby/issues/33439).

#### Do you support Docker Compose?
Yes, as long as you [configure your Docker daemon](https://github.com/NVIDIA/nvidia-docker/wiki/Advanced-topics#default-runtime) to use the `nvidia` runtime as the default, you will be able to use `docker-compose` with GPU support.

#### Do you support Kubernetes?
Since Kubernetes 1.8, the recommended way is to use our official [device plugin](https://github.com/NVIDIA/k8s-device-plugin). Note that this is still alpha support.



