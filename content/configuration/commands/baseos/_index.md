---
title: baseos
chapter: true
---

# baseos

Builds a base operating system file system from a Dockerfile.

## Flags

- `--dockerfile`: full path to the base operating system Dockerfile
- `--filesystem-size-mbs`: base file system root file system size, default 500M
- `--profile`: name of the profile to use, no default
- `--storage-provider`: select storage provider, no default, required; respective storage provider configuration must be specified, check [Storage providers](/configuration/storage_providers) for details
- `--tag`: tag name under which the resulting file system will be stored

## Default tag name

If tag name is not given to the command, the name will be inherited from the Dockerfile `FROM` command.

## Example

```sh
sudo firebuild baseos \
    --profile=standard \
    --dockerfile $(pwd)/baseos/_/alpine/3.13/Dockerfile
```