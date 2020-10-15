---
layout: post
title:  "Dream Team: Carla, ROS and COROS - Part I Introduction"
date:   2020-10-15 10:11:37 +0200
comments: false
tags: Simulation Carla Docker ROS
author: "Johann"
---

$$carla + ROS + COROS \neq chaos$$

To start the development of driver assistance systems or even autonomous pilot functions you need a suitable simulation environment and the ability to easily access required sensor signals and control the vehicle. In addition a certain abstraction of the interfaces is necessary. 

In this post we will look at how the simulation environment [carla](https://carla.org) is suitable for AD development can be accessed and controlled via a [ROS](https://www.ros.org) using the [carla ros bridge](https://github.com/carla-simulator/ros-bridge) interface. And best of all: this runs on Linux and Windwos equally with no installation hussle using [COROS](https://github.com/gismo07/coros).

In Part I we look at the basics of Carla, ROS and COROS. Let's start!

# Carla
![screenshot of carla](https://carla.org/img/posts/2020-31-07/sky_atmosphere.jpg)
*screenshot of carla https://carla.org/img/posts/2020-31-07/sky_atmosphere.jpg*

Choosing a suitable simulation environment is difficult. There are a number of tools on the market - some open source, others must be purchased. In this post we use carla. Firstly because it is available for free, and secondly because a large number of sensors and a ROS connection is available.

To mention some of the core features: 
 - Scalability via a server multi-client architecture
 - Autonomous Driving sensor suite
 - Maps generation
 - Traffic scenarios simulation
 - ROS integration
 - Autonomous Driving baselines

At this point, carla is up and running (at least we have mastered [Part II]({{site.baseurl}}/2020-10-15/dream-team-carla-ros-coros-part-2))! But how do we add some cars and gather the sensor data? Actually one could now use directly the carla python API. This works fine in case you are only working within the Carla software. 

But what happens if you want to test your algorithm on a different platform - like on another simulation environment, a scaled vehicle or even the real car. This is where ROS comes into play.

<br>
# ROS


ROS delivers an abstraction between our algorithm and the environment (remember simulation or real car). But what exactly is ROS?

This is what the ROS-Docu says:
> The Robot Operating System (ROS) is a flexible framework for writing robot software. It is a collection of tools, libraries, and conventions that aim to simplify the task of creating complex and robust robot behavior across a wide variety of robotic platforms -
*https://www.ros.org/about-ros/*

<br>
To simplify things roughly: ROS is a framework that handles the **message exchange** of **encapsulated functions**. There are defined **message types** and a variety of **tools** for visualization, signal processing and so on.

Imagine that our task is to build a lane keeping function that can calculate a steering angle from images taken by a front facing camera. We could now write in a programming language our choice of a big monolith that fulfils this task, like: 

![monolith]({{site.baseurl}}/assets/intro/mono.png)
*large, encapsulated monolith approach*

This works, no question, but what if we want to replace parts of our Lane Keeping Assistant, such as lane detection? Now we would have to dive deep into the code again and change the software.

The ROS approach, by contrast, is a completely different one. Instead of having one big application, we divide the logical steps into subtasks. 

And this is exactly where the difference comes in: these subtasks are not represented by pure object oriented programming - like e.g. one class for lane recognition and another one for steering control - but the **tasks form their own program**, their own executable:

![ros approach]({{site.baseurl}}/assets/intro/ros.png)
*ROS approach, similar to a microservice architecture*

This approach is similar to a splitting into microservices, referred as ROS nodes. No matter in which programming language the nodes are written. The arrangement of the nodes to the overall application and the communication within it is task of ROS and does not burden us further.

What is really cool about ROS is that the community provides a large number of nodes. So, following our example, we can download nodes for trajectory calculation as well as for steering control and can fully concentrate on lane detection. The replacement of nodes is plug and play. You can find a full list of available ROS packages in the [ROS Index](https://index.ros.org/packages/).

Up to now, everything sounds wonderfully simple, but there are a few practical barriers, especially when installing ROS. It gets really tough on Windows. Especially for students with little experience in ROS and Linux who are eager to get into the development of their AD function, this can already be a showstopper.

And this brings us to the introduction of the last tool in this post: COROS.

<br>
# COROS
The goal of COROS is to provide a complete ROS environment with full GUI access while keeping installation effort to a minimum - there are really only two lines in the terminal needed - regardless of Linux, Windows or Mac.

COROS is a Docker image, which offers a full Linux-ROS environment including IDE ([Visual Studio Code](https://code.visualstudio.com)) via the web browser including all ROS tools like RViz:

![example of COROS in your browser](https://raw.githubusercontent.com/gismo07/coros/master/assets/coros_web.png)
*example of COROS in your browser*

<br>
# What's next?
At this point we naturally want to try the whole thing out! That brings us to [Part 2 - Installation]({{site.baseurl}}/2020-10-15/dream-team-carla-ros-coros-part-2).
