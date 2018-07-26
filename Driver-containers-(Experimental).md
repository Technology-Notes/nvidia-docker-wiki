**Note that the features described below are currently experimental**.

## Description

NVIDIA driver container images are available through the [NVIDIA public hub repository](https://hub.docker.com/r/nvidia/driver).

It allows the provisioning of the NVIDIA driver through the use of containers which provides several benefits over a standard driver installation, for example:
- Ease of deployment
- Fast installation
- Reproducibility

For more information about its internals, check out this [presentation](https://docs.google.com/presentation/d/1NY4X2K6BMaByfnF9rMEcNq6hS3NtmOKGTfihZ44zfrw/edit?usp=sharing).

## Requirements

The list of prerequisites for running a driver container is described below.  

1. Ubuntu 16.04 or Centos 7 with the IPMI driver enabled and the Nouveau driver disabled
1. NVIDIA GPU with Architecture > Fermi (2.1)
1. A [supported version of Docker](https://github.com/NVIDIA/nvidia-docker/wiki/Frequently-Asked-Questions#which-docker-packages-are-supported) 
1. The [NVIDIA Container Runtime for Docker](https://github.com/NVIDIA/nvidia-docker/wiki/Installation-(version-2.0)) configured with the `root` option

## Examples

```sh
# Run the driver container for Ubuntu 16.04 LTS in interactive mode
docker run -it --name nvidia-driver --privileged --pid=host -v /run/nvidia:/run/nvidia:shared \
           nvidia/driver:396.37-ubuntu16.04

# Run the driver container for Ubuntu 16.04 AWS in detached mode
docker run -d --name nvidia-driver --privileged --pid=host -v /run/nvidia:/run/nvidia:shared \
  nvidia/driver:396.37-ubuntu16.04-aws --accept-license

# Run the driver container for Ubuntu 16.04 HWE in detached mode with
# auto-restarts and auto-detection of kernel updates (aka DKMS)
docker run -d --name nvidia-driver --privileged --pid=host -v /run/nvidia:/run/nvidia:shared \
  --restart=unless-stopped -v /etc/kernel/postinst.d:/run/kernel/postinst.d \
  nvidia/driver:396.37-ubuntu16.04-hwe --accept-license

# Run the driver container for Centos 7 in detached mode and check its logs 
docker run -d --name nvidia-driver --privileged --pid=host -v /run/nvidia:/run/nvidia:shared \
  nvidia/driver:396.37-centos7 --accept-license
docker logs -f nvidia-driver

# Build a custom driver container image for Centos 7 with the current kernel
docker build -t nvidia-driver:centos7 --build-arg KERNEL_VERSION=$(uname -r) \
  https://gitlab.com/nvidia/driver.git#centos7

# Perform a driver update ahead of time for a given kernel version
docker exec nvidia-driver nvidia-driver update --kernel 4.15.0-23
```

## Quickstart

#### Ubuntu 16.04

```
curl https://get.docker.com | sudo CHANNEL=stable sh

curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/ubuntu16.04/nvidia-docker.list \
  | sudo tee /etc/apt/sources.list.d/nvidia-docker.list

sudo apt-get update && sudo apt-get install -y nvidia-docker2
sudo sed -i 's/^#root/root/' /etc/nvidia-container-runtime/config.toml

sudo tee /etc/modules-load.d/ipmi.conf <<< "ipmi_msghandler"
sudo tee /etc/modprobe.d/blacklist-nouveau.conf <<< "blacklist nouveau"
sudo tee -a /etc/modprobe.d/blacklist-nouveau.conf <<< "options nouveau modeset=0"
sudo update-initramfs -u

# Optionally, if the kernel is not up to date
# sudo apt-get dist-upgrade

sudo reboot

sudo docker run -d --privileged --pid=host -v /run/nvidia:/run/nvidia:shared \
  --restart=unless-stopped nvidia/driver:396.37-ubuntu16.04 --accept-license

sudo docker run --rm --runtime=nvidia nvidia/cuda:9.2-base nvidia-smi
```

#### Centos 7
```
curl https://get.docker.com | sudo CHANNEL=stable sh
sudo systemctl enable docker

curl -s -L https://nvidia.github.io/nvidia-docker/centos7/nvidia-docker.repo \
  | sudo tee /etc/yum.repos.d/nvidia-docker.repo

sudo yum install -y nvidia-docker2
sudo sed -i 's/^#root/root/' /etc/nvidia-container-runtime/config.toml

sudo tee /etc/modules-load.d/ipmi.conf <<< "ipmi_msghandler"
sudo tee /etc/modprobe.d/blacklist-nouveau.conf <<< "blacklist nouveau"
sudo tee -a /etc/modprobe.d/blacklist-nouveau.conf <<< "options nouveau modeset=0"

# Optionally, if the kernel is not up to date
# sudo yum update

sudo reboot

sudo docker run -d --privileged --pid=host -v /run/nvidia:/run/nvidia:shared \
  --restart=unless-stopped nvidia/driver:396.37-centos7 --accept-license

sudo docker run --rm --runtime=nvidia nvidia/cuda:9.2-base nvidia-smi
```

## Tags available
Check the [DockerHub](https://hub.docker.com/r/nvidia/driver/)