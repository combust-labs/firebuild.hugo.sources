# firebuild

## Convenience of containers, security of virtual machines

With `firebuild`, you can build and deploy secure VMs directly from `Dockerfiles` and Docker images in just few minutes.

The concept of `firebuild` is to leverage as much of the existing Docker world as possible. There are thousands of Docker images out there. Docker images are awesome because they encapsulate the software we want to run in our workloads, they also encapsulate dependencies. Dockerfiles are what Docker images are built from. Dockeriles are the blueprints of the modern infrastructure. There are thousands of them for almost anything one can imagine and new ones are very easy to write.

With firebuild it is possible to:

- build root file systems directly from Dockerfiles
- tag and version root file systems
- run and manage microvms on a single host
- define run profiles

## High level example

Build and start HashiCorp Consul 1.9.4 on Firecracker with three simple steps:

- build a base operating system image
- build Consul image
- start the application

```sh
sudo firebuild baseos \
    --profile=standard \
    --dockerfile $(pwd)/baseos/_/alpine/3.12/Dockerfile
```

```sh
sudo firebuild rootfs \
    --profile=standard \
    --dockerfile=git+https://github.com/hashicorp/docker-consul.git:/0.X/Dockerfile \
    --cni-network-name=machine-builds \
    --vmlinux-id=vmlinux-v5.8 \
    --tag=combust-labs/consul:1.9.4
```

```sh
sudo firebuild run \
    --profile=standard \
    --name=consul1 \
    --from=combust-labs/consul:1.9.4 \
    --cni-network-name=machines \
    --vmlinux-id=vmlinux-v5.8
```

Find the IP of the `consul1` VM and query Consul:

```sh
VMIP=$(sudo firebuild inspect \
    --profile=standard \
    --vmm-id=consul1 | jq '.NetworkInterfaces[0].StaticConfiguration.IPConfiguration.IP' -r)
```

```sh
$ curl http://${VMIP}:8500/v1/status/leader
"127.0.0.1:8300"
```