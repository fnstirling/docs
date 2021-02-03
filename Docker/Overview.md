# Overview

Delete unnecessary containers and images, [read more](https://docs.docker.com/docker-for-mac/space/)
``` shell
# Check whether you have any unnecessary containers and images
docker system df -v

# List images
$ docker image ls

# List containers
$ docker container ls -a

# If redundant objects run
$ docker system prune

#
```