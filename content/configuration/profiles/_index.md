---
title: Profiles
chapter: true
---

# Profiles

A `firebuild` profile is a JSON file with common values used to execute various commands.

Technically, `firebuild` does not require a profile. Common arguments may be provided on every execution. The profile exists for two reasons:

- it makes subsequent operations more concise by moving the tedious arguments away
- provides extra isolation with different chroots, cache directories, and image / kernel catalogs

Profiles are stored in the `/etc/firebuild/profiles` directory.

## Profile settings

A profile can define following settings:

- Firecracker binary path: path to the firecracker binary
- Jailer binary path: path to the jailer binary
- Chroot base path: path to the chroot base directory
- Run cache path: path to the run cache directory
- Storage provider type: string identifying the storage provider to use
- Storage provider arguments: storage provider specific configuration
- Tracing configuration
