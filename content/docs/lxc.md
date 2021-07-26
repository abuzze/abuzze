---
title: "LXC Cheatsheet"
date: "26.07.2021"
draft: false
tags: ['cheat sheet']
---
# LXC Config Cheatsheet

first lxd config

	lxd init

check the zpool
```
zpool list
zpool status
```
check zfs

	zfs list

show lxd config

	lxc profile show default

Public images

	lxc image list images:

Install and run an image

	lxc launch images:alpine/3.8/i386 alpine-test

Finding containers
```
$ lxc list --fast
$ lxc list | grep RUNNING
$ lxc list | grep STOPPED
```
runnnig commands in containers
```
lxc exec containerName -- command
lxc exec containerName -- /path/to/script
lxc exec containerName --env EDITOR=/usr/bin/vim -- command
```
enter a shell in a container

	lxc exec debian9-c4 bash

start/stop/restart/delete a container

	lxc [start|stop|restart|delete] containerName

show infos about a container

	lxc info containerName

add a br0 to neplan config
```
bridges:
        br0:
          interfaces: [enp0s31f6]
          dhcp4: true
          dhcp6: true
```

Change the default lxc bridge
```
lxc profile edit default.

config: {}
description: Default LXD profile
devices:
  eth0:
    name: eth0
    nictype: bridged
    parent: br0 #change this to br0
    type: nic
name: default
```
## show the config for a container

	 lxc config show containerName

## Autostarting containers on boot
```
lxc config set pihole boot.autostart true
lxc config get pihole boot.autostart
```

you can also define the priority if you're starting more than one container and the delay
```
$ lxc config set {vm-name} boot.autostart.priority integer
$ lxc config set {vm-name} boot.autostart.delay integer
```
## share folder from the host to the container

https://ubuntu.com/blog/mounting-your-home-directory-in-lxd

Example
```
echo 'root:1000:1' | sudo tee -a /etc/subuid /etc/subgid
lxc config set containerName raw.idmap 'uid 1000 105'
lxc config set containerName raw.idmap 'gid 0 0'
lxc config device add containerName homedir disk source=/home/tycho path=/home/ubuntu
```
only the user that owns the folder inside the container, can write to it. Even root is not allowed!
