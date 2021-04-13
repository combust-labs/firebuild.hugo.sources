---
title: profile-create
chapter: true
---

# profile-create

Create a profile.

## Flags

- `--profile`: string, name of the profile to create
- `--binary-firecracker`: string, full path to the Firecracker binary
- `--binary-jailer`: string, full path to the Jailer binary
- `--chroot-base`: string, full path to the chroot base directory
- `--run-cache`: string, full path to the run cache directory
- `--storage-provider`: string, selected storage provider
- `--storage-provider-property-string`: key / string value storage provider configuration property in the format `property=value`, multiple OK
- `--storage-provider-property-int64`: key / int64 value storage provider configuration property in the format `property=value`, multiple OK

A profile can be updated by issuing a subsequent `profile-create` command.

## Example

```sh
sudo firebuild profile-create \
	--profile=standard \
	--binary-firecracker=$(readlink /usr/bin/firecracker) \
	--binary-jailer=$(readlink /usr/bin/jailer) \
	--chroot-base=/fc/jail \
	--run-cache=/fc/cache \
	--storage-provider=directory \
	--storage-provider-property-string="rootfs-storage-root=/fc/rootfs" \
	--storage-provider-property-string="kernel-storage-root=/fc/vmlinux"
```

The directories referenced by `--chroot-base`, `--run-cache`, `--storage-provider-property-string="rootfs-storage-root=..."` and `--storage-provider-property-string="kernel-storage-root=..."` must exist before the profile can be created.

All flags except `--profile` are optional.

Commands use the same flag names and a flag provided on the command line takes precedence over the profile value.

Profiles are stored in the `/etc/firebuild/profiles` directory.