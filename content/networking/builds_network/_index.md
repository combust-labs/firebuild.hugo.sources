---
title: Build network
weight: 1
chapter: true
---

# Build network

The `rootfs` command starts a Firecracker virtual machine which is turn connects to a bootstrap specific gRPC service. As such, the virtual machine requires a network interface. The machine is launched inside of the network specified with `--cni-network-name` flag. It is recommended to create a dedicated network for the builds because the build time virtual machines do not require communicating with other machines on other networks.

Most often, access to the outside internet will be required as Dockerfiles and Docker containers, which the machines are built from, in almost all cases pull data from the internet.

The default CNI bridge implementation with the host-local IPAM provides egress to the guest.

An example of the build CNI network:

```sh
cat <<EOF > /etc/cni/conf.d/machine-builds.conflist
{
    "name": "machine-builds",
    "cniVersion": "0.4.0",
    "plugins": [
        {
            "type": "bridge",
            "name": "builds-bridge",
            "bridge": "builds0",
            "isDefaultGateway": true,
            "ipMasq": true,
            "hairpinMode": true,
            "ipam": {
                "type": "host-local",
                "subnet": "192.168.128.0/24",
                "resolvConf": "/etc/resolv.conf"
            }
        },
        {
            "type": "firewall"
        },
        {
            "type": "tc-redirect-tap"
        }
    ]
}
EOF
```

In a multi-tenant environment, it would be considerable to have a tenant specific build network.