# Overview

Delete unnecessary containers and images, [read more](https://docs.docker.com/docker-for-mac/space/)
``` shell
# Check whether you have any unnecessary containers and images
docker system df -v

# List images
docker image ls

# List containers
docker container ls -a

# If redundant objects run
docker system prune

# Inspect a list of running services
docker ps

# Stop service
docker stop CONTAINER_ID

# List images
docker images
docker images -a

# Pull applicatin image
docker pull USERNAME/IMAGE_NAME

# Build
docker build -t USERNAME/IMAGE_NAME .

# Run container
# -p: This publishes the port on the container and maps it to a port on our host. We will use port 80 on the host, but you should feel free to modify this as necessary if you have another process running on that port. For more information about how this works, see this discussion in the Docker docs on port binding.
# -d: This runs the container in the background.
# --name: This allows us to give the container a memorable name.
docker run --name MEMORABLE_NAME -p 80:8080 -d USERNAME/IMAGE_NAME

# Login to docker hub
# When prompted, enter your Docker Hub account password. Logging in this way will create a ~/.docker/config.json file in your user’s home directory with your Docker Hub credentials.
docker login -u USERNAME

# Push image to Docker Hub
docker push USERNAME/IMAGE_NAME
```