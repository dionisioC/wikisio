---
title: Podman
date: 20231229
author: Dionisio
---

# Podman

Since Docker changed its usage policy, lots of developers started to use other
solutions. One of them is [Podman](https://podman.io).

Some Arch distributions, [EndeavourOS,](https://endeavouros.com) include
[Podman](https://podman.io).
[Red Hat](https://www.redhat.com) backs [Podman](https://podman.io) up,
making it one of the most popular alternatives to Docker.

## Images

By default, podman goes to the quay.io registry. So if we do:

```shell
docker run docker.io/hello-world
```

We see we are using podman, and we are going to the quay.io registry:

```text
Emulate Docker CLI using podman. Create /etc/containers/nodocker to quiet msg.
Resolved "hello-world" as an alias (/etc/containers/registries.conf.d/
00-shortnames.conf)
Trying to pull quay.io/podman/hello:latest…
Getting image source signatures
Copying blob d08b40be6878 done   |
Copying config e2b3db5d4f done   |
Writing manifest to image destination
!… Hello Podman World …!

         .--"--.
       / -     - \
      / (O)   (O) \
   ~~~| -=(,Y,)=- |
    .---. /`  \   |~~
 ~/  o  o \~~~~.----. ~~
  | =(X)= |~  / (O (O) \
   ~~~~~~~  ~| =(Y_)=-  |
  ~~~~    ~~~|   U      |~~

Project:   https://github.com/containers/podman
Website:   https://podman.io
Documents: https://docs.podman.io
Twitter:   @Podman_io
```

But if we want to use an image that is not available in quay.io, we are going
to get the following error:

```shell
docker run --rm -it -p 4566:4566 -p 4510-4559:4510-4559 localstack/localstack
```

```text
Emulate Docker CLI using podman. Create /etc/containers/nodocker to quiet msg.
Error: short-name "localstack/localstack" did not resolve to an alias and no
unqualified-search registries are defined in "/etc/containers/registries.conf"
```

One possible fix is to write the qualified name:

```shell
docker run docker.io/hello-world
```

So we get the Docker Hub image:

```text
Emulate Docker CLI using podman. Create /etc/containers/nodocker to quiet msg.
Trying to pull docker.io/library/hello-world:latest…
Getting image source signatures
Copying blob c1ec31eb5944 done   |
Copying config d2c94e258d done   |
Writing manifest to image destination

Hello from Docker
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

Another solution is to add `text unqualified-search-registries = ["docker.io"]`
to the `text /etc/containers/registries.conf` file.

For security reasons, it seems to be better to always qualify the images.

Now we can do:

```shell
docker run --rm -it -p 4566:4566 -p 4510-4559:4510-4559 localstack/localstack
```

And get:

```text
Emulate Docker CLI using podman. Create /etc/containers/nodocker to quiet msg.
Resolving "localstack/localstack" using unqualified-search registries
(/etc/containers/registries.conf) Trying to pull
docker.io/localstack/localstack:latest…
```

Full information can be found by typing:

```shell
man 5 containers-registries.conf
```
