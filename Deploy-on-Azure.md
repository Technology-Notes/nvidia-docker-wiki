## Contents
1. [Azure subscription ID](#azure-subscription-id)
1. [GPU instance creation](#gpu-instance-creation)
1. [Container deployment](#container-deployment)

## Azure Subscription ID

Connect to your [Microsoft Azure Dashboard](https://portal.azure.com/) and find your Subscription ID:
[[images/azure-subscription-id.png]]
This image was kindly provided by the Azure support team.

## GPU instance creation

Before deploying GPU containers, we first need to provision a VM of the [N-Series type](https://azure.microsoft.com/en-us/blog/azure-n-series-general-availability-on-december-1/).  
Using [Docker machine](https://docs.docker.com/machine/install-machine/) and your subscription ID:

```sh
docker-machine create --driver azure \
                      --azure-subscription-id ********-****-****-****-************ \
                      --azure-location southcentralus \
                      --azure-size Standard_NV6 \
                      --azure-image canonical:UbuntuServer:16.04.0-LTS:latest \
                      azure01
```
The first time you run this command, you will be asked to open a webpage and enter an authentication code, after you do, provisioning will resume.
```sh
(azure01) Microsoft Azure: To sign in, use a web browser to open the page https://aka.ms/devicelogin and enter the code ********* to authenticate.
```

Once the provisioning is completed, we must install the NVIDIA driver and nvidia-docker.


```sh
# SSH into the machine
docker-machine ssh azure01

# Install official NVIDIA driver package
sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub
sudo sh -c 'echo "deb http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64 /" > /etc/apt/sources.list.d/cuda.list'
sudo apt-get update && sudo apt-get install -y --no-install-recommends cuda-drivers

# Unload nouveau
sudo rmmod nouveau

# Install nvidia-docker and nvidia-docker-plugin
wget -P /tmp https://github.com/NVIDIA/nvidia-docker/releases/download/v1.0.0/nvidia-docker_1.0.0-1_amd64.deb
sudo dpkg -i /tmp/nvidia-docker*.deb && rm /tmp/nvidia-docker*.deb
```

## Container deployment

First, setup your environment:

```sh
eval `docker-machine env azure01`
export NV_HOST="ssh://docker-user@$(docker-machine ip azure01):"
ssh-add ~/.docker/machine/machines/azure01/id_rsa
```

Using `nvidia-docker` [remotely](nvidia-docker#running-it-remotely) you can now deploy your containers in the Azure cloud:

```sh
nvidia-docker run --rm nvidia/cuda nvidia-smi
     
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 367.57                 Driver Version: 367.57                    |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  Tesla M60           Off  | A527:00:00.0     Off |                  Off |
| N/A   39C    P8    14W / 150W |      0MiB /  8123MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+
```
When you're done, remember to cleanup your instance with `docker-machine rm -f azure01`.