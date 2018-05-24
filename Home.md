Select the topic you want to learn about from the list on the right.

# Frequently Asked Questions

### Setting up

* [How do I register the new runtime to the Docker daemon?](Frequently-Asked-Questions#how-do-i-register-the-new-runtime-to-the-docker-daemon)
* [Which Docker packages are supported?](Frequently-Asked-Questions#which-docker-packages-are-supported)
* [How do I install 2.0 if I'm not using the latest Docker version?](Frequently-Asked-Questions#how-do-i-install-20-if-im-not-using-the-latest-docker-version)
* [What is the minimum supported Docker version?](Frequently-Asked-Questions#what-is-the-minimum-supported-docker-version)
* [How do I install the NVIDIA driver?](Frequently-Asked-Questions#how-do-i-install-the-nvidia-driver)
* [Can I use 2.0 and 1.0 side-by-side?](Frequently-Asked-Questions#can-i-use-20-and-10-side-by-side)
* [Why do I get the error `Unknown runtime specified nvidia`?](Frequently-Asked-Questions#why-do-i-get-the-error-unknown-runtime-specified-nvidia)
* [Why do I get the error `flag provided but not defined: -console`?](Frequently-Asked-Questions#why-do-i-get-the-error-flag-provided-but-not-defined--console)
* [Why do I get the error `Depends: docker [...] but it is not installable` or `nothing provides docker [...]`?](Frequently-Asked-Questions#why-do-i-get-the-error-depends-docker--but-it-is-not-installable-or-nothing-provides-docker-)
* [Why do I get the error `file /etc/docker/daemon.json from install of nvidia-docker2 conflicts with file from package docker`?](Frequently-Asked-Questions##why-do-i-get-the-error-file-etcdockerdaemonjson-from-install-of-nvidia-docker2-conflicts-with-file-from-package-docker)

### Platform support

* [Is macOS supported?](Frequently-Asked-Questions#is-macos-supported)
* [Is Microsoft Windows supported?](Frequently-Asked-Questions#is-microsoft-windows-supported)
* [Do you support Microsoft native container technologies (e.g. Windows server, Hyper-v)?](Frequently-Asked-Questions#do-you-support-microsoft-native-container-technologies-eg-windows-server-hyper-v)
* [Do you support Optimus (i.e. NVIDIA dGPU + Intel iGPU)?](Frequently-Asked-Questions#do-you-support-optimus-ie-nvidia-dgpu--intel-igpu)
* [Do you support Tegra platforms (arm64)?](Frequently-Asked-Questions#do-you-support-tegra-platforms-arm64)
* [What distributions are officially supported?](Frequently-Asked-Questions#what-distributions-are-officially-supported)
* [Do you support PowerPC64 (ppc64)?](Frequently-Asked-Questions#do-you-support-powerpc64-ppc64)
* [How do I use this in on my Cloud service provider (e.g. AWS, Azure, GCP)?](Frequently-Asked-Questions#how-do-i-use-this-in-on-my-cloud-service-provider-eg-aws-azure-gcp)

### Container runtime

* [Does it have a performance impact on my GPU workload?](Frequently-Asked-Questions#does-it-have-a-performance-impact-on-my-gpu-workload)
* [Is OpenGL supported?](Frequently-Asked-Questions#is-opengl-supported)
* [How do I fix `unsatisfied condition: cuda >= X.Y`?](Frequently-Asked-Questions#how-do-i-fix-unsatisfied-condition-cuda--xy)
* [Do you support CUDA Multi Process Service (a.k.a. MPS)?](Frequently-Asked-Questions#do-you-support-cuda-multi-process-service-aka-mps)
* [Do you support running a GPU-accelerated X server inside the container?](Frequently-Asked-Questions#do-you-support-running-a-gpu-accelerated-x-server-inside-the-container)
* [I have multiple GPU devices, how can I isolate them between my containers?](Frequently-Asked-Questions#i-have-multiple-gpu-devices-how-can-i-isolate-them-between-my-containers)
* [Why is `nvidia-smi` inside the container not listing the running processes?](Frequently-Asked-Questions#why-is-nvidia-smi-inside-the-container-not-listing-the-running-processes)
* [Can I share a GPU between multiple containers?](Frequently-Asked-Questions#can-i-share-a-gpu-between-multiple-containers)
* [Can I limit the GPU resources (e.g. bandwidth, memory, CUDA cores) taken by a container?](Frequently-Asked-Questions#can-i-limit-the-gpu-resources-eg-bandwidth-memory-cuda-cores-taken-by-a-container)
* [Can I enforce exclusive access for a GPU?](Frequently-Asked-Questions#can-i-enforce-exclusive-access-for-a-gpu)
* [Why is my container slow to start with 2.0?](Frequently-Asked-Questions#why-is-my-container-slow-to-start-with-20)
* [Can I use it with Docker-in-Docker (a.k.a. DinD)?](Frequently-Asked-Questions#can-i-use-it-with-docker-in-docker-aka-dind)
* [Why is my application inside the container slow to initialize?](Frequently-Asked-Questions#why-is-my-application-inside-the-container-slow-to-initialize)
* [Is the JIT cache shared between containers?](Frequently-Asked-Questions#is-the-jit-cache-shared-between-containers)
* [What is causing the CUDA `invalid device function` error?](Frequently-Asked-Questions#what-is-causing-the-cuda-invalid-device-function-error)
* [Why do I get `Insufficient Permissions` for some `nvidia-smi` operations?](Frequently-Asked-Questions#why-do-i-get-insufficient-permissions-for-some-nvidia-smi-operations)
* [Can I profile and debug my GPU code inside a container?](Frequently-Asked-Questions#can-i-profile-and-debug-my-gpu-code-inside-a-container)
* [Is OpenCL supported?](Frequently-Asked-Questions#is-opencl-supported)
* [Is Vulkan supported?](Frequently-Asked-Questions#is-vulkan-supported)

### Container images

* [What do I have to install in my container images?](Frequently-Asked-Questions#what-do-i-have-to-install-in-my-container-images)
* [Do you provide official Docker images?](Frequently-Asked-Questions#do-you-provide-official-docker-images)
* [Can I use the GPU during a container build (i.e. `docker build`)?](Frequently-Asked-Questions#can-i-use-the-gpu-during-a-container-build-ie-docker-build)
* [Are my container images built for version 1.0 compatible with 2.0?](Frequently-Asked-Questions#are-my-container-images-built-for-version-10-compatible-with-20)
* [How do I link against driver APIs at build time (e.g. `libcuda.so` or `libnvidia-ml.so`)?](Frequently-Asked-Questions#how-do-i-link-against-driver-apis-at-build-time-eg-libcudaso-or-libnvidia-mlso)
* [The official CUDA images are too big, what do I do?](Frequently-Asked-Questions#the-official-cuda-images-are-too-big-what-do-i-do)

### Ecosystem enablement

* [Do you support Docker Swarm mode?](Frequently-Asked-Questions#do-you-support-docker-swarm-mode)
* [Do you support Docker Compose?](Frequently-Asked-Questions#do-you-support-docker-compose)
* [Do you support Kubernetes?](Frequently-Asked-Questions#do-you-support-kubernetes)
