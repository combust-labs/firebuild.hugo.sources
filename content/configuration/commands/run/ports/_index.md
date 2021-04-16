---
title: --ports
chapter: true
---

# Exposing guest ports on the host

The `--ports` argument publishes a guest port on the host interface or all interfaces.

The following formats are supported:

- `interface:host-port:dest-port:/protocol`
- `interface:host-port:dest-port`
- `host-port:dest-port:/protocol`
- `interface:dest-port`
- `host-port:dest-port`
- `dest-port:/protocol`
- `dest-port`

There are following rules to the parsing:

- `interface` cannot be longer than 15 characters
- if there is no `host-port`, the value is copied from the `dest-port`
- port numbers must be higher than `9`, the parser looks for minimum 2 digits
- if no protocol is specified, `/tcp` is assumed

The parser parses the port values as integers but it validates that they are in the range of `[1, 65535]`. The initial parsing will match any three letter sequence at the protocol location but this is further validated to contain `/tcp` or `/udp` only; this check is case sensitive.

The integration is done via `iptables`. [More details here](/firebuild-docs/networking/firebuild_and_iptables/#guest-ports-exposed-on-the-host).

## Examples

1. expose guest `tcp` port `16686` on host port `26686` on interface `eno1`: `--port=eno1:26686:16686:/tcp`
2. expose guest `udp` port `6831` on host port `6831` on all interfaces: `--port=6831:/tcp`
3. expose guest `udp` port `6831` on host port `16831` on all interfaces: `--port=16831:6831:/tcp`

## Metadata location

The ports are stored in the VM metadata at `.Configs.RunConfig.Ports` path.

## Cleanup

The ports are cleaned up when executing `kill` and `purge` commands for daemonized VMs and immediately on stop for non-daemonized VMs.