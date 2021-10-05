---
title: "Setting Up Docker"
date: 2021-09-08
weight: 10
---

For this project we use Docker for building containerized applications. It allows for us to bulid applications the can be easily shared across multiple platforms. It also allows us to spin up development environments with preconfigured settings we have defined for containers.

Here we denote how we go about setting up docker. The first use case it setting up a development environment for our docs page. This very docs page! So here we go!

## Perquisites


- Sourcetree
    - Configured with the repo for the featherstream docs page.

- Windows
    - This walkthrough assumes you are on Windows but Docker is also available on Linux

#### Step 1. Download

Download "Docker Desktop for Windows".

Navigate to the following webpage to start the Download

[https://docs.docker.com/desktop/windows/install/](https://docs.docker.com/desktop/windows/install/)

![Docker Button](/images/other/docker/docker-button.png?classes=border,shadow "button")

Once you have downloaded the installer and click the "Ok" button you should see docker unpacking itself.

![Docker Download](/images/other/docker/docker-download.png)

If the download was successful you should see the "Successful download window." That would inform you to "Close and restart".

![Docker Complete](/images/other/docker/docker-complete.png)

#### Step 2. Enabling Virtualization

Once you have installed Docker and done a restart you may find that since you are on Windows you will hit an error due to something called "Virtualization".

![Docker Error](/images/other/docker/docker-error.png)

To fix this you will need to navigate to Windows Features.

- Visit the Control Panel on you machine.
- Navigate to Programs and Features.
- On the left sidebar select "Turn Windows features on or off"
- ensure that "Virtual Machine Platform" and "Windows Subsystem for Linux"
- Ensure that Hyper-V is installed and on.
  - If it is not installed you and read about how to install Hyper-V [here.](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)
  {{% notice info %}}
  Hyper-V is required if you want Windows volume tro be actively watched by Docker. WSL2 currently is bugged for this feature as of ```Docker-Desktop 4.0.0```
  {{% /notice %}}
- Ensure that Virtualization is enabled

If Virtualization is enabled you should be able to check by navigating to the performance tab of Task manager.

![Docker Error](/images/other/docker/task-manager.png)

#### Step 3. Setup Linux Kernel

If you are setting up a wsl2 linux kernel on you machine for the first time you will see this error upon restart.

![Docker wsl2](/images/other/docker/docker-wsl2.png)

To enable the linux kernel or download one because you haven't previously you can follow this [guide.](https://docs.microsoft.com/en-us/windows/wsl/install-win10#step-4---download-the-linux-kernel-update-package)

You will need to download the "WSL2 Linux kernel update package for x64 machines" via this [link.](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi)

Assuming you don't have a virtual linux kernel enabled, run the following command in powersheel as admin

```
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

Then set wsl2 as the default version with the following command.

```
wsl --set-default-version 2
```
#### Step 4. Introduction To Docker

Once you have restarted the machine and WSL2 is successful installed you should be able to view this window

[Docker GUI](/images/other/docker/docker-gui.png)

Click "skip tutorial"

Go to Windows Powershell run the following command to ensure we are working out of the correct directory.

```
cd .\PycharmProjects\
```

We then write to command to spin up our new container using ubuntu as our kernel. The command below spins up a new container

```
 docker run --name featherstream -v $PWD/featherstream:/featherstream -p 1313:1313 -it ubuntu:latest /bin/bash
```

The command above spins up a new container called *featherstream*. the ```-v``` flag allows us to map the featherstream directory to a new directory of the same inside of the container. ```-p``` flag opens up the port 1313. and ```-it``` will start an interactive shell for that container.

If we want to return to that terminal for a container of the same name we run:

```
# Only required to start container if not currently running
docker start featherstream
docker exec -it featherstream /bin/bash
```

Now that we have our container up and running we require prerequisites to build our site:

```

# install utils that we'll need:
apt-get update
apt-get install curl git

# download deb file
curl -L https://github.com/gohugoio/hugo/releases/download/v0.74.3/hugo_0.74.3_Linux-64bit.deb -o hugo.deb

# install
apt install ./hugo.deb

# verify that it worked
hugo version
```

We now want to see the site in the browser. We need to start Hugo's built-in webserver inside the container:

```
# Ensure we are looking at the directory containing the config file.
cd featherstream/src
hugo server --bind 0.0.0.0 -D
```
