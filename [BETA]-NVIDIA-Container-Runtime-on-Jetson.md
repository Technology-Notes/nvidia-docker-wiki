# NVIDIA Container Runtime on Jetson
## Introduction

	As part of JetPack 4.2.1, users will now be able to use an early access of NVIDIA Container Runtime for Docker. Users will be able to run Deep Learning and HPC containers that are GPU accelerated with Docker on Jetson devices.

The NVIDIA runtime enables graphics and video processing applications such as DeepStream to be run in containers on the Jetson platform. As part of this release, users have access to two Jetson container images:
- nvcr.io/nvidia/linux4tegra-base:r32.1
- nvcr.io/nvidia/deepstream

The purpose of this document is to provide users with steps on getting started with running Docker containers on Jetson using the NVIDIA runtime. 

# Installation
The NVIDIA Container Runtime for Docker (via the nvidia-docker2 packages) will be available for installation as part of JetPack with the other SDK components (CUDA, AI, Computer Vision, …) as shown below in figure 1.

![](https://lh3.googleusercontent.com/_IrW289rk7TV-KjJNcxc8RZxoAyBjaoyjAxSBTTbYK97izactu5UhTgRsw3kFO8widR_Ze_R1UjgSqHpcenVL3rBB8y9qd5NkSb8Ciw6G4i3lMCzQ4HbTjpwhDclM7LWMp4I-c_9)
_Figure 1: Jetpack Installation step 2_

Once your nano configured you can check that the NVIDIA runtime is installed by running the following commands:
```diff
$ sudo dpkg --get-selections | grep nvidia
libnvidia-container-tools			install
libnvidia-container0:arm64			install
nvidia-container-runtime			install
nvidia-container-runtime-hook		install
nvidia-docker2				install

$ sudo docker info | grep nvidia
+ Runtimes: nvidia runc
```

## Hello-world!

Once done with the installation process, we’ll go ahead and create a cool graphics application
```
# Allow containers to communicate with Xorg
$ sudo xhost +si:localuser:root
$ sudo docker run --runtime nvidia --network host -it -e DISPLAY=$DISPLAY -v /tmp/.X11-unix/:/tmp/.X11-unix nvcr.io/nvidian/nvidia-l4t-base:r32.1

root@nano:/# apt-get update && apt-get install -y --no-install-recommends make g++
root@nano:/# cp -r /usr/local/cuda/samples /tmp
root@nano:/# cd /tmp/samples/5_Simulations/nbody
root@nano:/# make
root@nano:/# ./nbody
```

You should see the following result:

![](https://lh3.googleusercontent.com/i2W0kbAvSi-qqeD4VxK44gXH2N0svJz2GBM9cRFoLoDNuNtTV9ruYQv_EUFwZZQEI30xJyouxdkHYVFAkR8I7I23zN9JrHG9_tNnOnaqYsV3swTpjxPj2CcUBaAN0nLR2dFoE8Ht)

If you don’t see the packages in the first command or if you don’t see the runtime head to the Troubleshooting section.


## Building CUDA in Containers on Jetson

Docker gives you the ability to build containers using the “docker build” command, you’ll find below an example of how to do that on your Jetson device:

```shell
$ mkdir /tmp/docker-build && cd /tmp/docker-build
$ cp -r /usr/local/cuda/samples/ ./
$ tee ./Dockerfile <<EOF
FROM nvcr.io/nvidian/nvidia-l4t-base:r32.1

RUN apt-get update && apt-get install -y --no-install-recommends make g++
COPY ./samples /tmp/samples

WORKDIR /tmp/samples/1_Utilities/deviceQuery
RUN make clean && make

CMD ["./deviceQuery"]
EOF

$ sudo docker build -t devicequery .
$ sudo docker run -it --runtime nvidia devicequery

CUDA Device Query (Runtime API) version (CUDART static linking)

Detected 1 CUDA Capable device(s)

Device 0: "Xavier"
  CUDA Driver Version / Runtime Version          10.0 / 10.0
  CUDA Capability Major/Minor version number:    7.2
  Total amount of global memory:                 15692 MBytes (16454430720 bytes)
  ( 8) Multiprocessors, ( 64) CUDA Cores/MP:     512 CUDA Cores
  GPU Max Clock rate:                            1500 MHz (1.50 GHz)
  Memory Clock rate:                             1377 Mhz
  Memory Bus Width:                              256-bit
  L2 Cache Size:                                 524288 bytes
...

deviceQuery, CUDA Driver = CUDART, CUDA Driver Version = 10.0, CUDA Runtime Version = 10.0, NumDevs = 1
Result = PASS
```


Known limitation: The base l4t image doesn’t allow you to statically compile with all CUDA libraries. Only libcudadevrt.a and libcudart_static.a are included.

## Enabling Jetson Containers on an x86 workstation (using qemu)
One of the very cool features that are now enabled is the ability to build ARM CUDA binaries on your x86 machine without needing a cross compiler.
You can very easily run aarch64 containers on your x86 workstation by using qemu’s virtualization features, this section will go over the steps to enable that. The next section will go over the workflow that allows you to build on x86 and then run on Jetson.
Installing the following packages should allow you to enable support for aarch64 containers on x86:
```shell
$ sudo apt-get install qemu binfmt-support qemu-user-static

# Check if the entries look good.
$ sudo cat /proc/sys/fs/binfmt_misc/status
enabled

# See if /usr/bin/qemu-aarch64-static exists as one of the interpreters.
$ cat /proc/sys/fs/binfmt_misc/qemu-aarch64
enabled
interpreter /usr/bin/qemu-aarch64-static
flags: OCF
offset 0
magic 7f454c460201010000000000000000000200b700
mask ffffffffffffff00fffffffffffffffffeffffff
```

Make sure the F flag is present, if not head to the troubleshooting section, as this will result in a failure to start the Jetson container.
You’ll usually find errors in the form: exec user process caused "exec format error"

## Building Jetson Containers on an x86 workstation (using qemu)

Now that you have the required setup, we can get to building an ARM CUDA application on x86. Simply copy your code inside your container and run nvcc.

```
$ mkdir /tmp/docker-build && cd /tmp/docker-build
$ cp -r /usr/local/cuda/samples/ ./

$ tee ./Dockerfile <<EOF
FROM nvcr.io/nvidian/nvidia-l4t-base:r32.1

RUN apt-get update && apt-get install -y --no-install-recommends make g++
COPY ./samples /tmp/samples

WORKDIR /tmp/samples/1_Utilities/deviceQuery
RUN make clean && make

CMD ["./deviceQuery"]
EOF

$ sudo docker build -t docker://USERNAME/devicequery .
$ sudo docker push docker://USERNAME/devicequery

# On your Jetson enabled device:
$ sudo docker run -it --runtime nvidia docker://USERNAME/devicequery

CUDA Device Query (Runtime API) version (CUDART static linking)

Detected 1 CUDA Capable device(s)

Device 0: "Xavier"
  CUDA Driver Version / Runtime Version          10.0 / 10.0
  CUDA Capability Major/Minor version number:    7.2
  Total amount of global memory:                 15692 MBytes (16454430720 bytes)
  Memory Bus Width:                              256-bit
  L2 Cache Size:                                 524288 bytes
...

deviceQuery, CUDA Driver = CUDART, CUDA Driver Version = 10.0, CUDA Runtime Version = 10.0, NumDevs = 1
Result = PASS
```


Alternatively you can get a shell running, mount your code inside the container and compile it.

```
$ sudo docker run -it -v /usr/local/cuda:/usr/local/cuda http://nvcr.io/nvidian/nvidia-l4t-base:r32.1
root@x86host:/# apt-get update && apt-get install -y --no-install-recommends make g++
root@x86host:/# cp -r /usr/local/cuda/samples /tmp
root@x86host:/# cd /tmp/samples/5_Simulations/nbody
root@x86host:/# make
```

Known limitation: Unfortunately you won’t be able to run any binary that calls into the NVIDIA driver on the x86 host.

## Troubleshooting

### No package show are shown in dpkg’s output

```diff
$ sudo dpkg --get-selections | grep nvidia
- libnvidia-container-tools			install
- libnvidia-container0:arm64			install
- nvidia-container-runtime			install
- nvidia-container-runtime-hook			install
- nvidia-docker2				install
```

You need to reinstall the NVIDIA Container Runtime for Docker using the Jetpack process. Make sure that no errors are shown in the UI.

### nvidia-docker2 package is missing from dpkg’s output

You will need to manually register the runtime:

```diff
$ sudo dpkg --get-selections | grep nvidia
libnvidia-container-tools			install
libnvidia-container0:arm64			install
nvidia-container-runtime			install
nvidia-container-runtime-hook   		install
- nvidia-docker2				install

$ ls /etc/docker/daemon.json
ls: cannot access '/etc/docker/daemon.json': No such file or directory

$ sudo tee /etc/docker/daemon.json <<EOF
{
    "runtimes": {
        "nvidia": {
            "path": "/usr/bin/nvidia-container-runtime",
            "runtimeArgs": []
        
    
}
EOF
$ sudo pkill -SIGHUP dockerd
```

### Docker info doesn’t show the NVIDIA runtime
```diff
$ sudo docker info | grep nvidia

$ sudo pkill -SIGHUP dockerd
$ sudo docker info | grep nvidia
+ Runtimes: nvidia runc
```

### Generating and viewing logs

You can enable logs by uncommenting the debug field in /etc/nvidia-container-runtime/config.toml

```shell
$ cat /etc/nvidia-container-runtime/config.toml 
disable-require = false
#swarm-resource = "DOCKER_RESOURCE_GPU"

[nvidia-container-cli]
#root = "/run/nvidia/driver"
#path = "/usr/bin/nvidia-container-cli"
environment = ["PATH=/tmp"]
debug = "/var/log/nvidia-container-runtime-hook.log"
#ldcache = "/etc/ld.so.cache"
load-kmods = true
#no-cgroups = false
#user = "root:video"
ldconfig = "@/sbin/ldconfig.real"
```

### /usr/local/cuda is readonly
One of the limitations of this tech preview is that we are mounting the cuda directory from the host. This was done with size in mind as a devel CUDA container weighs 3GB, on Nano it’s not always possible to afford such a huge cost. We are currently working towards creating smaller full blown CUDA containers.


### Running or building a container on x86 (using qemu+binfmt_misc) is failing
The container does not start (hangs) or there is an error saying ‘exec format error’. Check if the interpreter is available to the containers.
```shell
# See if /usr/bin/qemu-aarch64-static exists as one of the interpreters.
$ cat /proc/sys/fs/binfmt_misc/qemu-aarch64
enabled
interpreter /usr/bin/qemu-aarch64-static
flags: OCF
offset 0
magic 7f454c460201010000000000000000000200b700
mask ffffffffffffff00fffffffffffffffffeffffff
```

If the flags does not include ‘F’ then the kernel is loading the interpreter lazily. The easiest fix is to have the binfmt-support package version >= 2.1.7, which automatically includes the --fix-binary (F) option. The other option is to run containers with /usr/bin/qemu-aarch64-static mounted inside the container:

```
# volume mount /usr/bin/qemu-aarch64-static
docker run -it -v /usr/bin/qemu-aarch64-static:/usr/bin/qemu-aarch64-static -v /usr/local/cuda:/usr/local/cuda http://nvcr.io/nvidian/nvidia-l4t-base:r32.1
If running `docker build`; perhaps a better option is to use ‘podman’ (https://podman.io/) instead. Install podman on the system and run `podmand build` with `-v /usr/bin/qemu-aarch64-static:/usr/bin/qemu-aarch64-static`. Example:
# volume mount /usr/bin/qemu-aarch64-static
sudo podman build -v /usr/bin/qemu-aarch64-static:/usr/bin/qemu-aarch64-static -t <image_tag> .
```