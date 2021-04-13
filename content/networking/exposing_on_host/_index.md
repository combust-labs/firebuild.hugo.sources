---
title: Exposing VMs on host interfaces
weight: 4
chapter: true
---

# Exposing VMs on host interfaces

By default, the CNI network makes egress available to the guest. However, it's not possible to reach a service running in the guest from outside of the host. In order to allow access to the service from outside of the host, correct routes have to be configured using iptables or similar.

For example, to allow access to a Jaeger Query UI over the `eno1` interface to a VM running on a CNI bridge with IP `192.168.127.100` over the standard port `16686`, configure iptables like this:

```sh
sudo iptables -t filter -N FIREBUILD-FORWARD
sudo iptables -t filter -A FORWARD -m comment --comment "firebuild:forward" -j FIREBUILD-FORWARD
sudo iptables -t filter -A FIREBUILD-FORWARD \
    -m comment --comment "firebuild:forward:jaeger:1.22:tcp:16686" \
    -p tcp -d 192.168.127.110 --dport 16686 \
    -m state --state NEW,ESTABLISHED,RELATED -j ACCEPT

sudo iptables -t nat -N FIREBUILD-JAEGER122
sudo iptables -t nat -A PREROUTING -j FIREBUILD-JAEGER122
sudo iptables -t nat -A FIREBUILD-JAEGER122 \
    -m comment --comment "firebuild:nat:jaeger:1.22:tcp:16686" \
    -p tcp --dport 16686 \
    -j DNAT \
    --to-destination 192.168.127.110:16686
```

To limit to a specific interface, for example `eno1`:

```sh
sudo iptables -t filter -N FIREBUILD-FORWARD
sudo iptables -t filter -A FORWARD -m comment --comment "firebuild:forward" -j FIREBUILD-FORWARD
sudo iptables -t filter -A FIREBUILD-FORWARD \
    -m comment --comment "firebuild:forward:jaeger:1.22:tcp:16686" \
    -p tcp -d 192.168.127.110 --dport 16686 \
    -m state --state NEW,ESTABLISHED,RELATED -j ACCEPT

sudo iptables -t nat -N FIREBUILD-JAEGER122
sudo iptables -t nat -A PREROUTING -j FIREBUILD-JAEGER122
sudo iptables -t nat -A FIREBUILD-JAEGER122 \
    -m comment --comment "firebuild:nat:jaeger:1.22:tcp:16686" \
    -p tcp -i eno1 --dport 16686 \
    -j DNAT \
    --to-destination 192.168.127.110:16686
```