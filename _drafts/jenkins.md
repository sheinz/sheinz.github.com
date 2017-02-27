---
layout: post
title: "RaspberryPi running Jenkins for GitHub"
description: ""
category:
tags: []
---

## Preface

Continuous integration comes very helpful when a project grows out of a simple
hello world application. GitHub with Travis integration gives very easy
solution to this problem. There're tons of tutorials how to setup GitHub project
with Travis CI. For a non-embedded software it is definitely a way to go. But if
you want to add ability to run tests on a real hardware Travis and other could
based solutions do not fit.

### esp-open-rtos

I think esp-open-rtos had Travis CI from the very start. And it helped a lot
for the project to keep compilation errors away. As with any new PR all
examples were built and reveal any compile-time errors.

But run-time errors were not checked in any way. So, the obvious solution is
to create a standalone test server that would run tests on a real hardware.

I had an old Raspberry PI laying around and decided to give it a try as a test
server.

## Hardware

I used official image of RASPBIAN JESSIE LITE for the described setup.

### Performance
I have a Raspberry PI model B with 512MB RAM. It has a single core ARM CPU.
Even though it seems like a suitable setup for a small project build server,
Jenkins web interface is terribly slow. I'm not sure why with every web
request it consumes so much CPU. Nevertheless the setup has proved to be working.
If there's an opportunity to use better hardware for running Jenkins it's
definitely a good idea.

### Power supply
Raspberry PI needs a decent power supply to operate without glitches. If you
want to power esp8266 boards from USB ports, the power supply needs to be even
more powerful. I used 5V 2A power supply that I bought on ebay (a decent one).
Another important note is how to connect power to Raspberry PI so that USB ports
will receive enough current without losing it in PCB traces. I connected the
power supply directly to the USB ports on Raspberry PI.

[image]

## esp-open-sdk

To run tests they must be built at first. A firmware for esp8266 is built with
a toolchain for xtensa architecture. Conveniently esp-open-sdk provides
everything you need to build esp8266 firmware. So, we need to install it
for Raspberry PI.

### esp-open-sdk installation

The installation process is described at esp-open-sdk github page. It will take
quite a long time to build all parts of the toolchain on Raspberry PI.

Install all the dependencies for esp-open-sdk installation:
```bash
sudo apt-get install make unrar-free autoconf automake libtool gcc g++ gperf \
    flex bison texinfo gawk ncurses-dev libexpat-dev python-dev python python-serial \
    sed git unzip bash help2man wget bzip2 libtool-bin
```

Get the latest version of esp-open-sdk:
```bash
git clone --recursive https://github.com/pfalcon/esp-open-sdk.git
```

Build it:
```bash
make STANDALONE=n
```


## Jenkins installation

Refer the [official Jenkins documentation](1) for detail installation instructions.

```bash
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo echo "deb https://pkg.jenkins.io/debian-stable binary/" >> /etc/apt/sources.list
sudo apt-get update
sudo apt-get install jenkins
```

After installation Jenkins will start a post-installtion procedure which will
take quite a long time (up to an hour).
You can check with `htop` that jenkins is consuming 100% CPU during this period.
The web interface will not be available during this period.
The progress can be checked in `/var/log/jenkins/jenkins.log`

## esp-open-sdk

Refere the [official esp-open-sdk documentation](2) for detail instructions.

There's one problem with building esp-open-sdk for Raspberry PI.
As RPI has only 512 MB of RAM the build might fail due to RAM shortage when
building multiple jobs simultaneously.

[1](https://pkg.jenkins.io/debian-stable/)

