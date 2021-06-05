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

## Avaialble containers

| OS              | Tag                            |
|-----------------|--------------------------------|
| Alpine 3.13.5   | khalyomede/vlang:latest-alpine |
| Ubuntu 20.04    | khalyomede/vlang:latest-ubuntu |
| Debian 9 (Slim) | khalyomede/vlang:latest-debian |

## Test

Compile the images

```bash
docker build --no-cache latest-alpine -t khalyomede/vlang:latest-alpine
docker build --no-cache latest-ubuntu -t khalyomede/vlang:latest-ubuntu
docker build --no-cache latest-debian -t khalyomede/vlang:latest-debian
```

Create a docker-compose.yml file

```yml
version: "3"
services:
  v:
    build: ./latest-alpine
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
