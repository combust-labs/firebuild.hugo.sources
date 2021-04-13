---
title: Docker
weight: 4
chapter: true
---

## Install Docker

`firebuild` uses Docker to build application file systems and requires Docker installed and configured on the host build host. To install and configure Docker, [follow the instructions from the Docker website](https://docs.docker.com/engine/install/).

## Configure non-root user management

`firebuild` itself requires `sudo` to run and it communicates with Docker using the Docker API so it does not need the non-root Docker management configured. However, for own convenience, you can choose to configure Docker management for a non-root user. Instruction [can be found here](https://docs.docker.com/engine/install/linux-postinstall/).

{{% notice info %}}
<strong>The following Docker features are used by `firebuild`</strong>:
{{% /notice %}}

- `docker images`
- `docker build`, `tag` and `rmi`
- `docker run`, `stop` and `rm`
- `docker export`
