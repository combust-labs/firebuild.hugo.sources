---
title: firebuild
weight: 6
chapter: true
---

## Build and install firebuild

Currently, there are no binary releases available. To build and install from sources, execute the following commands:

```sh
mkdir -p $GOPATH/src/github.com/combust-labs/firebuild
cd $GOPATH/src/github.com/combust-labs/firebuild
git clone https://github.com/combust-labs/firebuild.git .
go install
```

The resulting binary will be installed under `$GOPATH/bin/firebuild`.

The binary can be now moved to the host where it is intended to be running and placed at the target host `$PATH`. The recommended directory is `/usr/bin`.