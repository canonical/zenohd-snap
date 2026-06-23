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

The router starts automatically as a daemon after installation. Manage it with:

```bash
snap services zenohd          # check status
snap logs zenohd.zenohd       # view logs
snap stop zenohd.zenohd       # stop the daemon
snap start zenohd.zenohd      # start the daemon
```

To use a custom configuration, place a JSON5 file at
`/var/snap/zenohd/common/config.json5` before starting the daemon:

```bash
sudo cp my-config.json5 /var/snap/zenohd/common/config.json5
sudo snap restart zenohd.zenohd
```

To use a YAML config or a different path, pass `-c` explicitly instead:

```bash
snap set zenohd daemon-options="-c /path/to/config.yaml"
```

A reference configuration is bundled at `$SNAP/etc/zenohd/DEFAULT_CONFIG.json5`.

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
sudo snap restart zenohd.zenohd
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
