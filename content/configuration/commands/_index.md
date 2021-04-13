---
title: Commands
chapter: true
---

# Commands

This section documents `firebuild` commands with their respective options.

## Common flags

All commands support the following common flags:

- `--log-level`: log level, default `debug`
- `--log-as-json`: default `false`
- `--log-color`: default `false`
- `--log-force-color`: default `false`
- `--tracing-enable`: default `false`, when set, enables remote tracer
- `--tracing-collector-host-port`: address of the collector, required when tracing is enabled, default `127.0.0.1:6831`
- `--tracing-log-enable`: if `true`, logs tracing events, default `false`