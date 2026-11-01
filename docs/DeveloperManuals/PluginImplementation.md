---
title: "Plugin Implementation"
sidebar_position: 2
description: >
  Plugin Implementation
---

Plugins are code extensions that enable you to pull data from data-sources and present them in DevLake. 
They can be implemented in both Go and Python. The framework itself is written in Go, and Python (called PyDevLake) is a supplemental extension to 
support developers who prefer it using it. PyDevLake is relatively brand new, and we would like to see it gain more traction; we encourage you to give it 

The Go development manual can be found [here](https://github.com/apache/devlake/blob/main/backend/DevelopmentManual.md). This manual also covers the framework in detail.

The Python development manual can be found [here](https://github.com/apache/devlake/blob/main/backend/python/README.md).

## How a plugin becomes part of DevLake

DevLake ships as a single `lake` binary: the framework, the API server and the official plugins are linked at build time. A Go plugin joins that binary when its package lives under `backend/plugins/<name>` and its `impl` package exports a type implementing [`plugin.PluginMeta`](https://github.com/apache/devlake/blob/main/backend/core/plugin/plugin_meta.go).

Registration is generated rather than hand-written. `make plugins-registry` scans `plugins/*/impl`, writes `backend/plugins/all/all.go`, and the server blank-imports that package so each listed plugin registers itself on startup.

- `make build` compiles in every plugin and writes `bin/lake`.
- `DEVLAKE_PLUGINS=jira,gitlab make build` compiles in a subset. The variable also drives the generator, so run `make plugins-registry` without it before you commit.
- `DEVLAKE_PLUGINS=none make build` produces a binary with no plugin compiled in.

When a plugin is added, removed or renamed, regenerate the registry and commit the result; CI fails when `plugins/all/all.go` is out of date.

A plugin developed outside this repository is not compiled in. Build it as a Go shared object — `make build-plugin`, or `go build -buildmode=plugin` — and place it under `PLUGIN_DIR`. The server loads it at startup on top of the compiled-in plugins. See [Sideloading plugins](./DeveloperSetup.md#sideloading-plugins) for the toolchain and version requirements.

Python plugins do not go through this registry. They are discovered at runtime under `REMOTE_PLUGIN_DIR`.
