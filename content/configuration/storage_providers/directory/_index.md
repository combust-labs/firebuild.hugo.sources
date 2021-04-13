---
title: directory
chapter: true
---

# Directory

This storage provider stores the root file systems and kernel images on the host accessible storage.

## Selecting the provider

- `--storage-provider=directory`

## Configuration options

- `rootfs-storage-root`: root directory of the rootfs storage
- `kernel-storage-root`: root directory of the kernel storage

## Configuring via command line flags

- `--storage-provider.directory.rootfs-storage-root`: string, path to the rootfs storage root
- `--storage-provider.directory.kernel-storage-root`: string, path to the kernel storage root

## Configuring via profiles

- `--storage-provider-property-string="rootfs-storage-root=..."`
- `--storage-provider-property-string="kernel-storage-root=..."`

{{% notice info %}}
<strong>Limitations</strong>:
{{% /notice %}}

Firecracker SDK used by `firebuild` links the `rootfs` file in the jail directory using the `os.Symlink` function. It is not possible to link to another volume, a mount has to be created instead. As such, this storage provider requires the chroot base and run cache reside on the same volume.
