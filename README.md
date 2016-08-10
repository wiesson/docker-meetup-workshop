# Tech Overview
* Docker Daemon  => Runtime for linux containers
* Docker Client  => User Interface for Docker Daemon
* Images => Read-Only Template to start Container, consists of a series of layers
* Union File System =>  Combine Layers (Branches) to Image -> fast because only changes are added/updated -> Fast pulling/building
* Docker Registry => Stores Images public/private (Dockerhub, hosted docker registry)

# Preparation
* [Mac Os X](https://download.docker.com/mac/stable/Docker.dmg)
* [Windows](https://download.docker.com/win/stable/InstallDocker.msi)
* [Linux Docker Engine](https://docs.docker.com/engine/installation/linux/)
* [Linux Docker Compose](https://github.com/docker/compose/releases/download/1.8.0/docker-compose-Linux-x86_64)
* [Create docker id]( https://hub.docker.com/register/)

# First Steps
Hello World:
```
docker run IMAGENAME COMMAND
docker run ubuntu /bin/echo 'Hello world'
```

Checkout image size:
```
docker images |grep ubuntu
```

Run `hello world` on alpine container
```
docker run alpine echo 'Hello world'
```

Why Alpine?
* Only 5MB
* Great [Package Manager](http://forum.alpinelinux.org/packages)

Checkout running processes (Execute every command in a separate terminal window)
```
docker run alpine sleep 1200
docker ps
```

Use id from `docker ps` to stop container
```
docker stop -t 1 c84f5a6e6d51   # -t Seconds allowed to finished process (default: 10)
```

Checkout logs
```
docker logs id
```

Show ALL containers
```
docker ps -a # -a Show all containers (default: only running)
docker rm ID #Delete Container
```

Show only docker container ids
```
docker ps -q # -q show only container ids
```

Remove old container
```
docker rm $(docker ps -aq)
```

Inspect container/image details
```
docker inspect container-id/image-id
```

Run container in Background
```
docker run -d --name relaxo alpine sleep 1200   # -d start process in background
```

Show resource usage of container
```
docker stats relaxo   # Cancel with [CTRL/CMD]+[C]
```

Show processes in container
```
docker top my-proxy   # Cancel with [CTRL/CMD]+[C]
```

Start nginx(webserver) container with image nginx (tag: alpine) and download website
```
docker run -d --name my-proxy nginx:alpine
docker exec my-proxy netstat -tulpen        # Show Services listening
docker exec my-proxy ls                     # Execute `ls` command in container
docker exec my-proxy wget http://localhost  # Download website from nginx
docker exec my-proxy ls                     # Check if website was downloaded
docker exec my-proxy cat index.html         # Show website
```

"But i want ssh on my container!" - "NO!"
```
docker exec -ti my-proxy /bin/sh # -t allocate tty / -i interactive
# You should have a shell on your container now
cat /etc/alpine-release
```

Start nginx(webserver) and `PUBLISH` all ports (delete old container with name my-proxy first)
```
docker stop my-proxy                            # Stop old container
docker rm my-proxy                              # Delete old container
docker run -d -P --name my-proxy nginx:alpine   # -P Publish all ports on random Port
docker ps                                       # 0.0.0.0:32829->80/tcp

# Open your local browser and check out http://localhost:32829
docker exec my-proxy /bin/sh -c 'echo "Test" > /usr/share/nginx/html/index.html'    Example to change index.html

# Recheck Website
```

Start new container and connect to nginx container
```
docker run -ti --link my-proxy alpine /bin/sh
env
cat /etc/hosts
ping my-proxy
apk add --update curl   # apk = apt/yum package manager alpine
curl my-proxy:80
```

Working with volume mounts
```
# First create index.html
echo "Some Test Text" > index.html

# Run container and mount local directory into container
docker run -P -v $(pwd):/usr/share/nginx/html:ro nginx:alpine     # -v HOST_PATH:CONTAINER_PATH / :ro => Read only
```

Create our own image template
```
touch Dockerfile
```

Create file `Dockerfile` and paste following content
```
FROM nginx:alpine

WORKDIR /usr/share/nginx/html/

ADD index.html .
```

Build image
```
docker build .
```

Build image with name
```
docker build . -t test
```

Build image without cache
```
docker build . --no-cache
```

Build image and check if there is a new base image
```
docker build --pull .
```

Login in your account
```
docker login
```

Push image (you need a dockerhub account)
```
docker build . -t YOURID/testimage
```

Change index.html and rebuild image
```
echo 'v2' >> index.html
docker build . -t YOURID/testimage:v2
docker push YOURID/testimage:v2
```

# Links
* [Networking](https://www.ctl.io/developers/blog/post/docker-networking-rules/)
* [Docker Volumes](https://docs.docker.com/v1.10/engine/userguide/containers/dockervolumes/)
* [Best Practise](https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/)
* [Open Source Registry](https://github.com/docker/distribution)
* [Startup Order Wrapper Method](https://docs.docker.com/compose/startup-order/)
* [Monitoring](https://opsnotice.xyz/how-to-monitor-docker-hosts/)
* [Logging/Fluentd](http://www.fluentd.org/guides/recipes/docker-logging)
* [Awesome Docker](https://github.com/veggiemonk/awesome-docker)
* [Security](https://github.com/coreos/clair)
