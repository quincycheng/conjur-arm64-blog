# conjur-arm64-blog

This is the draft of conjur-arm64 blog.

----

# TL;DR

This blog is about my jounry of building [CyberArk Conjur](https://www.conjur.org/ container image that works on both ARM-based & Intel/AMD-based computer.

If you're looking for a solution to execute Conjur on Pi4, you can download [docker-compose.yml](docker-compose.yml) & [.env](.env) files to spin up a the `quincycheng/conjur:latest` image that I built

# Why I am doing this?

As an IOT & single-board-computer lover, I recently pick up a Raspberry Pi 4 board as an upgrade of Pi2.
It got enough processing power to execute container-based apps, while operates in low voltage powered by USB.

Considering the power consumed by homelab VM farm, guess it's a good idea to run the 7x24 systems on Pi 4 instead of 220V VM systems.

![pi 4 & pi2](./media/pi.jpg)
*(left) Raspberry Pi 4 in Argon NEO Heatsink Case (right) Raspberry Pi 2 with 3.5" LCD HAT*


One of the apps that I use is [CyberArk Conjur](https://www.conjur.org/).    Together with [summon](https://cyberark.github.io/summon/), it's an awesome way to inject secrets to my container-based apps as well as CLI.

To make sure it can be executed on Pi4, we need to make sure the applications is arm64 compatible.
The first thing that I checked is Docker Hub.
After a quick search, I found that link to the image is [https://hub.docker.com/repository/docker/cyberark/conjur](https://hub.docker.com/repository/docker/cyberark/conjur)

![screen capture 1](./media/conjur-arm64-1.png)

I have two news - good and bad.

The bad news is As you can see if above screen capture, the offical repo got `linux/amd64` images, and no `linux/arm64`.  

The good news is, Fortunately, Conjur is an open source ource project, so why don't we build one for `linux/arm64`?   


# Going down the rabbit hole

Okay, first thing first, let's get the source code.
With the [link](https://github.com/cyberark/conjur) from [Docker Hub](https://hub.docker.com/repository/docker/cyberark/conjur), I can quickly access GitHub project page.   

![screen capture 2](./media/conjur-arm64-2.png)
*Conjur project on GitHub*


Let's create a folder to clone the project.
With `git clone https://github.com/cyberark/conjur.git`, we got the source code less than a second, literally.  
I notice that Conjur is written in Ruby, which should be portable and can easily port for arm64 using `ruby` images, right?

![screen capture 3](./media/conjur-arm64-3.png)
*Cloning Conjur project from GitHub*

Container defination is located in `Dockerfile` file, and the base image for assemble is set by `FROM` statement

![screen capture 4](./media/conjur-arm64-4.png)
*`From` statement in `Dockerfile` from `conjur` project *

![screen capture 5](./media/conjur-arm64-5.png)
*No arm64 support from `cyberark/ubuntu-ruby-fips` on docker hub*


![screen capture 6](./media/conjur-arm64-6.png)
*Link to GitHub from `cyberark/ubuntu-ruby-fips` on docker hub*


![screen capture 7](./media/conjur-arm64-7.png)
*`cyberark/conjur-base-image`on GitHub*


![screen capture 8](./media/conjur-arm64-8.png)
*`From` statement in `Dockerfile` from `ubuntu-ruby-fips` project *



![screen capture 9](./media/conjur-arm64-9.png)
*`From` statement in `Dockerfile` from sub-projects under `ubuntu-ruby-fips`*



![screen capture 10](./media/conjur-arm64-10.png)
*Hierarchy of image relationship*


# 1st image: openssl-builder

![screen capture 11](./media/conjur-arm64-11.png)
*`buildx.sh`for building both amd64 & arm64 `openssl-builder` image*

![screen capture 12](./media/conjur-arm64-12.png)
*building `openssl-builder` image*


14:37:56
13:59:23


![screen capture 13](./media/conjur-arm64-13.png)
*Built 38 mins and get an error *


https://github.com/openssl/openssl/issues/11105


![screen capture 14](./media/conjur-arm64-14.png)
*"Building on aarch64 with fips" issue from `openssl` on Github*

Based on https://www.openssl.org/docs/fips.html, at the time of writing this blog, it said `Neither validation will work with any release other than 1.0.2`.
So guess we need to stick with `openssl 1.0.2`.

So back to the [issue page](https://github.com/openssl/openssl/issues/11105), there is a workaround which does not require any source code modifications.
Awesome!   Shout to [@alexw91](https://github.com/alexw91)

![screen capture 15](./media/conjur-arm64-15.png)
*The solution to fix the compile issue*

![screen capture 16](./media/conjur-arm64-16.png)
*Updated `Dockerfile`*


56 Minutes and 50 Seconds


![screen capture 19](./media/conjur-arm64-19.png)
*Built `openssl-builder` successfully in 3410.1s

![screen capture 20](./media/conjur-arm64-20.png)

# 2nd iamge: ubuntu-ruby-builder

![screen capture 21](./media/conjur-arm64-21.png)
![screen capture 22](./media/conjur-arm64-22.png)
![screen capture 23](./media/conjur-arm64-23.png)
![screen capture 24](./media/conjur-arm64-24.png)

# 3rd image: postgres-client-builder

![screen capture 25](./media/conjur-arm64-25.png)
![screen capture 26](./media/conjur-arm64-26.png)
![screen capture 27](./media/conjur-arm64-27.png)
![screen capture 28](./media/conjur-arm64-28.png)
![screen capture 29](./media/conjur-arm64-29.png)

# One level up! 4th image: ubuntu-ruby-fips

![screen capture 30](./media/conjur-arm64-30.png)
![screen capture 31](./media/conjur-arm64-31.png)
![screen capture 32](./media/conjur-arm64-32.png)
![screen capture 33](./media/conjur-arm64-33.png)


# Building conjur image, again

![screen capture 34](./media/conjur-arm64-34.png)
![screen capture 35](./media/conjur-arm64-35.png)
![screen capture 36](./media/conjur-arm64-36.png)


# Spin it up, with errors?

![screen capture 37](./media/conjur-arm64-37.png)
![screen capture 38](./media/conjur-arm64-38.png)

# Debug and try again

![screen capture 39](./media/conjur-arm64-39.png)
![screen capture 40](./media/conjur-arm64-40.png)
![screen capture 41](./media/conjur-arm64-41.png)

# Lessons Learnt



