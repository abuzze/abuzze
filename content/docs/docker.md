---
title: "Docker Cheatcheet"
date: "26.07.2021"
draft: false
tags: ['cheat sheet']
---
# Docker Cheatsheat


## Cleanup

Diskspace and resources used
```
    docker system df
```
The RECLAIMABLE column shows how much diskspace can be saved by deleting unused images

The size of an image
```
    docker image ls
```
The content of a volume. This will show the mountpoint, which can be inspected with du.
```
    docker volume ls
    docker volume inspect 69915af8ddf99
```
Remove all containers and their volumes (works only if all containers are stopped)
```
    docker ps -a -q | xargs docker rm -v
```
List/remove all stopped containers
```
    docker container ls --filter "status=exited" -aq
    docker container rm $(docker container ls --filter "status=exited" -aq)
```
Remove all dangling images
```
    docker images -f dangling=true -q | xargs docker rmi
```
Remove docker volumes
```
    docker volume rm $(docker volume ls -q)
```
Remove networks
```
    docker network rm <NETWORK_ID>
```
Prune all unused resources, exept volumes

    WARNING! This will remove:
    - all stopped containers
    - all networks not used by at least one container
    - all dangling images
    - all dangling build cache

```
docker system prune
```

Running a cleanup job is possible, via a container: [https://github.com/meltwater/docker-cleanup](https://github.com/meltwater/docker-cleanup)