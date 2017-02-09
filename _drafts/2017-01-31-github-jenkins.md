---
layout: post
title: "RaspberryPi running Jenkins for GitHub"
description: ""
category:
tags: []
---
{% include JB/setup %}

# Preface

Continuous integration comes very helpful when a project grows out of a simple
hello world application. GitHub with Travis integration gives very easy
solution to this problem. There're tons of tutorials how to setup GitHub project
with Travis CI. For a non-embedded software it is definitely a way to go. But if
you want to add ability to run tests on a real hardware Travis and other could
based solutions do not fit.

# esp-open-rtos

I think esp-open-rtos had Travis CI from the very start. And it helped a lot
for the project to keep compilation errors away. As with any new PR all
examples were built and reveal any compile-time errors.

But run-time errors were not checked in any way. So, the obvious solution is
to create a standalone test server that would run tests on a real hardware.

I had an old Raspberry PI laying around and decided to give it a try as a test
server.

## Jenkins installation

Refere the [official Jenkins documentation](1) for detail installation instructions.

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

