---
title: CNI plugins
weight: 5
chapter: true
---

## Install and configure CNI plugins

`firebuild` uses CNI plugins for VM networking. Installing the plugins is very straightforward. Create `/opt/cni/bin/` directory and download the plugins:

```sh
mkdir -p /opt/cni/bin
curl -O -L https://github.com/containernetworking/plugins/releases/download/v0.9.1/cni-plugins-linux-amd64-v0.9.1.tgz
tar -C /opt/cni/bin -xzf cni-plugins-linux-amd64-v0.9.1.tgz
```

Firecracker requires the `tc-redirect-tap` CNI plugin. This plugin requires separate installation and does not offer binary distributions, it needs to be built from sources.

```sh
mkdir -p $GOPATH/src/github.com/awslabs/tc-redirect-tap
cd $GOPATH/src/github.com/awslabs/tc-redirect-tap
git clone https://github.com/awslabs/tc-redirect-tap.git .
make install
```