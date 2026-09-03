# folder-backup - Local folder archive backup and restore with narrow sudo deposit

![Version](https://img.shields.io/badge/Version-1.16.0-blue?style=flat-square)
![License](https://img.shields.io/badge/License-MIT-green?style=flat-square)
[![CIAO](https://img.shields.io/badge/Philosophy-CIAO%20(Caution%20%E2%80%A2%20Intentional%20%E2%80%A2%20Anti--fragile%20%E2%80%A2%20Over--engineered)-purple.svg)](https://github.com/cloudgen/ciao)
[![Stars](https://img.shields.io/github/stars/cloudgen/folder-backup?style=flat-square)](https://github.com/cloudgen/folder-backup)

**folder-backup** packs a folder you name into a dated gzip archive, stores it under `/var/backup/folder-backup/`, checks the file count and size, and can put that folder back onto the hard-disk projects tree. A normal login can install the program locally, write a grant file you can read, and submit it. Copying the archive into `/var/backup` needs an admin-installed narrow grant first. There is no online `curl|sh` install.

| You (your own login) | Admin / already root | Not this |
|----------------------|----------------------|----------|
| Install to `~/.local/bin`, generate and submit a grant, run `backup` / `restore` once the grant exists | Install into `/usr/local/bin` and install the sudoers fragment | No download-and-run install channel; a normal login does not write `/etc` |

## Features

- **Local self-management**: `install`, `uninstall`, `where-is-me`, `version`, `about`, `help`; on a real terminal a bare `folder-backup` (or `menu` / `main`) shows the numbered work list
- **Backup**: `backup <folder>` → stage tar.gz → elevated deposit → verify → **retention prune** (max **5**/day, **30** total per project basename)
- **Retention**: `MAX_DAILY_BACKUPS` / `MAX_TOTAL_BACKUPS` (defaults 5 / 30); oldest first; never cross-basename
- **Restore**: `restore <archive|prefix> [dest]` — default dest is hard-disk `${PROJECTS_ROOT}/<project>`
- **Restore dest whitelist**: allow `/etc/{{username}}` (invoking user); always refuse `/etc/passwd` and other non-whitelisted system paths
- **Narrow sudoers**: `print-sudoers` emits deposit / verify-list / restore-stage allowlist (admin installs to `/etc/sudoers.d/`)
- **Sudoer approval submit**: `generate-sudoer-request` writes a local JSON grant you can review; then `submit-sudoer-request` lets sudoer-cli allocate a JSON request into `/var/sudoer-cli/sudoer-request` (does not write `/etc`, does not `mkdir` inbound)
- **Fail-closed**: missing source, unauthorized deposit, verify mismatch, non-empty restore without `--force`
- **CIAO / CIAO-Lite** defensive design (Protection Zones, `out_*` output SSOT)

## Quick Installation

**Local (your own login, no root needed):**

```sh
# From this repository checkout
sh src/folder-backup install
# or force refresh after updates
sh src/folder-backup install --force

# Ensure ~/.local/bin is on PATH, then:
folder-backup version
```

**Global (preferred before durable sudoers / production elevation):**

```sh
sudo sh src/folder-backup install
# or: folder-backup install --global   # needs write access to /usr/local/bin
# Managed binary mode is always 0755 so every user can run the shell ship unit.
# If an older install left 0711 (rwx--x--x), re-run: sudo sh src/folder-backup install
```

**Sudoers (required for non-root deposit / restore of root-owned archives):**

```sh
# Prefer: refresh project-sudoers-file + write admin script under /dev/shm
# Production (global install present):
folder-backup print-sudoers-install-script
# Test mode only (local/unmanaged):
folder-backup print-sudoers-install-script --allow-test-local

# Admin (account with sudo rights) — handoff script (path printed by CLI):
sudo sh /dev/shm/folder-backup-<user>-sudoers-admin.sh install   # visudo + install 0440
sudo sh /dev/shm/folder-backup-<user>-sudoers-admin.sh replace   # remove old then install
sudo sh /dev/shm/folder-backup-<user>-sudoers-admin.sh uninstall # leave test elevation
sudo sh /dev/shm/folder-backup-<user>-sudoers-admin.sh status

# Manual equivalent still valid (paths are per-user — multi-user safe):
# sudo visudo -c -f ~/.config/folder-backup/sudoers.fragment-<user>
# sudo install -m 0440 ~/.config/folder-backup/sudoers.fragment-<user> /etc/sudoers.d/folder-backup-<user>
```

**Security note:** Local `~/.local/bin` install is **not** production-secure for host elevation — the user can change the binary and stage trees. Prefer global install for any host that keeps `/etc/sudoers.d/folder-backup-<user>`. Multi-user hosts get **one fragment file per user** (no shared overwrite). See [`SECURITY.md`](./SECURITY.md).

This product is **local-only** for its install *channel* (no default `SCRIPT_URL` online install). Global vs local here means install *location*, not an online channel.

After install, on a terminal:

```text
$ folder-backup
[INFO] **folder-backup**(*1.16.0*) — numbered list of live work commands
1. backup: *Pack a named folder into a dated gzip archive under /var/backup/folder-backup*
2. restore: *Put an archive back onto the hard-disk projects tree*
3. sudoers: *Grant and drafts*
9. Exit
Choice: 9
```

Choose a number, or type the command name. `3` opens grant/draft setup (JSON grant, inbound submit, sudoers text, admin install script, remove draft — each is also a typed command). `8` goes back from that list. `9` exits. `folder-backup sudoers` is not a command. In a script or pipe, `folder-backup` with no arguments prints help instead.

**Source repository:** [cloudgen/folder-backup](https://github.com/cloudgen/folder-backup)  
Config identity: `REPO_USER=cloudgen`, `REPO_NAME=folder-backup` (override with env if needed; does not enable online install while `SCRIPT_URL` is empty).

## Usage

```sh
folder-backup                               # TTY numbered work list; off-TTY is help
folder-backup help
folder-backup menu                          # same list as a bare TTY run; off-TTY is help
folder-backup about
folder-backup --json about

folder-backup backup /path/to/project
folder-backup restore project-name              # → hard-disk PROJECTS_ROOT/project-name
folder-backup restore project-name --disk       # explicit hard-disk
folder-backup restore project-name --ram        # → /dev/shm/project-name
folder-backup restore NAME-YYYYMMDD-N.tar.gz /explicit/dest
folder-backup restore project-name --force      # allow non-empty dest

folder-backup print-sudoers
folder-backup generate-sudoer-request   # local verified JSON (review this file)
folder-backup submit-sudoer-request     # JSON request into /var/sudoer-cli/sudoer-request (if present)
folder-backup submit-sudoer-request ~/.config/folder-backup/sudoer-request-$(id -un).json
folder-backup uninstall --force
```

**Environment (selected):**

| Variable | Role |
|----------|------|
| `REPO_USER` | Git host owner (default `cloudgen`) |
| `REPO_NAME` | Git repository name (default `folder-backup`) |
| `SCRIPT_URL` | Online install channel (default **empty** — local only) |
| `BACKUP_ROOT` | Durable root (default `/var/backup`) |
| `BACKUP_NOTATION` | Subdir (default `folder-backup`) |
| `PROJECTS_ROOT` | Hard-disk projects tree for restore default |
| `RAM_ROOT` | RAM projects root (default `/dev/shm`) |
| `RESTORE_HOST_DEFAULT` | `hard-disk` (default) or `ram-drive` |
| `ALLOW_TEST_LOCAL_SUDOERS` | `1` = allow test-mode `print-sudoers` / `generate-sudoer-request` / `submit-sudoer-request` without `--allow-test-local` |
| `SUDOER_CLI` | Override path to `sudoer-cli` |
| `SUDOER_ADM_USER` | Approver login to detect (default `sudoer-adm`) |

## Examples

```sh
# Backup the RAM genesis tree
folder-backup backup /dev/shm/genesis-template

# Restore latest genesis-template-* archive to hard-disk projects tree
folder-backup restore genesis-template

# Restore into a temporary path
folder-backup restore genesis-template-20260803-3.tar.gz /tmp/genesis-restore
```

## Platform Compatibility

| Platform | Status |
|----------|--------|
| Linux, `/bin/sh` (dash/bash) | Supported |
| `tar`, `find`, `date` | Required |
| `sudo` + narrow sudoers | Required for non-root deposit/restore of root-owned archives |
| macOS / BSD | Not primary; GNU `stat`/`sed -E` assumptions may differ |

## Related Projects

- [folder-backup](https://github.com/cloudgen/folder-backup) — this product
- [CIAO Defensive Programming](https://github.com/cloudgen/ciao)
- [CIAO-Lite](https://github.com/cloudgen/ciao-lite)
- [cli-template](https://github.com/cloudgen/cli-template) — bootstrap parent architecture (Type 0 local-only template)

## Contributing

Keep changes surgical. Honor **CIAO-Lite Protection Zones** in `src/folder-backup`. Product behavior must stay consistent with live `docs/requirements/requirement-*.md`. Run `sh tests/run.sh` before proposing commits.

## License

MIT License — see [`LICENSE.md`](./LICENSE.md).

## Last Update

2026-09-03 — version **1.16.0** (sudoers family submenu: grant/draft setup verbs stay live CLI commands; TP-CLI-13/16/18).
2026-09-03 — version **1.15.0** (numbered list look: **folder-backup**(*version*) header; italic gray descriptions; TP-CLI-18).
2026-08-30 — version **1.14.0** (storage = cache folder + persistence `${HOME}/.local/folder-backup/`; TP-CLI-06/12).
2026-08-30 — version **1.13.0** (about Cache folder preferred `/dev/shm/cache/cache-folder-backup`; TP-CLI-06/12).
2026-08-28 — version **1.12.0** (TTY empty argv opens the numbered work list; off-TTY still help; TP-CLI-07/13).
2026-08-23 — version **1.11.0** (`menu` / `main` numbered work list; TP-CLI-13..16).
2026-08-23 — version **1.10.0** (`print-sudoers` / JSON emit `backup *` / `restore *`; TP-26; INC-20260823-001).
2026-08-18 — housekeeping: Description rewritten in people-and-folders voice (no Type-1 lead); install heading says “your own login.” Version still **1.9.0** (no product-source change).
2026-08-17 — version **1.9.0** (`generate-sudoer-request`; independent generate dest; operator-readable errors; TP-24/25).
2026-08-17 — version **1.8.2** (submit **update** when `/etc/sudoers.d/folder-backup-<user>` exists; TP-23).
2026-08-17 — version **1.8.1** (submit inbound fidelity; pretty JSON re-encode; TP-22e/22f; review JR-1..8).
2026-08-15 — version **1.8.0** (JSON sudoer file = `folder-backup` backup/restore only; TP-22/22b/22c).
