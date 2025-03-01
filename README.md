# vlang

Docker containers for V.

## Summary

- [About](#about)
- [Features](#features)
- [Examples](#examples)
- [Available containers](#available-containers)
- [Test](#test)

## About

I created these Docker containers to have disposable container for given V versions. I plan to follow V version, as well as each version after the v1 release, so you can choose your containers given the V version and OS of your choice.

## Features

- Provides containers for specific V version
- Provide containers that is always up to date with the latest changes (master branch) of the V Github repository
- Provide containers for Alpine Linux, Ubuntu and Debian

## Examples

- [1. Using Docker Compose](#1-using-docker-compose)
- [2. Avoid loosing v package using Docker Compose](#2-avoid-loosing-v-package-using-docker-compose)
- [3. Refresh latest image](#3-refresh-latest-image)

### 1. Using Docker Compose

In this example, we will create a container that contains the v executable on Alpine Linux OS.

```yml
version: "3"
services:
  v:
    image: khalyomede/vlang:latest-alpine
    entrypoint: v
    volumes:
      - .:/home/alpine
    working_dir: /home/alpine
```

Then, startup your containers:

```bash
$ docker-compose up --dettach
```

Finally, check v is executable:

```bash
$ docker-compose run v --version
V 0.2.2 f4486d7
```

### 2. Avoid loosing v package using Docker Compose

V manages modules on a OS wide folder (like Python), under `/root/.vmodules` (on Linux based OS). In this example, we will mount this folder in our project to keep installed modules.

```yml
version: "3"
services:
  v:
    image: khalyomede/vlang:latest-alpine
    entrypoint: v
    volumes:
      - .:/home/alpine
      - ./docker-data/v/root/.vmodules:/root/.vmodules
    working_dir: /home/alpine
```

This will create a new `docker-data/v/root/.vmodules` folder in your working folder.

### 3. Refresh latest image

Let us say you used the `khalyomede/vlang:latest-alpine` image 3 months ago. You want to use it again for another project. The issue is that your V version will be the one 3 months ago.

To fix this, simply call this command:

```bash
docker compose pull v
```

Where "v" is the name of the service that points to the image.

When running `docker compose run v --version`, it should display the latest version the image you used for the service was built for.

If your `docker-compose.yml` file use a custom version of the base image provided by this package, because you need a custom executable for example

```yml
services:
  v:
    build: ./docker/customized-v
    entrypoint: v
    working_dir: /home/alpine
    volumes:
      - .:/home/alpine
```

And your `docker/customized-v/Dockerfile` looks like this:

```dockerfile
FROM khalyomede/vlang:latest-alpine

RUN apk add --no-cache zbar
```

and you wish this custom image to use the latest changes from the base `khalyomede/vlang:` image, run this command:

```bash
docker compose build --no-cache v
```

## Avaialble containers

Browse available tags at [https://hub.docker.com/r/khalyomede/vlang/tags](https://hub.docker.com/r/khalyomede/vlang/tags).

## Test

Compile the images

```bash
docker build --no-cache src/latest-alpine -t khalyomede/vlang:latest-alpine
docker build --no-cache src/latest-ubuntu -t khalyomede/vlang:latest-ubuntu
docker build --no-cache src/latest-debian -t khalyomede/vlang:latest-debian
```

Create a docker-compose.yml file

```yml
version: "3"
services:
  v:
    build: ./src/latest-alpine
    entrypoint: v
    volumes:
      - .:/home/alpine
    working_dir: /home/alpine
```

Create an index.v file

```v
fn main() {
  println("hello world")
}
```

Startup your container and test

```bash
$ docker-compose run v run index.v
hello world
```
