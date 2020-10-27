---
layout: post
title:  "Build Carla using Docker"
date:   2020-10-24 10:11:37 +0200
comments: false
tags: Docker Carla
author: "Johann"
---

# Requirements
Unfortunately, the requirements to build Carla, respectively the Carla Docker Container, are relatively high:
 - 64-bit version of Docker
 - **Minimum 8GB of RAM**
 - **Minimum of 300GB disk space**
 - Github account
 - Epic account

## Uff, why so much space?
For building the carla image, [ue4-docker](https://github.com/adamrehn/ue4-docker) is used. According to their documentation:

> Although none of the container images produced by ue4-docker currently exceed 100GB in size, the build process requires at least 300GB of available disk space under Linux and at least 500GiB of available disk space under Windows

Enough space available? Then let's get started. But what if not?

### Workaround I - Docker data-root
It is possible to configure docker to build and store images on an external hard drive. For this the file `/etc/docker/daemon.json` must be modified:
```json
{
 //...
 "data-root": "/media/ubuntu/1TB/docker",
 //...
}
```
After this, we need to restart the docker daemon:
```shell
sudo systemctl daemon-reload
```

### Workaround II - Linux VM
If your host system is Windows or you dont want to change your docker deamon settings, a fresh installation of Linux within a VM (like [Virtual Box](https://www.virtualbox.org/)) could be the better option. To store the virtual harddrive also an external USB drive is possible.

To speed up the later building process it is a good idea to preallocate disk space when creating the virtual hard disk.

## Get access to UnrealEngine Github project
Later within the build process the Unreal Engine C++ source code is pulled from github. However Unreal Engine is not a puplic repo. To get access follow the offical guide [How do I access Unreal Engine 4 C++ source code via GitHub?](https://www.unrealengine.com/en-US/ue4-on-github).

Now that we meet all the requirements we can kick off.

# Step I - Dependencies
The list of dependencies is very manageable:
```shell
sudo apt-get update && sudo apt-get install -y python3-pip
sudo pip3 install ue4-docker
sudo ue4-docker setup
```

# Step II - Build the preparation images
1. first we need to clone the carla github repo and change directory to the Docker folder:
	```shell
	git clone https://github.com/carla-simulator/carla.git
	cd carla/Utils/Docker
	```
2. now we build the Unreal Engine image with:
	```shell
	ue4-docker build 4.24.3 --no-engine --no-minimal
	```
3. optionally we can free some storage:
	```shell
	ue4-docker clean
	```
4. build the requisites carla image:
```shell
docker build -t carla-prerequisites -f Prerequisites.Dockerfile .
```

# Step II - Build the carla image
At this point it makes sense to modify `Carla.Dockerfile` to use more build steps:
```
FROM carla-prerequisites:latest

ARG GIT_BRANCH

USER ue4

RUN cd /home/ue4 && \
  if [ -z ${GIT_BRANCH+x} ]; then git clone --depth 1 https://github.com/carla-simulator/carla.git; \
  else git clone --depth 1 --branch $GIT_BRANCH https://github.com/carla-simulator/carla.git; fi

RUN cd /home/ue4/carla && \
  ./Update.sh

RUN cd /home/ue4/carla && make CarlaUE4Editor
RUN cd /home/ue4/carla && make PythonAPI
RUN cd /home/ue4/carla && make build.utils
RUN cd /home/ue4/carla && make package
RUN rm -r /home/ue4/carla/Dist

WORKDIR /home/ue4/carla
```

The script `Update.sh` is going to download about 12GB of assets data. If some of the build commands lateron fail, we will have to download this data over and over again.

Also the Makefiles for CarlaUE4Editor or the PythonAPI are issuing multiple git pulls, we will cache them also if later commands fail.

To build the carla image issue this command:
```shell
docker build -t carla -f Carla.Dockerfile .
```
