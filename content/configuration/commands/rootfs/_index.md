---
title: rootfs
chapter: true
---

# rootfs

Create an application root file system.

## Flags

### Dockerfile build

- `--build-arg`: key / value build argument, multiple OK
- `--dockerfile`: local or remote (HTTP / HTTP) path; if the Dockerfile uses ADD or COPY commands, it's recommended to use a local file
- `--dockerfile-stage`: default empty string, the Dockerfile stage name to build from

### Docker image build

- `--docker-image`: no default, Docker image tag name to build from; mutually exclusive with `--dockerfile`
- `--docker-image-base`: no default, rootfs base when building from Docker image, required because the base operating system can't be established from a Docker image; for example `alpine:3.13`

### Shared settings

- `--bootstrap-certs-key-size` default `2048`, embedded CA bootstrap certificates key size, recommended values: 2048 or 4096
- `--bootstrap-certs-validity`, default 5 minutes, the period for which the embedded bootstrap certificates are valid for
- `--bootstrap-initial-communication-timeout`, default 30 seconds, how long to wait for vminit to initiate bootstrap with commands request before considering bootstrap failed
- `--bootstrap-server-bind-interface`, no default, the interface to bind the bootstrap server on; if empty, a list of up broadcast up will be resolved and the first interface will be used
- `--cni-bin-dir`: CNI plugins bin directory, default `/opt/cni/bin`
- `--cni-conf-dir`: CNI configuration directory, default `/etc/cni/conf.d`
- `--cni-cache-dir`: CNI cache directory: default `/var/lib/cni`
- `--post-build-command`: OS specific commands to run after Dockerfile commands but before the file system is persisted, multiple OK
- `--pre-build-command`: OS specific commands to run before any Dockerfile command, multiple OK
- `--tag`: no default, required tag name of the build, required; must be `org/name:version`

#### Firecracker / Jailer settings

- `--binary-firecracker`: no default, path to the Firecracker binary, required, can be specified with a profile
- `--binary-jailer`: no default, path to the Firecracker Jailer binary to use, required, can be specified with a profile
- `--chroot-base`: default `/srv/jailer`, chroot base directory; can't be empty or `/`, required, can be specified with a profile
- `--jailer-gid`: default `0`, Jailer GID value
- `--jailer-numa-node`: default `0`, Jailer NUMA node
- `--jailer-uid`: default `0`, Jailer UID value
- `--netns` default `/var/lib/netns`, network namespace directory

#### Run cache

- `--run-cache`: default `/var/lib/firebuild`, firebuild run cache base directory

#### Build machine configuration

- `--cni-network-name`: no default, CNI network within which the build should run; it's recommended to use a dedicated network for build process, required
- `--cpu`, default `1`, number of CPUs for the build VMM, required
- `--cpu-template`: no default, CPU template (empty, C2 or T3)
- `--ht-enabled`: default `false`, when specified, enable hyper-threading
- `--kernel-args`: default `console=ttyS0 noapic reboot=k panic=1 pci=off nomodules rw`, kernel arguments, required
- `--mem`: default `128`, amount of memory for the VMM in megabytes, required
- `--no-mmds`: default `false`, if set, disables MMDS, the build process requires MMDS
- `--root-drive-partuuid`: no default, root drive part UUID
- `--ssh-user`: no default, SSH user, no effect during build
- `--vmlinux-id`: no default, kernel ID / name

#### Misc settings

- `--log-firecracker-http-calls`: default `false`, if set, logs Firecracker HTTP client calls in debug mode
- `--shutdown-graceful-timeout-seconds`: default 30 seconds, graceful shutdown timeout before vmm is stopped forcefully

## Example

```sh
sudo firebuild rootfs \
    --profile=standard \
    --dockerfile=git+https://github.com/hashicorp/docker-consul.git:/0.X/Dockerfile \
    --cni-network-name=machine-builds \
    --vmlinux-id=vmlinux-v5.8 \
    --tag=combust-labs/consul:1.9.4
```