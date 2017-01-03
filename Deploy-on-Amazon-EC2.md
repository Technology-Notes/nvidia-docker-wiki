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

Before deploying GPU containers, we first need to provision an [EC2 G2 instance](https://aws.amazon.com/ec2/instance-types/#gpu).  
Using [Docker machine](https://docs.docker.com/machine/install-machine/) and the information above:

```sh
docker-machine create --driver amazonec2 \
                      --amazonec2-region us-west-2 \
                      --amazonec2-zone b \
                      --amazonec2-ami ami-b7a114d7 \
                      --amazonec2-instance-type g2.2xlarge \
                      --amazonec2-vpc-id vpc-*** \
                      --amazonec2-access-key AKI*** \
                      --amazonec2-secret-key *** \
                      aws01
```

Once the provisioning is completed, we install the NVIDIA drivers and NVIDIA Docker on the newly created instance (Ubuntu by default).  
Note that if you create a custom [AMI](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html), you could simply reuse it instead of doing what follows:

```sh
# SSH into the machine
docker-machine ssh aws01

# Install official NVIDIA driver package
sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub
sudo sh -c 'echo "deb http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64 /" > /etc/apt/sources.list.d/cuda.list'
sudo apt-get update && sudo apt-get install -y --no-install-recommends cuda-drivers

# Install nvidia-docker and nvidia-docker-plugin
wget -P /tmp https://github.com/NVIDIA/nvidia-docker/releases/download/v1.0.0-rc.3/nvidia-docker_1.0.0.rc.3-1_amd64.deb
sudo dpkg -i /tmp/nvidia-docker*.deb && rm /tmp/nvidia-docker*.deb
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
nvidia-docker run --rm nvidia/cuda nvidia-smi
     
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 367.57                 Driver Version: 367.57                    |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  GRID K520           Off  | 0000:00:03.0     Off |                  N/A |
| N/A   28C    P8    18W / 125W |      0MiB /  4036MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+
```