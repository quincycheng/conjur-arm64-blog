# conjur-arm64-blog

This is the draft of conjur-arm64 blog.

----

# TL;DR

This blog is about my journey of building [CyberArk Conjur](https://www.conjur.org/) container image that works on both ARM-based & Intel/AMD-based computer.

If you're looking for a solution to execute Conjur on Pi4, you can download [docker-compose.yml](docker-compose.yml) & [.env](.env) files to spin up a the `quincycheng/conjur:latest` image that I built

# Why I am doing this?

As an IOT & single-board-computer lover, I recently pick up a Raspberry Pi 4 board as an upgrade of Pi2.
Pi4 SDB is very nice SBC, that got enough processing power to execute container-based apps, while keep operating in low voltage powered by USB.

Considering the power consumed by homelab VM servers, guess it's a good idea to run the 7x24 systems on Pi 4 and power on my 220V VM systems only when I need them.

<img src="./media/pi.jpg" width="50%" height="50%"/>

*(left) Raspberry Pi 4 in Argon NEO Heatsink Case (right) Raspberry Pi 2 with 3.5" LCD HAT*


One of the apps that I use is [CyberArk Conjur](https://www.conjur.org/).    Together with [summon](https://cyberark.github.io/summon/), it's an awesome way to inject secrets to my container-based apps, CLI and automation tools like [Ansible](https://galaxy.ansible.com/cyberark/conjur).

To run applications on Pi4, we need to make sure the applications are arm64 compatible.

Conjur is publicily avaliable as container image, let's check Docker Hub.

After a quick search, I found that link to the image is [https://hub.docker.com/repository/docker/cyberark/conjur](https://hub.docker.com/repository/docker/cyberark/conjur)

<img src="./media/conjur-arm64-1.png" width="50%" height="50%"/>

Now, I have two news - good and bad.

The bad news is, as you can see in above screen capture, the offical repo got `linux/amd64` images, and no `linux/arm64`.  

The good news is, Conjur is an open source ource project, meaning that we could build an container image with both `linux/arm64` & `linux/amd64`


# Going down the rabbit hole

Okay, first thing first, let's get the source code.
With the [link](https://github.com/cyberark/conjur) from [Docker Hub](https://hub.docker.com/repository/docker/cyberark/conjur), I can quickly access GitHub project page.   

<img src="./media/conjur-arm64-2.png" width="50%" height="50%"/>

*Conjur project on GitHub*

Let's create a folder to clone the project.
With `git clone https://github.com/cyberark/conjur.git`, we got the source code less than a second, literally.  
I notice that Conjur is written in Ruby, which should be portable and can easily port for arm64 using `ruby` images, right?

<img src="./media/conjur-arm64-3.png" width="50%" height="50%"/>

*Cloning Conjur project from GitHub*

Container defination is located in `Dockerfile` file, and the base image for assemble is set by `FROM` statement

<img src="./media/conjur-arm64-4.png" width="50%" height="50%"/>

*`From` statement in `Dockerfile` from `conjur` project*

<img src="./media/conjur-arm64-5.png" width="50%" height="50%"/>
*No arm64 support from `cyberark/ubuntu-ruby-fips` on docker hub*


<img src="./media/conjur-arm64-6.png" width="50%" height="50%"/>
*Link to GitHub from `cyberark/ubuntu-ruby-fips` on docker hub*


<img src="./media/conjur-arm64-7.png" width="50%" height="50%"/>
*`cyberark/conjur-base-image`on GitHub*


<img src="./media/conjur-arm64-8.png" width="50%" height="50%"/>
*`From` statement in `Dockerfile` from `ubuntu-ruby-fips` project*



<img src="./media/conjur-arm64-9.png" width="50%" height="50%"/>
*`From` statement in `Dockerfile` from sub-projects under `ubuntu-ruby-fips`*



<img src="./media/conjur-arm64-10.png" width="50%" height="50%"/>
*Hierarchy of image relationship*


# 1st image: openssl-builder

<img src="./media/conjur-arm64-11.png" width="50%" height="50%"/>
*`buildx.sh`for building both amd64 & arm64 `openssl-builder` image*

<img src="./media/conjur-arm64-12.png" width="50%" height="50%"/>
*building `openssl-builder` image*


14:37:56
13:59:23


<img src="./media/conjur-arm64-13.png" width="50%" height="50%"/>

*Built 38 mins and get an error *


https://github.com/openssl/openssl/issues/11105


<img src="./media/conjur-arm64-14.png" width="50%" height="50%"/>
*"Building on aarch64 with fips" issue from `openssl` on Github*

Based on https://www.openssl.org/docs/fips.html, at the time of writing this blog, it said `Neither validation will work with any release other than 1.0.2`.
So guess we need to stick with `openssl 1.0.2`.

So back to the [issue page](https://github.com/openssl/openssl/issues/11105), there is a workaround which does not require any source code modifications.
Awesome!   Shout to [@alexw91](https://github.com/alexw91)

<img src="./media/conjur-arm64-15.png" width="50%" height="50%"/>

*The solution to fix the compile issue*

<img src="./media/conjur-arm64-16.png" width="50%" height="50%"/>

*Updated `Dockerfile`*


56 Minutes and 50 Seconds


<img src="./media/conjur-arm64-19.png" width="50%" height="50%"/>

*Built `openssl-builder` successfully in 3410.1s

<img src="./media/conjur-arm64-20.png" width="50%" height="50%"/>

# 2nd iamge: ubuntu-ruby-builder

<img src="./media/conjur-arm64-21.png" width="50%" height="50%"/>
<img src="./media/conjur-arm64-22.png" width="50%" height="50%"/>
<img src="./media/conjur-arm64-23.png" width="50%" height="50%"/>
<img src="./media/conjur-arm64-24.png" width="50%" height="50%"/>


# 3rd image: postgres-client-builder

<img src="./media/conjur-arm64-25.png" width="50%" height="50%"/>
<img src="./media/conjur-arm64-26.png" width="50%" height="50%"/>
<img src="./media/conjur-arm64-27.png" width="50%" height="50%"/>
<img src="./media/conjur-arm64-28.png" width="50%" height="50%"/>
<img src="./media/conjur-arm64-29.png" width="50%" height="50%"/>


# One level up! 4th image: ubuntu-ruby-fips

<img src="./media/conjur-arm64-30.png" width="50%" height="50%"/>
<img src="./media/conjur-arm64-31.png" width="50%" height="50%"/>
<img src="./media/conjur-arm64-32.png" width="50%" height="50%"/>
<img src="./media/conjur-arm64-33.png" width="50%" height="50%"/>



# Building conjur image, again

<img src="./media/conjur-arm64-34.png" width="50%" height="50%"/>
<img src="./media/conjur-arm64-35.png" width="50%" height="50%"/>


Below is the [.env](.env)


```Shell
CONJUR_DATA_KEY=<place your data key here, can be generated by `docker-compose exec conjur conjurctl account create default`>
POSTGRES_PASSWORD=<place your database password here, can be any random string>
```

Below is the [docker-compose.yml](docker-compose.yml)

```yaml
version: '3'
services:
  database:
    image: postgres:10.14
    container_name: postgres_database
    environment:
      POSTGRES_PASSWORD: "${POSTGRES_PASSWORD}"
    volumes:
    - ./postgres-data:/var/lib/postgresql/data

  conjur:
    image: quincycheng/conjur
    container_name: conjur_server
    command: server
    environment:
      DATABASE_URL: "postgres://postgres:${POSTGRES_PASSWORD}@database/postgres"
      CONJUR_DATA_KEY: "${CONJUR_DATA_KEY}"
    depends_on:
    - database
    restart: on-failure
    ports:
      - "8888:80"
```

# Spin it up, with errors?

<img src="./media/conjur-arm64-37.png" width="50%" height="50%"/>
<img src="./media/conjur-arm64-38.png" width="50%" height="50%"/>


# Debug and try again

<img src="./media/conjur-arm64-39.png" width="50%" height="50%"/>
<img src="./media/conjur-arm64-40.png" width="50%" height="50%"/>
<img src="./media/conjur-arm64-41.png" width="50%" height="50%"/>


# Lessons Learnt



