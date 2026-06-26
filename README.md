# zenohd-snap

The [Eclipse Zenoh](https://zenoh.io) router as a snap.

[![Get it from the Snap Store](https://snapcraft.io/en/dark/install.svg)](https://snapcraft.io/zenohd)
[![zenohd](https://snapcraft.io/zenohd/badge.svg)](https://snapcraft.io/zenohd)

## Install

To install the snap from the store:

```bash
snap install zenohd
```

## Usage

### Daemon

The daemon is installed disabled. Enable and start it with:

```bash
sudo snap start --enable zenohd.daemon
```

Manage it with:

```bash
snap services zenohd.daemon          # check status
snap logs zenohd.daemon              # view logs
sudo snap stop --disable zenohd.daemon   # stop and disable
```

### Ad-hoc router

To run an ad-hoc router instance without the daemon:

```bash
zenohd [OPTIONS]
```

### Configuration

To use a custom configuration, place a config file at one of the following
paths. The launcher probes them in order and uses the first one found:

| Path | Available to |
|------|-------------|
| `~/snap/zenohd/common/config.{json5,yaml,yml}` | plain app |
| `/var/snap/zenohd/common/config.{json5,yaml,yml}` | daemon and plain app |

`~/snap/zenohd/common/` takes precedence when both directories contain a config
file. Having more than one config extension in the same directory is an error.

```bash
# daemon: place config in SNAP_COMMON
sudo cp my-config.json5 /var/snap/zenohd/common/config.json5
sudo snap restart zenohd.daemon

# plain app: optionally place config in SNAP_USER_COMMON (takes precedence)
cp my-config.json5 ~/snap/zenohd/common/config.json5
zenohd
```

Only one config extension (`json5`, `yaml`, or `yml`) may exist in each
directory at a time. A reference configuration is bundled at
`$SNAP/etc/zenohd/DEFAULT_CONFIG.json5`.

To pass a config file explicitly, use `--config` (or `-c`); auto-discovery is
skipped when this flag is present:

```bash
zenohd --config /path/to/my-config.json5
```

## Plugins

zenohd supports runtime-loadable plugins. Plugin configuration lives entirely
in the config file — no CLI flags are needed.

Place plugin `.so` files in `/var/snap/zenohd/common/plugins/` and enable them
via `/var/snap/zenohd/common/config.json5`:

```json5
{
  plugins_loading: {
    enabled: true,
    search_dirs: ["/var/snap/zenohd/common/plugins"],
  },
  plugins: {
    // REST API plugin — exposes zenoh over HTTP on port 8000
    rest: {
      __required__: true,
      http_port: 8000,
    },
  },
}
```

Then restart the daemon to pick up the changes:

```bash
sudo snap restart zenohd.daemon
```

## Development

Make sure that snapcraft is [installed and set up](https://snapcraft.io/docs/snapcraft-setup).

To build the snap:

```bash
snapcraft pack
```

To install the locally built snap:

```bash
snap install --dangerous zenohd_*.snap
```

To run the test suite against the built snap:

```bash
sudo snap/tests/test-help.sh zenohd_*.snap
```
