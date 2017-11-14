**This page hasn't been updated for 2.0 yet**

## Contents
1. [Amazon AWS account setup](#amazon-aws-account-setup)
1. [GPU instance creation](#gpu-instance-creation)
1. [Container deployment](#container-deployment)

## Amazon AWS account setup

Connect to your [AWS management console](https://console.aws.amazon.com)

### Step 1

Under _VPC > Subnets_

1. Select your **VPC ID**
2. Select the corresponding **Availability Zone**

[[images/ec2-vpc.png]]

### Step 2

Under _Identity and Access Management > Users_

1. Create a new user
2. Save the newly generated pair **Access Key** / **Secret Access Key**
3. Edit the user permissions and give it the policy **AmazonEC2FullAccess**

[[images/ec2-user-keys.png]]

[[images/ec2-user-policy.png]]


## GPU instance creation

Before deploying GPU containers, we first need to provision an [EC2 P2 instance](https://aws.amazon.com/ec2/instance-types/#gpu).  
Using [Docker machine](https://docs.docker.com/machine/install-machine/) and the information above:

```sh
docker-machine create --driver amazonec2 \
                      --amazonec2-region us-west-2 \
                      --amazonec2-zone b \
                      --amazonec2-ami ami-efd0428f \
                      --amazonec2-instance-type p2.xlarge \
                      --amazonec2-vpc-id vpc-*** \
                      --amazonec2-access-key AKI*** \
                      --amazonec2-secret-key *** \
                      aws01
```

Once the provisioning is completed, we install the NVIDIA drivers and NVIDIA Docker on the newly created instance (using a Ubuntu 16.04 AMI).  
Note that if you create a custom [AMI](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html), you could simply reuse it instead of doing what follows:

```sh
# Restart the instance first, to be sure we are running the latest installed kernel
docker-machine restart aws01

# SSH into the machine
docker-machine ssh aws01

# Install official NVIDIA driver package
sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub
sudo sh -c 'echo "deb http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64 /" > /etc/apt/sources.list.d/cuda.list'
sudo apt-get update && sudo apt-get install -y --no-install-recommends linux-headers-generic dkms cuda-drivers

# Install nvidia-docker and nvidia-docker-plugin
wget -P /tmp https://github.com/NVIDIA/nvidia-docker/releases/download/v1.0.1/nvidia-docker_1.0.1-1_amd64.deb
sudo dpkg -i /tmp/nvidia-docker*.deb && rm /tmp/nvidia-docker*.deb
exit

# Reboot to complete installation of the NVIDIA driver
docker-machine restart aws01
```

## Container deployment

First, setup your environment:

```sh
eval `docker-machine env aws01`
export NV_HOST="ssh://ubuntu@$(docker-machine ip aws01):"
ssh-add ~/.docker/machine/machines/aws01/id_rsa
```

Using `nvidia-docker` [remotely](nvidia-docker#running-it-remotely) you can now deploy your containers in the Amazon cloud:

```sh
$ nvidia-docker run --rm nvidia/cuda nvidia-smi
Wed May 17 17:13:09 2017       
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 375.51                 Driver Version: 375.51                    |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  Tesla K80           Off  | 0000:00:1E.0     Off |                    0 |
| N/A   26C    P8    29W / 149W |      0MiB / 11439MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+
                                                                               
+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID  Type  Process name                               Usage      |
|=============================================================================|
|  No running processes found                                                 |
+-----------------------------------------------------------------------------+
```