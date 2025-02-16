---
title: 'Homelab Episode 0 : Getting Setup'
date: 2025-02-16
permalink: /posts/2025-02-16-Homelab-Episode0-Getting-Setup/
tags:
  - Homelab
  - Linux
  - Docker
  - Docker-Compose
  - Ubuntu
---

# Before we begin

First we will create a Ubuntu VM, we will be using one we created in the cloud provider Vultr (My refferal link: https://www.vultr.com/?ref=9574728) and SSH in. 

Our VM will be specced out with one shared CPU core and one GB of memory, as well as a 10 GB SSD we have attached to /mnt/ssd-docker-data1.

# Installing Docker

Once we are SSH'd into your vm we will install docker and docker-compose. The below instructions have been copied from the offical docker install instructions: https://docs.docker.com/engine/install/ubuntu/#installation-methods

### 1. Set up Docker's apt repository

```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```
### 2. Install the Docker packages

Latest Specific version
To install the latest version, run:

```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### 3. Verify that the installation is successful by running the hello-world image

```
sudo docker run hello-world
```
This command downloads a test image and runs it in a container. When the container runs, it prints a confirmation message and exits.


# Optional : Change docker volumes path

We will be changing the default volumes path to the virtual ssd that is attached to our vm at /mnt/ssd-docker-data1.

### 1. Open docker service file

Using nano, we will open the services config file

```
nano /lib/systemd/system/docker.service
```

### 2. Edit docker service file

Once opened inside of nano, you will want to look for a string that looks like this:

```
ExecStart=/usr/bin/dockerd -H fd:// $DOCKER_OPTS
```
Once the string is located, we will use the `--data-root` flag to tell docker where we want to store data
```
ExecStart=/usr/bin/dockerd --data-root /mnt/ssd-docker-data1/ -H fd:// --containerd=/run/containerd/containerd.sock
```
### 3. Apply settings

Once your changes have been completed, save the file by pressing `crtl+x` and slecting `y` for yes. Now reboot your VM to apply the settings.

# Install Docker-Compose
Docker-compose is a much easier way to manager container deployment as we can use configuration files to startup containers.

### 1. Download Docker-Compose to your system

Run the below commands to install the latest version of docker-compose on your system

```
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

### 2. Verify installation complete

Run the below command to validate your docker-compose install

```
docker-compose --version
```

Output should look like: `Docker Compose version v2.23.3`
