---
title: Roadmap
weight: 6
chapter: true
---

# Roadmap

1. Support STOPSIGNAL. Pass the signal via vminit to the guest and handle when service stop is called.
2. Integrate service discovery for run and kill commands. Run should register the machine in the service catalog, kill should remove it. Start with Consul Service Catalog integration but implement as a go-plugin back end so the connectors can be maintained separate from the core.
  - Investigate how to use the Consul DNS in a guest such that the VMs can resolve themselves via DNS names. This will serve the first clustering method.
3. Create the kernel build command based on the single kernel config already documented on the blog. This will serve as a documentation of the kernel build process.
4. Combine `baseos` and `rootfs` commands into one command but keep the file system level separation. Rename the rootfs command to something more suitable, like … `build? Eliminate the `baseos` vs `rootfs` confusion.
5. Provide a wider range of base operating systems: Ubuntu 18.04, 20.04, Red Hat 8 UBI should come first. This will most likely require investigating `systemd` as `init`.
6. Redo the `rootfs` and `kernel` storage provider as a gRPC go-plugin so additional storage backends can be provided at runtime.
7. Integrate volumes:
  - Add a command to create a named volume of a given size and specific file system type. Provide a profile setting for where the volumes are stored and allow volume namespacing, similar to how `rootfs` images are stored. Store volume metadata next to the volume. Exact metadata to be figured out when implementing.
  - Run command: add a `--volume=volume-name:/path/in/guest` flag, multiple ok, pass volumes info via `vminit` which will create the required directory in guest, if it does not exist and mount it. VM kill will not remove those volumes so they become de-facto persistent storage for VMs.
8. Further extend `vminit` bootstrap: this will reduce the `baseos` image size because the setup of `ssh` and the default user account can be removed, this will also improve startup time.
  - Pass host keys at `vminit` time
  - Allow user creation via `vminit` and inject ssh keys at this stage, if not given, auto-generate when machine is starting, follow this method: https://serverfault.com/a/471346/614568; generate directly via golang so no dependency is required
  - Allow setting `locale` via `vminit`
  - Configure NTP
  - Install additional packages on boot
  - Run commands: this will serve as `--userdata`
  - Set `timezone`, default to `UTC`
9. Internal event emitter with configurable gRPC plugin based sinks. Enable usage of these via profiles.
10. Create tools to manage networking, in the first iteration, CNI bridge networks.
  - Figure out how to manage overlay networks so a multi-node Firecracker cluster can be enabled.
11. Extract the actual command executors into separate packages, outside of the command implementations.
12. CONSIDER: Create a daemon to execute all firebuild operations via gRPC and HTTP API.
  - The API will enable operating via tools like Packer, Terraform and Nomad
13. Work towards more permissive license

## Implemented

1. **Implemented in [combust-labs/firebuild/pull/55](https://github.com/combust-labs/firebuild/pull/55)**: Run command: add a `--port=host:vmport` flag, multiple ok, allow for `iface:host:vmport` format, if two elements, auto-discover first suitable interface. For each one of those, configure iptables such that ports become available via the host interface.