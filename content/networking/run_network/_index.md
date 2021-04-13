---
title: Run network
weight: 2
chapter: true
---

# Run network

The `run` allows placing a virtual machine in a network specified with `--cni-network-name` flag. The rule of thumb is to create a dedicated network per tenant in a multi-tenant environment.

The default CNI bridge implementation with the host-local IPAM provides egress to the guest.

An example of the run CNI network:

```sh
cat <<EOF > /etc/cni/conf.d/machines.conflist
{
    "name": "machines",
    "cniVersion": "0.4.0",
    "plugins": [
        {
            "type": "bridge",
            "name": "machines-bridge",
            "bridge": "machines0",
            "isDefaultGateway": true,
            "ipMasq": true,
            "hairpinMode": true,
            "ipam": {
                "type": "host-local",
                "subnet": "192.168.127.0/24",
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
