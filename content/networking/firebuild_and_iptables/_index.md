---
title: Firebuild and iptables
weight: 4
chapter: true
---

# Firebuild and iptables

Firebuild integrates with `iptables` to support selected operational features, outside of CNI. This page documents the `iptables` usage and gives more details on every case.

## Guest ports exposed on the host

When running a VM with the `--port` flags, firebuild uses `iptables` to setup the routing from the host interface to the guest port indicated by the flag. The following happens:

- a `FIREBUILD-FORWARD` chain is created in the `filter` table and a jump from `FORWARD` chain to the new chain is created
- an `FBD-<vmid>` chain is created in the `nat` table and a jump from `PREROUTING` chain to the new chain is created
- for every port, a rule is added to the `filter` `FIREBUILD-FORWARD` chain; the rule has the following format:

```sh
iptables -t filter -A FIREBUILD-FORWARD \
    -m comment --comment "[*|interface-name]:int-host-port:int-dest-port:[tcp|udp]" \
    -p tcp|udp [-i interface-name] -d ip-address-of-the-vm --dport int-host-port \
    -m state --state NEW,ESTABLISHED,RELATED -j ACCEPT
```

- for every port, a rule is added to the `nat` `FBD-<vmid>` chain; the rule has the following format:

```sh
iptables -t nat -A FBD-<vmid> \
    -m comment --comment "[*|interface-name]:int-host-port:int-dest-port:[tcp|udp]" \
    -p tcp|udp [-i interface-name] --dport int-host-port -j DNAT \
    --to-destination ip-address-of-the-vm:int-dest-port
```

When a VM is terminated:

- the VM specific `nat` table chain is removed
- the `nat` `PREROUTING` jump is removed
- the `filter` `FIREBUILD-FORWARD` VM specific rules are removed
- the `filter` `FIREBUILD-FORWARD` chain remains with the `FORWARD` jump

### Manual configuration example

This is what the above would look like if it was to be set manually up without explicit interface:

```sh
sudo iptables -t filter -N FIREBUILD-FORWARD
sudo iptables -t filter -A FORWARD -j FIREBUILD-FORWARD
sudo iptables -t filter -A FIREBUILD-FORWARD \
    -m comment --comment "*:16686:16686:tcp" \
    -p tcp -d 192.168.127.110 --dport 16686 \
    -m state --state NEW,ESTABLISHED,RELATED -j ACCEPT
sudo iptables -t nat -N FDB-jaegervm
sudo iptables -t nat -A PREROUTING -j FDB-jaegervm
sudo iptables -t nat -A FDB-jaegervm \
    -m comment --comment "*:16686:16686:tcp" \
    -p tcp --dport 16686 -j DNAT \
    --to-destination 192.168.127.110:16686
```

and with an interface, for example `eno1`:

```sh
sudo iptables -t filter -N FIREBUILD-FORWARD
sudo iptables -t filter -A FORWARD -j FIREBUILD-FORWARD
sudo iptables -t filter -A FIREBUILD-FORWARD \
    -m comment --comment "eno1:16686:16686:tcp" \
    -p tcp -i eno1 -d 192.168.127.110 --dport 16686 \
    -m state --state NEW,ESTABLISHED,RELATED -j ACCEPT
sudo iptables -t nat -N FDB-jaegervm
sudo iptables -t nat -A PREROUTING -j FDB-jaegervm
sudo iptables -t nat -A FDB-jaegervm \
    -m comment --comment "eno1:16686:16686:tcp" \
    -p tcp -i eno1 --dport 16686 -j DNAT \
    --to-destination 192.168.127.110:16686
```
