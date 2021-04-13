---
title: Go programming language
weight: 2
chapter: true
---

## Install and configure Go

`firebuild` is written in Go and requires version 1.16+ to build. The application can be built on a system separate to where it will be deployed. To install Go on your Linux host, [follow the instruction on the Go website](https://golang.org/doc/install).

Finally, create the Go working directories:

```sh
mkdir -p $GOPATH/{src,bin}
```