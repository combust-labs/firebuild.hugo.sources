---
title: run
chapter: true
---

# run

Run a virtual machine.

## Flags

- `--daemonize`: default `false`, when set, runs the VMM in the detached mode
- `--env-file`: default none, full path to an environment file to apply to the VMM during bootstrap, multiple OK
- `--env`: string key / string value in format `key=value`, additional environment variables to apply to the VMM during bootstrap, multiple OK
- `--from`: no default, the image to launch from, for example: `combust-labs/postgres:13`
- `--identity-file`: full path to the SSH public key to deploy to the machine during bootstrap, must be regular file, multiple OK
- `--hostname`: hostname to apply to the VMM during bootstrap; if empty, a random name will be assigned
- `--name`: no default, name of the VM, maximum 20 characters; allowed characters: letters and digits; if not specified, a random name will be generated

### Firecracker / Jailer settings

- `--binary-firecracker`: no default, path to the Firecracker binary, required, can be specified with a profile
- `--binary-jailer`: no default, path to the Firecracker Jailer binary to use, required, can be specified with a profile
- `--chroot-base`: default `/srv/jailer`, chroot base directory; can't be empty or `/`, required, can be specified with a profile
- `--jailer-gid`: default `0`, Jailer GID value
- `--jailer-numa-node`: default `0`, Jailer NUMA node
- `--jailer-uid`: default `0`, Jailer UID value
- `--netns` default `/var/lib/netns`, network namespace directory

### Run cache

- `--run-cache`: default `/var/lib/firebuild`, firebuild run cache base directory

### Build machine configuration

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

### Misc settings

- `--log-firecracker-http-calls`: default `false`, if set, logs Firecracker HTTP client calls in debug mode
- `--shutdown-graceful-timeout-seconds`: default 30 seconds, graceful shutdown timeout before vmm is stopped forcefully

## Remaining arguments

This command allows overriding the default command extracted from the Dockerfile or the Docker image. The command can be supplied by adding:

```sh
firebuild run ... -- command override
```

At the end of the `firebuild` command.

## Examples

Start a machine without SSH access:

```sh
sudo firebuild run \
    --profile=standard \
    --from=combust-labs/consul:1.9.4 \
    --cni-network-name=machines \
    --vmlinux-id=vmlinux-v5.8
```

Start a machine with SSH access:

```sh
sudo firebuild run \
    --profile=standard \
    --from=combust-labs/consul:1.9.4 \
    --cni-network-name=machines \
    --identity-file=/home/user/.ssh/id.pub \
    --ssh-user=alpine \
    --vmlinux-id=vmlinux-v5.8
```

Start a machine with command override:

```sh
sudo firebuild run \
    --profile=standard \
    --from=combust-labs/consul:1.9.4 \
    --cni-network-name=machines \
    --vmlinux-id=vmlinux-v5.8 -- agent -server -client 0.0.0.0 -bootstrap-expect 3 -data-dir /consul/data
```