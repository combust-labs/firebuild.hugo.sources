---
title: Static IP assignment
weight: 3
chapter: true
---

# Static IP assignment

An example of the CNI network with static IP assignment:

```sh
cat <<EOF > /etc/cni/conf.d/staticnet.conflist
{
    "name": "staticnet",
    "cniVersion": "0.4.0",
    "plugins": [
        {
            "type": "bridge",
            "name": "staticnet-bridge",
            "bridge": "staticnet0",
            "isDefaultGateway": true,
            "ipMasq": true,
            "hairpinMode": true,
            "ipam": {
                "type": "static",
                "addresses": []
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

To launch a virtual machine with a static IP address, use the following `run` command flags:

- `--ip-address=value` where the value is the IP address in the CIDR notation, for example `192.168.126.1/24`
- `--gateway=value` optional gateway IP address in the address notation, for example `192.168.126.254`

With the static IP assignment, it is possible to set up a management type infrastructure for other dynamic resources. For example, this IP assignment strategy allows bootstrapping a multi-VM Consul or ZooKeeper cluster with the need to run a DNS server but requires manual IP address tracking.