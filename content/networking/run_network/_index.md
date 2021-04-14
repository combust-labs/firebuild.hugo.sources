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

## Assigning an IP address

The `run` command offers the `--ip-address` flag which allows selecting an IP address to assign to the VM. Assuming that no other VM uses the requested IP address, the assignment will succeed.

### An example: running a 3 VM HashiCorp Consul cluster

Start 3 VMs using predefined IP addresses in the `machines` network:

```sh
# Start Consul 1 with IP 192.168.127.10:
sudo firebuild run \
    --profile=standard \
    --from=combust-labs/consul:1.9.4 \
    --cni-network-name=machines \
    --vmlinux-id=vmlinux-v5.8 \
    --ip-address=192.168.127.10 \
    --name=consul1 \
    --daemonize \
    -- agent -server -client 0.0.0.0 -bootstrap-expect 3 -data-dir /consul/data -retry-join 192.168.127.11 -retry-join 192.168.127.12 -node consul1

# Start Consul 2 with IP 192.168.127.11:
sudo firebuild run \
    --profile=standard \
    --from=combust-labs/consul:1.9.4 \
    --cni-network-name=machines \
    --identity-file=/home/radek/.ssh/github.pub \
    --ssh-user=alpine \
    --vmlinux-id=vmlinux-v5.8 \
    --ip-address=192.168.127.11 \
    --name=consul2 \
    --daemonize \
    -- agent -server -client 0.0.0.0 -bootstrap-expect 3 -data-dir /consul/data -retry-join 192.168.127.10 -retry-join 192.168.127.12 -node consul2

# Start Consul 3 with IP 192.168.127.10:
sudo firebuild run \
    --profile=standard \
    --from=combust-labs/consul:1.9.4 \
    --cni-network-name=machines \
    --vmlinux-id=vmlinux-v5.8 \
    --ip-address=192.168.127.12 \
    --name=consul3 \
    --daemonize \
    -- agent -server -client 0.0.0.0 -bootstrap-expect 3 -data-dir /consul/data -retry-join 192.168.127.10 -retry-join 192.168.127.11 -node consul3
```

After a few seconds required to boot the VMs, start the services and reconcile the cluster, query one of the nodes for status:

```sh
$ curl http://192.168.127.12:8500/v1/status/leader
```

The output will be similar to:

```sh
"192.168.127.10:8300"
```

Next, let's query the DNS. Consul nodes were launched with with the `-node` flags set. The cluster uses the default `8600` DNS port and default data center name of `dc1`. Hence, we can query for a specific node:

```sh
$ dig @192.168.127.10 -p 8600 consul1.node.dc1.consul
```

The response from the DNS should look similar to:

```
; <<>> DiG 9.11.3-1ubuntu1.14-Ubuntu <<>> @192.168.127.10 -p 8600 consul1.node.dc1.consul
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 13511
;; flags: qr aa rd; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 2
;; WARNING: recursion requested but not available

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;consul1.node.dc1.consul.	IN	A

;; ANSWER SECTION:
consul1.node.dc1.consul. 0	IN	A	192.168.127.10

;; ADDITIONAL SECTION:
consul1.node.dc1.consul. 0	IN	TXT	"consul-network-segment="

;; Query time: 1 msec
;; SERVER: 192.168.127.10#8600(192.168.127.10)
;; WHEN: Wed Apr 14 21:31:00 UTC 2021
;; MSG SIZE  rcvd: 104
```