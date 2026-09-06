# garm-bin

AUR package that installs [GARM](https://github.com/cloudbase/garm) (GitHub
Actions Runner Manager) from the official upstream binary release.

GARM is a self-hosted runner orchestrator: it creates and scales ephemeral
GitHub Actions (and Gitea) runners on demand across compute providers like
LXD, Incus, OpenStack, Azure, AWS, GCP, and Kubernetes.

## What's included

- **Binaries:** `/usr/bin/garm` (the daemon) and `/usr/bin/garm-cli`
  (statically linked, straight from the upstream release tarballs, for both
  `x86_64` and `aarch64`)
- **Service:** `garm.service` (upstream's unit, pointed at `/usr/bin/garm`),
  running as the dedicated `garm` system user created via `sysusers.d`
- **Directories:** `/etc/garm/` (configuration) and `/var/lib/garm/` (state,
  e.g. the sqlite database), both owned by `garm`, plus
  `/opt/garm/providers.d/` for external provider binaries

## Install

```bash
yay -S garm-bin
```

Or manually:

```bash
git clone https://aur.archlinux.org/garm-bin.git
cd garm-bin
makepkg -si
```

## Setup

The service reads `/etc/garm/config.toml`, which you must create before it
will start — the upstream
[systemd quickstart](https://github.com/cloudbase/garm/blob/main/doc/quickstart-systemd.md)
has a complete working example. Point the database at the state directory:

```toml
[database.sqlite3]
  db_file = "/var/lib/garm/garm.db"
```

Install one or more
[external providers](https://github.com/cloudbase/garm/blob/main/doc/providers.md)
into `/opt/garm/providers.d/`, then:

```bash
sudo systemctl enable --now garm
garm-cli init --name my_garm --url http://<garm-host>
```

## Automatic updates

A GitHub Actions workflow checks daily for new GARM releases and pushes
updates to the AUR automatically.

## License

The packaging files in this repository are provided under
[Apache-2.0](LICENSE.md). GARM itself is licensed under Apache-2.0 by the
[upstream project](https://github.com/cloudbase/garm).
