---
title: inspect
chapter: true
---

# inspect

Inspect a running virtual machine.

## Flags

- `--profile`: name of the profile to use, no default
- `--vmm-id`: ID of the virtual machine to inspect

## Example

```sh
sudo firebuild inspect \
    --profile=standard \
    --vmm-id=wcabty1922gloailwrce
```

### Output

The resulting data is a JSON formatted string containing the VM runtime metadata:

```json
{
  "latest": {
    "meta-data": {
      "Drives": {
        "1": {
          "DriveID": "1",
          "IsReadOnly": "false",
          "IsRootDevice": "true",
          "Partuuid": "",
          "PathOnHost": "rootfs"
        }
      },
      "EntrypointJSON": "{\"Cmd\":[\"agent\",\"-dev\",\"-client\",\"0.0.0.0\"],\"EntryPoint\":[\"docker-entrypoint.sh\"],\"Env\":{\"HASHICORP_RELEASES\":\"https://releases.hashicorp.com\"},\"Shell\":[\"/bin/sh\",\"-c\"],\"User\":\"0:0\",\"Workdir\":\"/\"}",
      "Env": {},
      "ImageTag": "combust-labs/consul:1.9.4",
      "LocalHostname": "sharp-mirzakhani",
      "Machine": {
        "CPU": "1",
        "CPUTemplate": "",
        "HTEnabled": "false",
        "KernelArgs": "console=ttyS0 noapic reboot=k panic=1 pci=off nomodules rw",
        "Mem": "128",
        "VMLinux": "vmlinux-v5.8"
      },
      "Network": {
        "CniNetworkName": "machines",
        "Interfaces": {
          "b6:16:f2:3d:29:cf": {
            "Gateway": "192.168.127.1",
            "HostDeviceName": "tap0",
            "IfName": "",
            "IP": "192.168.127.89",
            "IPAddr": "192.168.127.89/24",
            "IPMask": "ffffff00",
            "IPNet": "ip+net",
            "NameServers": ""
          }
        }
      },
      "Users": {},
      "VMMID": "wcabty1922gloailwrce"
    }
  }
}
```