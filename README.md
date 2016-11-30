# CI&T Memcached Docker image(s)

These Docker image(s) intends to be a containerized Memcached solution for multiple purposes.

The source code is available under GPLv3 at Github in this [link]((https://github.com/ciandt-dev/docker-hub-memcached).

By utilizing Docker technologies, that already provides an easy way of spinning up new environments along with its dependecies. This image can speed up developers which different backgrounds and equipments to create quickly a new local environment allowing them to easily integrate in automated tests and deployment pipelines.

At this moment we have the following version(s).

## [Acquia](#acquia)

Our intent is to be a Docker container that mimics Memcached running on Acquia environment with the same version of softwares, packages, modules and its underlying operating system.

Acquia publishes a table with its platform infrastructure information on the link: https://docs.acquia.com/cloud/arch/tech-platform

These images will have the following name pattern: __acquia-*YYYY-MM-DD*__

#### [*Bundled software versions*](#software-versions)

These are the currently software versions bundled in the image(s) by tag.

* acquia-latest __OR__ acquia-2016-11-30
  * Ubuntu 12.04.5
  * Memcached 1.4.13
  * Dumb-init 1.2.0

__*Deprecated*__

* acquia-2016-11-08
  * Ubuntu 12.04.5
  * Memcached 1.4.13
  * Dumb-init 1.2.0

### [*Quick Start*](#quickstart)

__Download the image__

```
docker pull ciandtsoftware/memcached:acquia-latest
```

__Run a container__

```
docker run \
  --name myContainer \
  --detach \
  ciandtsoftware/memcached:acquia-latest
```

__Check running containers__

```
docker ps --all
```

* * *

### [Software Versions](#software-versions)

These are the currently versions bundled in this image.

Already installed

* Ubuntu 12.04.5
* Memcached 1.4.13
* Dumb-init 1.2.0

* * *

### [Running standalone](#running-standalone)

If you just need the container there is a snippet that can help running in standalone mode.

```
# define variables
DOCKER_CONTAINER_NAME="myContainer"
DOCKER_IMAGE="memcached:acquia-latest"

# run your container
docker run \
  --name "${DOCKER_CONTAINER_NAME}" \
  --detach \
  "${DOCKER_IMAGE}"
```

After run, you can inquiry Docker service and get the IP address and port of your newly running container named __myContainer__ by using the following command:

```
docker inspect --format '{{ .NetworkSettings.IPAddress }} myContainer'
```

Let's suppose that the returned IP address was __172.17.0.2__.
Since Memcached standard port is __11211__, just open a terminal and try to get the stats: access:

```
echo "stats" | nc 172.17.0.2 11211
```

Apache Memcached stats result should be returned perfectly.

* * *

### [Running in Docker-Compose](#running-docker-compose)

Since a project is not going to use solely this container, it may need a Docker-Compose file.

Just to exercise, follow an example of this running with __Apache/PHP__ and also both behind a __Nginx__ proxy.

Create a new folder and fill with these 3 files and respective folders;

##### [__conf/acquia.local.env__](#acquia-env)

```
## Nginx proxy configuration
# https://hub.docker.com/r/jwilder/nginx-proxy/
VIRTUAL_HOST=mySite.local
```

##### [__conf/memcached.local.env__](#acquia-env)

```
## Nginx proxy configuration
# https://hub.docker.com/r/jwilder/nginx-proxy/
VIRTUAL_HOST=myMemcached.local
VIRTUAL_PORT=11211
```

##### [__docker-compose.yml__](#docker-compose)

```
memcached:
  image: ciandtsoftware/memcached:acquia-latest
  container_name: memcached
  env_file: ../conf/memcached.local.env

acquia:
  build: ./acquia
  container_name: acquia
  env_file: ../conf/acquia.local.env
  links:
    - memcached

nginx:
  image: jwilder/nginx-proxy:latest
  container_name: nginx
  volumes:
    - /var/run/docker.sock:/tmp/docker.sock:ro
  ports:
    - "80:80"
    - "443:443"
```

Then just spin-up your Docker-Compose with the command:

```
docker-compose up -d
```

Inspect Nginx container IP address:

```
docker inspect \
        --format \
        "{{.NetworkSettings.Networks.bridge.IPAddress }}" \
        "nginx"
```

Use the IP address to update __hosts__ file. Let's suppose that was 172.17.0.2.

Then, add the entries to __/etc/hosts__.

> 172.17.0.2 acquia.local
> 172.17.0.2 memcached.local

And now, try to access in terminal

```
echo "stats" | nc memcached.local 11211
```

Voilà!
Your project now have Memcached, Apache/PHP and Nginx up and running.
\\o/

* * *

## [User Feedback](#user-feedback)

### [*Issues*](#issues)

If you have problems, bugs, issues with or questions about this, please reach us in [Github issues page](https://github.com/ciandt-dev/docker-hub-memcached/issues).

__Needless to say__, please do a little research before posting.

### [*Contributing*](#contributing)

We gladly invite you to contribute fixes, new features, or updates, large or small; we are always thrilled to receive pull requests, and do our best to process them as fast as we can.

Before you start to code, we recommend discussing your plans through a GitHub issue, especially for more ambitious contributions. This gives other contributors a chance to point you in the right direction, give you feedback on your design, and help you find out if someone else is working on the same thing.

### [*Documentation*](#documentation)

There are __two parts__ of the documentation.

First, in the master branch, is this README.MD. It explains how this little scripts framework work and it is published on [Github page](https://github.com/ciandt-dev/docker-hub-memcached).

Second, in each image version there is an additional README.MD file that explains how to use that specific Docker image version itself. __*Latest version*__ is always the one seen on [Docker Hub page](https://hub.docker.com/r/ciandtsoftware/memcached).

We strongly encourage reading it too!

* * *

Happy coding, enjoy!!

"We develop people before we develop software" - Cesar Gon, CEO

* * *
