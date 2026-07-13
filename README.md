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
- `v` user with is own folder for non-root scenarios

## Examples

- [1. Using Docker Compose](#1-using-docker-compose)
- [2. Avoid loosing v package using Docker Compose](#2-avoid-loosing-v-package-using-docker-compose)
- [3. Refresh latest image](#3-refresh-latest-image)
- [4. Use a non-root user](#4-use-a-non-root-user)

### 1. Using Docker Compose

In this example, we will create a container that contains the v executable on Alpine Linux OS.

```yml
version: "3"
services:
  v:
    image: khalyomede/vlang:latest-alpine
    entrypoint: v
    volumes:
      - .:/root
    working_dir: /root
```

Then, startup your containers:

```bash
$ docker-compose up --dettach
```

Finally, check v is executable:

```bash
$ docker-compose run v --version
V 0.4.12 0df4435
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
      - .:/root
      - ./docker-data/v/root/.vmodules:/root/.vmodules
    working_dir: /root
```

This will create a new `docker-data/v/root/.vmodules` folder in your working folder.

### 3. Refresh latest image

- [Using the base image](#using-the-base-image)
- [Using a custom image](#using-a-custom-image)

#### Using the base image

Let us say you used the `khalyomede/vlang:latest-alpine` image 3 months ago. You want to use it again for another project. The issue is that your V version will be the one 3 months ago.

To fix this, simply call this command:

```bash
docker compose pull v
```

Where "v" is the name of the service that points to the image.

When running `docker compose run v --version`, it should display the latest version the image you used for the service was built for.

#### Using a custom image

If your `docker-compose.yml` file use a custom version of the base image provided by this package, because you need a custom executable for example

```yml
services:
  v:
    build: ./docker/customized-v
    entrypoint: v
    working_dir: /root
    volumes:
      - .:/root
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

### 4. Use a non-root user

For use cases when you need a restricted workspace, use the `v` user with its dedicated `/home/v` folder.

```yml
services:
  v:
    image: khalyomede/vlang:latest-alpine
    entrypoint: v
    user: v
    working_dir: /home/v
    volumes:
      - .:/home/v
```

## Avaialble containers

Browse available tags at [https://hub.docker.com/r/khalyomede/vlang/tags](https://hub.docker.com/r/khalyomede/vlang/tags).

## Test

Compile the images

```bash
docker buildx build --platform=linux/amd64,linux/arm64 --no-cache src/latest-alpine -t khalyomede/vlang:latest-alpine
docker buildx build --platform=linux/amd64,linux/arm64 --no-cache src/latest-ubuntu -t khalyomede/vlang:latest-ubuntu
docker buildx build --platform=linux/amd64,linux/arm64 --no-cache src/latest-debian -t khalyomede/vlang:latest-debian
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
module main

fn main() {
  println("hello world")
}
```

Startup your container

```bash
docker compose build v
```

Check the version is the expected one

```bash
docker compose run --rm v version
```

```bash
V 0.4.9 e1b9054
```

Check a simple hello world program runs successfuly

```bash
docker compose run v run index.v
```

```bash
hello world
```
