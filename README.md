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

As you can see if above screen capture, the offical repo got `linux/amd64` images, and no `linux/arm64`.
Before I give up the an idea, why don't we build one? 
It's an Open Source project, right?   


# Going down the rabbit hole

Okay, first thing first, let's get the source code.
With the [link](https://github.com/cyberark/conjur) from Docker Hub 

![screen capture 2](./media/conjur-arm64-2.png)
![screen capture 3](./media/conjur-arm64-3.png)
![screen capture 4](./media/conjur-arm64-4.png)
![screen capture 5](./media/conjur-arm64-5.png)
![screen capture 6](./media/conjur-arm64-6.png)
![screen capture 7](./media/conjur-arm64-7.png)
![screen capture 8](./media/conjur-arm64-8.png)
![screen capture 9](./media/conjur-arm64-9.png)
![screen capture 10](./media/conjur-arm64-10.png)

# 1st image: openssl-builder

![screen capture 11](./media/conjur-arm64-11.png)
![screen capture 12](./media/conjur-arm64-12.png)
![screen capture 13](./media/conjur-arm64-13.png)
![screen capture 14](./media/conjur-arm64-14.png)
![screen capture 15](./media/conjur-arm64-15.png)
![screen capture 16](./media/conjur-arm64-16.png)
![screen capture 17](./media/conjur-arm64-17.png)
![screen capture 18](./media/conjur-arm64-18.png)
![screen capture 19](./media/conjur-arm64-19.png)
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



