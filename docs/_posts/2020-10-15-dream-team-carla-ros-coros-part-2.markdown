---
layout: post
title:  "Dream Team: Carla, ROS and COROS - Part II Installation"
date:   2020-10-15 10:11:37 +0200
comments: false
tags: Simulation Carla Docker ROS
author: "Johann"
---

$$(carla + ROS + COROS \neq chaos)^2$$

I have to admit that the first part was very theoretical, part 2 will be exactly contrary: we will install carla and COROS on our system.

Notice: all steps described here can also be found in a shorter form in the [COROS documentation](https://github.com/gismo07/coros/blob/master/README.md).

<br>

# Carla
![carla]({{site.baseurl}}/assets/install/carla.png)
*screenshot of the carla world server*

To install and launch carla, the process is quite straightforward:

## Windows
1. Download the `0.9.10.1` version from the offical [carla releases](https://github.com/carla-simulator/carla/releases/tag/0.9.10.1)

2. Start the carla exe located under `CARLA_0.9.10.1\WindowsNoEditor\CarlaUE4.exe`:
     ```bash
     ./CarlaUE4.exe -Carla-server -windowed
     ```

## Linux
Here we use the ready-made docker container. However, it is also possible to directly use the executable from the [carla releases](https://github.com/carla-simulator/carla/releases/tag/0.9.10.1).
1. Get the right carla docker image:
    ```bash
    sudo docker pull carlasim/carla:0.9.10.1
    ```
2. Make sure the package `xhost` is available on your system. If not:
    ```
    sudo apt-get update
    sudo apt-get install xhost
    ```

3. start carla while forwarding the x context:
    ```bash
    xhost local:root
    sudo docker run \
      -e SDL_VIDEODRIVER=x11 \
      -e DISPLAY=$DISPLAY \
      -v /tmp/.X11-unix:/tmp/.X11-unix \
      -p 2000-2002:2000-2002 \
      -it \
      --gpus all \
      carlasim/carla:0.9.10.1 ./CarlaUE4.sh -opengl
    ```

<br>
Now you should see a running carla instance. This is the carla server (world). Next, we want to install COROS to be able to use the carla ROS bridge to spawn a test vehicle in [Part III](({{site.baseurl}}/2020-10-15/dream-team-carla-ros-coros-part-3)).


<br>

# COROS
![carla]({{site.baseurl}}/assets/install/rviz.png)
*screenshot of COROS visualizing the LIDAR sensor of carla*

Also the installation of COROS is quick and easy:

## Windows
1. pull the COROS github project:
    ```bash
    git clone https://github.com/gismo07/coros.git
    ```
2. launch COROS using the provided startup script:
    ```bash
    cd coros
    cd utils-windows
    ./start-windows.cmd
    ```

## Linux / Mac
1. pull the COROS github project:
    ```bash
    git clone https://github.com/gismo07/coros.git
    ```
2. launch COROS using the provided startup script:
    ```bash
    cd coros
    cd utils
    sudo chmod +x ./start.sh
    ./start.sh
    ```

## Access the environment
  - to open the IDE, simply open the browser and go to [localhost:80](localhost:80), the password is currently set to `dev@ros`
  - to see the desktop of the virtual linux, open another browser tab [localhost:6080/vnc.html]() and click connect

<br>

# One last word
Remember: if a docker container is stopped, all data is lost! For this reason volumes are used to store the data permanently. Out of the box, only the contents of the two folders `/my_ros_data` and `/workspace` are persistently saved. If you want to add more, please refer to [COROS: data storage](https://github.com/gismo07/coros/blob/master/README.md#-data-storage)

<br>
In the [next part](({{site.baseurl}}/2020-10-15/dream-team-carla-ros-coros-part-3)) we are going through the basics of the carla ROS bridge.

<!-- ![communication of carla and coros](https://raw.githubusercontent.com/gismo07/coros/master/assets/carla.jpg) -->
