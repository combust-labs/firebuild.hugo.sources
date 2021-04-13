---
title: ls
chapter: true
---

# ls

List virtual machines for which a run cache location exists. The state is reported as boolean `running` flag.

A machine can enter `running: false` state under only two conditions: 

- when a `CtrlAltDel` Firecracker HTTP API request was sent to the VM socket outside of the `firebuild` tool
- when the VM exited from inside of the guest, for example by issuing `reboot` command

## Flags

- `--profile`: name of the profile to use, no default

## Example

```sh
sudo firebuild ls --profile=standard
```