# docker-cs2

> [Counter-Strike 2 (CS2) Dedicated Server](https://developer.valvesoftware.com/wiki/Counter-Strike_2_Dedicated_Servers) with automated/manual updating

## Table of Contents

- [How to Use This Image](#how-to-use-this-image)
- [Image Variants](#image-variants)
- [Environment Variables](#environment-variables)
  - [General](#general)
  - [Other](#other)
- [Populating with Own Server Files](#populating-with-own-server-files)
- [Updating the Server](#updating-the-server)
  - [Automated (recommended)](#automated-recommended)
  - [Manually](#manually)

## How to Use This Image

```sh
$ docker run \
  -v=cs2:/home/cs2/server \
  --net=host \
  ghcr.io/sazarkin/cs2:latest
```

This is a bare minimum example and the server will be:

- installed on a volume named `cs2` to [ensure persistence of server files](https://docs.docker.com/storage/).
- running on the default port `27015` on the `host` network for [optimal network performance](https://docs.docker.com/network/host/)
- running in LAN mode since a [Game Server Login Token](#cs2_gslt) (GSLT) is required to run the server on the internet.

To configure the server with more advanced settings, set [environment variables](#environment-variables).

## Image Variants

Each variant refers to a tag, e.g. `ghcr.io/sazarkin/cs2:<tag>`.

##### [`latest`](https://github.com/timche/docker-csgo/blob/master/base-cs2/Dockerfile) / [`<version>`](https://github.com/timche/docker-csgo/blob/master/base-cs2/Dockerfile)

Vanilla CS2 server.

## Environment Variables

### General

##### `CS2_MAP`

Default: `de_dust2`

Start the server with a specific map.

Sets `+map` in `srcds_run` parameters.

##### `CS2_MAX_PLAYERS`

Default: `16`

Maximum players allowed to join the server.

Sets `-maxplayers_override` in `srcds_run` parameters.

##### `CS2_RCON_PW`

Default: `changeme`

RCON password to administrate the server.

Sets `+rcon_password` in `srcds_run` parameters.

##### `CS2_PW`

Default: None

Password to join the server.

Sets `+sv_password` in `srcds_run` parameters.

##### `CS2_MAP_GROUP`

Default: `mg_active`

Map group.

Sets `+mapgroup` in `srcds_run` parameters.

##### `CS2_GAME_ALIAS`

Default: `competetive`

Game alias.

Sets `+game_alias` in `srcds_run` parameters.

##### `CS2_PARAMS`

Additional `srcds_run` [parameters](https://developer.valvesoftware.com/wiki/Command_Line_Options#Command-line_parameters).

##### `CS2_CUSTOM_FILES_DIR`

Default: `/usr/cs2`

Absolute path to a directory in the container containing custom server files. Changing this is not recommended in order to follow the documentation. See more at "[Populating with Own Server Files](#populating-with-own-server-files)".


### Other

##### `VALIDATE_SERVER_FILES`

Default: `false`

Validate and restore missing/fix broken server files on container start. Can be enabled with `true`.

This should especially be used whenever custom server files have been deleted and have overwritten files before, and you want to restore the original files.

##### `DEBUG`

Default: `false`

Print all executed commands for better debugging.

## Populating with Own Server Files

The server can be populated with your own custom server files (e.g. configs and maps) through a mounted directory that has the same folder structure as the server `cs2` folder in order to add or overwrite the files at their respective paths. Deleted custom server files, which have been added or have overwritten files before, are also removed from the `cs2` folder. The directory must be mounted at [`CS2_CUSTOM_FILES_DIR`](#cs2_custom_files_dir) (default: `/usr/cs2`) and will be synced with the server `cs2` folder at each start of the container.

**Note:** See [`VALIDATE_SERVER_FILES`](#validate_server_files) on how to restore original files if they've been overwritten before but are removed now.

### Example

#### Host

Custom server files in `/home/user/custom-files`:

<!-- prettier-ignore-start -->
```sh
custom-files
├── addons
│   └── sourcemod
│       └── configs
│           └── admins_simple.ini # Will be overwritten
└── cfg
    └── server.cfg # Will be added
```
<!-- prettier-ignore-end -->

#### Container

`/home/user/custom-files` mounted to [`CS2_CUSTOM_FILES_DIR`](#cs2_custom_files_dir) (default: `/usr/cs2`) in the container:

<!-- prettier-ignore-start -->
```sh
$ docker run \
  -v=cs2:/home/cs2/server \
  -v=/home/user/custom-files:/usr/cs2 \ # Mount the custom files directory
  --net=host \
  ghcr.io/sazarkin/cs2:latest
```
<!-- prettier-ignore-end -->

## Updating the Server

Once the server has been installed, the container will check for a server update at every container start.

### Manually

Restart the container with [`docker restart`](https://docs.docker.com/engine/reference/commandline/restart/).

#### Example

Container named `cs2`:

```sh
$ docker restart cs2
```
