# Requirements index

**Product:** folder-backup (POSIX `/bin/sh` local self-managed CLI — folder tar.gz backup with narrow sudo deposit)  
**Workspace state:** Specialized product law (left genesis); **software-development** class; bootstrap **cli-template → folder-backup** (domain extend; online install **intentionally absent** on A and B).  
**Updated:** 2026-08-30

| ID / key | Title | Area | Status | Path | Updated |
|----------|-------|------|--------|------|---------|
| requirement-class-software-dev | Software-development class law + residual stack (posix-sh, local-only); multi-vault forge push identity §2.0.5a; residual no dest approver / no dest fence; §1.1 | class | Active (1.1.1) | `requirement-class-software-dev.md` | 2026-08-28 |
| requirement-bootstrap-chain | Bootstrap chain A=cli-template → B=folder-backup (domain extend); empty argv case 2 | architecture | Active (2.1.0) | `requirement-bootstrap-chain.md` | 2026-08-28 |
| requirement-project-folder | Project layout (`src/`), install bins, `/var/backup` deposit | architecture | Active | `requirement-project-folder.md` | 2026-08-03 |
| requirement-three-layer-privilege-model | Type 0 + narrow Type 1 deposit; sudoers emit + **install-script** handoff; **per-user** fragment names; **trust tiers** (S13); submit workflow + inbound fidelity + **host-probe add/update** + **independent generate** (readable dest); fragment **MUST** be `backup *` / `restore *` **and** `--json` twins | architecture | Active (1.12.0) | `requirement-three-layer-privilege-model.md` | 2026-08-23 |
| requirement-sudoer-json-file | **JSON sudoer file** SSOT: `{{PRJ_NAME}}` only; `args` **MUST** be verb plus `*` **and** `--json` twins; pretty/compact legal; re-encode **MUST** keep every `commands[]` object; **independent generate** dest readable | architecture | Active (1.4.0) | `requirement-sudoer-json-file.md` | 2026-08-23 |
| requirement-folder-archive-backup | **Backup/restore ops SSOT**: backup + verify + **restore**; dest whitelist **W-ETC-USER** `/etc/{{username}}` (never `/etc/passwd`) | backup | Active (1.2.0) | `requirement-folder-archive-backup.md` | 2026-08-12 |
| requirement-folder-archive-backup-retention-total | **Total retention**: max **30** archives per project basename; prune oldest after successful deposit | backup | Active (1.0.0) | `requirement-folder-archive-backup-retention-total.md` | 2026-08-12 |
| requirement-folder-archive-backup-retention-daily | **Daily retention**: max **5** archives per basename per calendar day; prune oldest same-day `N` | backup | Active (1.0.0) | `requirement-folder-archive-backup-retention-daily.md` | 2026-08-12 |
| requirement-shell-cli-interface | Shell CLI interface (commands, flags, dispatch, modes); submit `--add`/`--update`; **generate-sudoer-request**; **`menu`/`main` routed**; **case 2 TTY empty argv = menu**; test-purpose grant-emit listed apart | shell | Active (1.7.0) | `requirement-shell-cli-interface.md` | 2026-08-28 |
| requirement-shell-cli-zero-arguments | **Withdrawn** — Type N always-help superseded by case 2 default-interaction | shell | Withdrawn (1.1.0) | `requirement-shell-cli-zero-arguments.md` | 2026-08-28 |
| requirement-shell-cli-default-interaction | Claimed TTY numbered list on **empty argv** (case 2) and `menu`/`main`; four work rows; test-purpose grant-emit omitted; **implemented** | shell | Active (1.3.0) | `requirement-shell-cli-default-interaction.md` | 2026-08-28 |
| requirement-shell-local-self-management | Local install / uninstall / where-is-me; **mode 0755** multi-user | shell | Active (1.2.1) | `requirement-shell-local-self-management.md` | 2026-08-28 |
| requirement-shell-output-requirements | Central `out_*` output SSOT | shell | Active | `requirement-shell-output-requirements.md` | 2026-08-03 |
| requirement-operator-readable-error | Operator-facing error **wording** (human-intro style: what happened / next step) | shell | Active (1.0.0) | `requirement-operator-readable-error.md` | 2026-08-17 |
| requirement-shell-modular-function-design | Single-file modular prefixes (`out_`/`inst_`/`app_`/`fb_`) | shell | Active | `requirement-shell-modular-function-design.md` | 2026-08-03 |
| requirement-shell-idempotency | Re-run safety; archive next-N no overwrite | shell | Active | `requirement-shell-idempotency.md` | 2026-08-03 |
| requirement-shell-interactive-vs-noninteractive | Interactive vs non-interactive / confirm policy | shell | Active | `requirement-shell-interactive-vs-noninteractive.md` | 2026-08-03 |
| requirement-shell-cli-storage | Cache folder **and** persistence `${HOME}/.local/folder-backup/`; about Cache folder + Persistence storage | shell | Active (1.2.0) | `requirement-shell-cli-storage.md` | 2026-08-30 |
| requirement-domain-folder-backup | Domain **surface** SSOT (four pillars); ops defer to folder-archive-backup; submit public inbound; **host-probe add/update**; **independent generate-sudoer-request**; grant-emit **test-purpose** (help apart) | domain | Active (1.6.2) | `requirement-domain-folder-backup.md` | 2026-08-23 |

## Intentionally absent (by design — inherited from cli-template)

| Parent (cli-template) surface | Status on folder-backup |
|-------------------------------|-------------------------|
| Online install / `SCRIPT_URL` / Type O empty-argv install-ensure | **Absent** (A already absent) |
| `version-check` / `self-update` / `self-uninstall` | **Absent** (A already absent) |
| Automatic companion `.sha256` channel integrity law | **Absent** (A already absent) |

**Install mode:** **local-only** (`install` + `uninstall` + `where-is-me`). Not dual-mode.

**Rules for agents:**

1. Treat rows above as the **live product-law inventory** for folder-backup.  
2. **Do not invent** additional `requirement-*.md` paths — verify on disk and add a registry row in the same change when creating one.  
3. Product source comments cite **only** these live requirement files — never templates/skills as behavioral authority.  
4. This versioned surface lists **requirement rows only** — do not dump templates / skills / terminologies / incidents path inventories here.  
5. Keep Status and Path in sync with each file’s header when status changes.  
6. **Class gate:** software-development requires exactly one Active `requirement-class-software-dev.md` (this registry includes it).  
7. **Domain SSOT:** exactly one Active `requirement-domain-*` (`requirement-domain-folder-backup`).  
8. **Do not reintroduce** online install package without explicit user order and registry update.

When adding a requirement: append a row, create the file under `docs/requirements/`, keep Status in sync with the file header.
