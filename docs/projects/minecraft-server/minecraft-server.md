# Minecraft Server with Docker

A reproducible Minecraft Java Edition server running in Docker, configured entirely through environment variables.

## Table of Contents

- [Description](#description)
- [Quickstart](#quickstart)
- [Usage](#usage)
- [Configuration](#configuration)
- [Testing](#testing)

## Description

import GithubLinkAdmonition from '@site/src/components/GithubLinkAdmonition';

<GithubLinkAdmonition
    link="https://github.com/bjoerndaigger/minecraft-server"
    text="bjoerndaigger/minecraft-server"
    title="Github Repository"
    type="info"
>
All sources discussed on this page - Dockerfile, Compose file, entrypoint script and templates.
</GithubLinkAdmonition>

This repository packages a Minecraft Java Edition server into a self-contained Docker setup. Instead of pulling one of the popular prebuilt community images, it builds its own image from scratch, so every part of the setup stays visible and reproducible: which Java runtime is used, which server version is downloaded, and how the server is configured at startup.

The image is based on `eclipse-temurin:25-jre` and downloads a pinned Minecraft server JAR during the build. Pinning the download URL means the same image build always produces the same server version, instead of silently jumping to whatever Mojang publishes next. The Minecraft EULA is accepted at build time by writing `eula=true` into `eula.txt`.

The more interesting part is the configuration. A Minecraft server is configured through a single `server.properties` file, which is awkward in a container - it lives inside the image, gets overwritten by the server, and editing it means rebuilding or shelling into the container. This project solves that by never shipping a finished `server.properties` at all. Instead, the image contains `server.properties.template`, a version of the file where every value is an environment variable placeholder. On each container start, the entrypoint script renders the real configuration with `envsubst` before launching the server.

The practical effect is that the whole server is configured through a `.env` file, and changing the difficulty or the player limit is a restart, not a rebuild. It also acts as a safety net: only properties that exist in the template can be written, so a typo in `.env` cannot introduce a broken or unsupported setting.

World data is kept out of the container entirely. A named Docker volume is mounted at `/data`, and the server is started from that directory, so everything it writes - the world, player data, logs, the generated config - lands on the volume and survives recreating the container.

Finally, the repository includes a small test setup based on [mcstatus](https://github.com/py-mine/mcstatus), a Python client for the Minecraft server protocol. It queries the running server the same way a real client would, which verifies the container from the outside rather than just checking whether the process is alive.

## Quickstart

### Prerequisites

- [Docker](https://docs.docker.com/get-started/get-docker/) and Docker Compose
- Python 3.10+ (only needed for testing)
- A Minecraft Java Edition client to connect with

Clone the repository:

```bash
git clone https://github.com/bjoerndaigger/minecraft-server.git
cd minecraft-server
```

Copy the `.env.template` file to `.env` and adjust the values:

```bash
cp .env.template .env # Mac/Linux
copy .env.template .env # Windows (CMD)
```

Build the image and start the server:

```bash
docker compose up --build -d
```

The server is now reachable at `localhost:8888`. To stop it:

```bash
docker compose down
```

> [!NOTE]
> By starting the server, you accept the [Minecraft End User License Agreement (EULA)](https://www.minecraft.net/en-us/eula). Review it before use.

## Usage

Add a server in the Minecraft client under **Multiplayer → Add Server** and enter `localhost:8888` as the address. The container listens on Minecraft's default port `25565` internally, which Docker Compose maps to host port `8888`.

The container runs with `tty` and `stdin_open` enabled, so the interactive server console stays available. Attach to it to run commands such as `op` or `stop`:

```bash
docker attach minecraft-server-mc-server-1
```

Detach again with `Ctrl+P` followed by `Ctrl+Q` - pressing `Ctrl+C` would stop the server.

World data lives in the named volume `local_data` and survives both container restarts and `docker compose down`. Since `restart: unless-stopped` is set, the server also comes back automatically after a host reboot. To wipe the world and start over:

```bash
docker compose down --volumes
```

## Configuration

All settings are defined in `.env` and mapped to `server.properties` at container start. Changing a value only requires a restart:

```bash
docker compose restart
```

| Variable | Description | Default value |
|----------|-------------|---------------|
| `GAMEMODE` | Default game mode for joining players (`survival`, `creative`, `adventure`, `spectator`) | `survival` |
| `DIFFICULTY` | World difficulty (`peaceful`, `easy`, `normal`, `hard`) | `easy` |
| `ALLOW_CHEATS` | Allows commands such as `/gamemode` for all players | `false` |
| `FORCE_GAMEMODE` | Forces players back into the default game mode when they rejoin | `false` |
| `LEVEL_NAME` | Name of the world folder inside the volume | `world` |
| `LEVEL_SEED` | Seed for world generation; empty means random | *(empty)* |
| `VIEW_DISTANCE` | Radius of chunks sent to clients | `10` |
| `SIMULATION_DISTANCE` | Radius of chunks in which entities and ticks are simulated | `10` |
| `MAX_PLAYERS` | Maximum number of simultaneous players | `10` |
| `ONLINE_MODE` | Verifies players against Mojang's authentication servers | `true` |
| `ALLOW_FLIGHT` | Permits flight in survival mode | `false` |
| `PLAYER_IDLE_TIMEOUT` | Minutes before idle players are kicked; `0` disables it | `0` |
| `SPAWN_PROTECTION` | Radius in blocks around spawn that only operators can build in | `16` |
| `SERVER_PORT` | Port the server listens on inside the container | `25565` |
| `MOTD` | Message shown in the client's server list | `A Minecraft Server` |

Adding a new setting requires two edits, since the template defines which properties may be written: add the placeholder to `server.properties.template`, add the variable to `.env.template` and `.env`, then rebuild with `docker compose up --build`. A full list of available properties is documented in the [Minecraft Wiki](https://minecraft.wiki/w/Server.properties).

## Testing

With the server running, set up a virtual environment and install `mcstatus`:

```bash
python -m venv .venv
source .venv/bin/activate # Mac/Linux
.venv\Scripts\activate.bat # Windows (CMD)
pip install -r requirements.txt
```

Query the server from the host:

```bash
# Full status (version, players, ping)
mcstatus localhost:8888 status

# Latency only
mcstatus localhost:8888 ping
```

A successful status response confirms that the port mapping works, the server JAR started, and the generated `server.properties` was accepted.
