---
title: purge
chapter: true
---

# purge

Purges remains of virtual machines in an unclear stopped state.

A machine can enter the unclean `running: false` state under only two conditions:

- when a `CtrlAltDel` Firecracker HTTP API request was sent to the VM socket outside of the `firebuild` tool
- when the VM exited from inside of the guest, for example by issuing `reboot` command

When this happens, the jail directory, the run cache and the CNI state remain on the host. This command cleans up this state.

## Flags

- `--profile`: name of the profile to use, no default

## Example

```sh
sudo firebuild purge --profile=standard
```
