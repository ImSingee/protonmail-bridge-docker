# ProtonMail IMAP/SMTP Bridge Docker Container

This is an unofficial Docker container of the [ProtonMail Bridge](https://protonmail.com/bridge/). Some of the scripts are based on [Hendrik Meyer's work](https://gitlab.com/T4cC0re/protonmail-bridge-docker).

Docker Hub: <https://github.com/ImSingee/protonmail-bridge-docker/pkgs/container/protonmail-bridge-docker>

GitHub: <https://github.com/ImSingee/protonmail-bridge-docker>

## Tags

tag | description
 -- | --
`master` | latest dev image - do not use it in production
`[version]` | image for specific proton-bridge version

Note: There is no `latest` tag, and we don't accept PRs for adding it.

## Initialization

Firstly, you need to initialize the required data before using it.

Run the following command to initialize the required data:

```bash
export VERSION=v3.21.2 # change it to the version you want to use
export DATA_DIR=./data # change it to the directory you want to store the data

docker run --rm -it -v ${DATA_DIR}:/root ghcr.io/imsingee/protonmail-bridge-docker:${VERSION} init
```

Wait for the bridge to startup, then you will see a prompt appear for [Proton Mail Bridge interactive shell](https://proton.me/support/bridge-cli-guide). Use the `login` command and follow the instructions to add your account into the bridge. Then use `info` to see the configuration information (username and password). After that, use `exit` to exit the bridge. You may need `CTRL+C` to exit the docker entirely.

## Run

To run the container, use the following command.

```bash
# export VERSION=...
# export DATA_DIR=...

docker run -d --name=protonmail-bridge -v ${DATA_DIR}:/root -p 1025:1025 -p 1143:1143 --restart=unless-stopped ghcr.io/imsingee/protonmail-bridge-docker:${VERSION}
```

## Bridge CLI Guide

The initialization step exposes the bridge CLI so you can do things like switch between combined and split mode, change proxy, etc. The [official guide](https://protonmail.com/support/knowledge-base/bridge-cli-guide/) gives more information on to use the CLI.

