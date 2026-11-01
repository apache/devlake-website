---
title: "Developer Setup"
description: >
  The steps to install DevLake in developer mode.
sidebar_position: 1
---


## Requirements

- <a href="https://docs.docker.com/get-docker" target="_blank">Docker v19.03.10+</a>
- <a href="https://golang.org/doc/install" target="_blank">Golang v1.19+</a>
- <a href="https://www.gnu.org/software/make/" target="_blank">GNU Make</a>
  - Mac (Preinstalled)
  - Windows: [Download](http://gnuwin32.sourceforge.net/packages/make.htm)
  - Ubuntu: `sudo apt-get install build-essential libssl-dev`

## How to setup dev environment

The following guide will walk through how to run DevLake's frontend (`config-ui`) and backend in dev mode.


1. Navigate to where you would like to install this project and clone the repository:

   ```sh
   git clone https://github.com/apache/devlake.git
   cd devlake
   ```

2. Install dependencies for plugins:

   - [RefDiff](../Plugins/refdiff.md#development)

3. Install Go packages

    ```sh
    cd backend
	go get
    cd ..
    ```

4. Copy the sample config file to new local file:

    ```sh
    cp env.example .env
    ```

5. Update the following variables in the file `.env`:

    * `DB_URL`: Replace `mysql:3306` with `127.0.0.1:3306`

6. Start the MySQL and Grafana containers:

    > Make sure the Docker daemon is running before this step.

    ```sh
    docker-compose -f docker-compose-dev.yml up -d mysql grafana
    ```

7. Run `devlake` and `config-ui` in dev mode in two separate terminals:

    ```sh
    # specify the plugins that you need for both backend and frontend
    export DEVLAKE_PLUGINS=bamboo,bitbucket,circleci,customize,dora,gitextractor,github,github_graphql,gitlab,jenkins,jira,org,pagerduty,refdiff,slack,sonarqube,trello,webhook
    # install poetry, just follow the guide in: https://python-poetry.org/docs/#installation 
    # run devlake
    make dev
    # run config-ui
    make configure-dev
    ```

    For common errors, please see [Troubleshooting](#troubleshooting).

8.  Config UI is running at `localhost:4000`
    - For how to use Config UI, please refer to our [tutorial](Configuration/Tutorial.md)

## Running Tests

```sh
# install mockery
go install github.com/vektra/mockery/v2@latest
# generate mocking stubs
make mock
# run tests
make test
```

## DB migrations

Please refer to the [Migration Doc](../DeveloperManuals/DBMigration.md).

## Using DevLake API

All DevLake APIs (core service + plugin API) are documented with swagger. To see API doc live with swagger:

    - Install [swag](https://github.com/swaggo/swag).
    - Run `make swag` to generate the swagger documentation.
    - Visit `http://localhost:8080/swagger/index.html` while `devlake` is running.


## Developing dashboards

To access Grafana, click *View Dashboards* button in the top left corner of Config UI, or visit `localhost:3002` (username: `admin`, password: `admin`).

For provisioning, customizing, and creating dashboards, please refer to our [Grafana Doc](../Configuration/Dashboards/GrafanaUserGuide.md).


## Troubleshooting


    Q: Running `make dev` yields error: `libgit2.so.1.3: cannot open share object file: No such file or directory`

    A: `libgit2.so.1.3` is required by the gitextractor plugin and should be . Make sure your program can find `libgit2.so.1.3`. `LD_LIBRARY_PATH` can be assigned like this if your `libgit2.so.1.3` is located at `/usr/local/lib`:

    ```sh
    export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib
    ```
   
    Note that the version has to be pinned to 1.3.0. If you don't have it, you may need to build it manually with CMake from [source](https://github.com/libgit2/libgit2/releases/tag/v1.3.0).


## Compiling

`make build` produces a single `bin/lake` binary: the framework, the API and every official plugin are compiled into it.

```sh
cd backend
make build
```

While iterating you usually do not want all of them. `DEVLAKE_PLUGINS` takes a comma-separated list:

```sh
DEVLAKE_PLUGINS=jira,gitlab make build
```

That variable also decides what the registry generator writes to `backend/plugins/all/all.go`, which is checked in. Run `make plugins-registry` without `DEVLAKE_PLUGINS` before committing, or CI's drift check will fail.

Release builds strip symbols. Set `DEVLAKE_DEBUG=1` to keep them, and use `make debug` to start the server under `dlv`.

To build plugins as shared objects instead — for sideloading, or for a server without any plugin compiled in — use:

```sh
make build-plugin                              # every plugin
DEVLAKE_PLUGINS=jira,gitlab make build-plugin  # a subset
```

The output lands in `bin/plugins/<name>/<name>.so`; set `PLUGIN_DIR` to write it somewhere else.

A server with no plugin compiled in is built with an empty registry:

```sh
DEVLAKE_PLUGINS=none make build
```

The `devlake_external_plugins` build tag selects the same empty registry and makes the intent explicit:

```sh
go build -tags devlake_external_plugins -o bin/lake ./server
```

## Sideloading plugins

A released `lake` binary already contains every official plugin, and it still accepts extras. Any `.so` found under `PLUGIN_DIR` (default `bin/plugins`) is opened at startup and registered on top of the compiled-in set, so you can add a custom plugin — or replace an official one — without rebuilding DevLake.

Two things to know before you try it:

- The `.so` has to be built with `go build -buildmode=plugin` against the **same DevLake version, Go toolchain, build flags and dependency versions** as the binary that loads it. Go rejects a plugin whose copy of a shared package differs, so build it in the matching `lake-builder` image rather than on your host.
- A plugin that fails to load is logged and skipped. The server keeps running without it, so check the log after adding one.

The plugin entry point (`PluginEntry`, implementing `plugin.PluginMeta`) and the interfaces a plugin can implement are described in the [plugin development manual](https://github.com/apache/devlake/blob/main/backend/DevelopmentManual.md).

## References

To dig deeper into developing and utilizing our built-in functions and have a better developer experience, feel free to dive into our [godoc](https://pkg.go.dev/github.com/apache/devlake) reference.
