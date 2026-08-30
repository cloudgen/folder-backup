**file**: docs/requirements/requirement-shell-cli-storage.md  
**Status**: Active (Version 1.2.0)  
**Area**: shell  
**Key**: `requirement-shell-cli-storage`  
**Philosophy**: CIAO **v2.10.2** / CIAO-Lite (Caution • Intentional • Anti-fragile • Over-engineered / Over-protect)

## 1. Purpose

This requirement is the **project Single Source of Truth** for **shell CLI storage** of folder-backup. Storage means **two** path families:

| Family | Role | Path |
|--------|------|------|
| **Cache folder** | Volatile scratch / backup staging | preferred `/dev/shm/cache/cache-folder-backup`; fallback `${XDG_CACHE_HOME}/cache-folder-backup` |
| **Persistence storage** | Durable Type 0 product files that survive reboot | `${HOME}/.local/folder-backup/` |

It owns resolver helpers, isolation, `app_main` wire, and about diagnostics for **both** families.

### 1.1 Human-facing

**In one sentence:** `folder-backup about` names the RAM cache folder for throw-away scratch and the persistence folder under your home `.local/folder-backup` for files that must survive a reboot.

| Box | Meaning | Example |
|-----|---------|---------|
| You / this login | See cache vs persistence | `folder-backup about` |
| The other role | Scripts that parse `--json` get the same folders as fields | `folder-backup --json about` |
| Not this file | Install binary under `.local/bin`; durable host deposit under `/var/backup` | `requirement-shell-local-self-management` · `requirement-domain-folder-backup` |

| Includes | Excludes |
|----------|----------|
| Cache folder (preferred) `/dev/shm/cache/cache-folder-backup` | `/dev/shm/folder-backup` as cache (that looks like a project folder) |
| Cache folder (fallback) `${XDG_CACHE_HOME}/cache-folder-backup` | Install binary `${HOME}/.local/bin/folder-backup` |
| Persistence storage `${HOME}/.local/folder-backup/` | Durable host deposit `/var/backup/...` |
| Create each chosen folder before using it | Bare shared `/tmp` dump |

| Surface | What you open | What for |
|---------|---------------|----------|
| `./src/folder-backup` | ship unit | resolvers + about text |
| `folder-backup about` | command | Cache folder lines + Persistence storage |
| `folder-backup --json about` | command | `cache_preferred` / `cache_fallback` / `effective_storage` / `persistent_storage` |

| You do… | What it means | What you type |
|---------|---------------|---------------|
| Check scratch vs keep-files | Preferred cache is RAM. Persistence is `${HOME}/.local/folder-backup/`. Those are different folders. | `folder-backup about` |
| Read machine fields | JSON names both families and the live chosen cache root. | `folder-backup --json about` |

---

## 2. Core Rules / Requirements (Mandatory)

### 2.0 Two families (sacred)

**Storage** on this product **MUST** mean **both** of these. **MUST NOT** treat cache as the only storage family.

| Family | Survives reboot | Typical use |
|--------|-----------------|-------------|
| **Cache folder** | no (RAM/tmp; XDG cache is last resort) | tar.gz staging, `mktemp`, `TMPDIR` |
| **Persistence storage** | yes | durable Type 0 product files that are not the install binary |

**MUST NOT confuse:**

| Path | Owner | Not |
|------|-------|-----|
| `${HOME}/.local/${APP_NAME}/` | Persistence storage (this file) | Install location |
| `${HOME}/.local/bin/${APP_NAME}` | Local install binary | Persistence |
| `/dev/shm/cache/cache-${APP_NAME}` | Cache folder (this file) | Ram-drive **project** tree |
| `/var/backup/${BACKUP_NOTATION}/` | Host deposit (privilege + domain) | Cache or persistence |

### 2.1 Cache resolver SSOT

1. **MUST** keep **one** authoritative cache-resolve helper: **`util_resolve_storage`**.  
2. New code that needs a product scratch/cache **root** **MUST** call `util_resolve_storage` (or `mktemp` under a path it returned).  
3. Resolver **MUST** print the chosen directory path on **stdout** for `$(util_resolve_storage)` capture.  
4. User-visible failure about cache **MUST** use Output SSOT.  
5. **MUST** expose policy helpers **`util_preferred_cache_dir`** and **`util_fallback_cache_dir`** (class B stdout) so about does not invent a second cache path family.

### 2.2 Cache live resolve priority

First match that can be created **and** is writable:

| Order | Condition | Path shape |
|-------|-----------|------------|
| 1 | `/dev/shm` exists and is writable; leaf is writable | `/dev/shm/cache/cache-${APP_NAME}` |
| 2 | `/tmp` is writable; leaf is writable | `/tmp/cache/cache-${APP_NAME}` |
| 3 | Fallback | `STORAGE_DIR` (`${XDG_CACHE_HOME:-${HOME}/.cache}/cache-${APP_NAME}`, env-overridable) |

**MUST NOT** use `/dev/shm/${APP_NAME}` or `/dev/shm/${APP_NAME}-${USERNAME}` as cache — those look like ram-drive **project** folders.

Create `/dev/shm/cache` (prefer mode **1777**) so other logins can add sibling `cache-<app>` leaves. Same spirit under `/tmp/cache`. If the preferred leaf exists but is **not writable**, fall through.

**Create before return:** for the **chosen** cache tier, the resolver **MUST** `mkdir -p` the leaf, then print the path. If no writable leaf can be created → **MUST** fail closed. **MUST NOT** return a path without creating it.

### 2.3 Persistence storage SSOT

1. Persistence **MUST** be **`${HOME}/.local/${APP_NAME}/`**.  
2. **MUST** keep **one** authoritative helper: **`util_resolve_persistent_storage`** (policy path: **`util_persistent_storage_dir`**).  
3. The helper **MUST** `mkdir -p` that folder, then print the path on stdout (class B). Fail closed if create/write fails.  
4. **MUST NOT** use `${HOME}/.local/bin` as persistence.  
5. **MUST NOT** use `${HOME}/.local/share/${APP_NAME}` as a substitute unless a later specialized change explicitly rebinds (this product’s persistence is `.local/${APP_NAME}/`).  
6. **MUST NOT** stage backup archives here by default — staging stays on the cache family.  
7. **MUST NOT** treat host `/var/backup` as this folder.

### 2.4 Isolation (cache)

1. Cache **leaves** **MUST** include **`${APP_NAME}`** as `cache-${APP_NAME}`.  
2. Fallback **MUST** sit under the invoking user’s XDG/home cache (inherently per-user). Preferred shm/tmp leaves sit under a sticky `…/cache/` parent — **MUST NOT** reuse ram-drive-shaped `${APP_NAME}-${USERNAME}` on `/dev/shm`.  
3. **MUST NOT** use a single shared world-writable dump for all apps.  
4. Live product **MUST** export `TMPDIR=${EFFECTIVE_STORAGE_DIR}` so `mktemp` inherits the chosen **cache** root.  
5. New scratch files **MUST** be created via **`util_mktemp`** (or `mktemp` under a path `util_resolve_storage` returned).  
6. **MUST NOT** use predictable `$$` names (forbidden: `/tmp/${APP_NAME}.$$`, `${EFFECTIVE_STORAGE_DIR}/${APP_NAME}.$$`).

**Complete `util_mktemp` sample:**

```sh
util_mktemp() {
    : "${APP_NAME:=folder-backup}"
    : "${EFFECTIVE_STORAGE_DIR:=}"
    _suffix="${1:-tmp}"
    case "${_suffix}" in
        *\$\$*) out_die "util_mktemp: refuse predictable \$\$ name template" ;;
    esac
    if [ -z "${EFFECTIVE_STORAGE_DIR}" ]; then
        EFFECTIVE_STORAGE_DIR=$(util_resolve_storage)
        export EFFECTIVE_STORAGE_DIR
    fi
    mktemp "${EFFECTIVE_STORAGE_DIR}/${APP_NAME}.${_suffix}.XXXXXX" \
        || mktemp
}
```

**Forbidden:**

```sh
# MUST NOT
tmp="/tmp/${APP_NAME}.$$"
tmp="${EFFECTIVE_STORAGE_DIR}/${APP_NAME}.$$"
```

### 2.5 Isolation (persistence)

1. Persistence is per-user because it sits under **`${HOME}`**.  
2. The leaf **MUST** include **`${APP_NAME}`**.  
3. **MUST** create the folder before returning it.

### 2.6 Wire and diagnostics

| Surface | Requirement |
|---------|-------------|
| `app_main` | Resolve once early: `EFFECTIVE_STORAGE_DIR=$(util_resolve_storage)`; `PERSISTENT_STORAGE_DIR=$(util_resolve_persistent_storage)`; export `EFFECTIVE_STORAGE_DIR`, `STORAGE_DIR`, `TMPDIR`, `PERSISTENT_STORAGE_DIR` |
| `app_about` JSON | Include `cache_preferred`, `cache_fallback`, live chosen cache `effective_storage`, and `persistent_storage` |
| `app_about` human | **Cache folder (preferred):** `/dev/shm/cache/cache-${APP_NAME}` · **Cache folder (fallback):** XDG `cache-${APP_NAME}` · **Persistence storage:** `${HOME}/.local/${APP_NAME}`. **MUST NOT** label cache lines Storage (effective)/(fallback) |
| Domain `backup` | Stage archives under the live **cache** root; clean up on exit |

Cache preferred/fallback about lines are the **policy** cache paths (helpers). Persistence about is the persistence policy path (created).

### 2.7 Staging rules for backups (cache family)

1. Create archives in a stage directory under `EFFECTIVE_STORAGE_DIR` (the live chosen **cache** root).  
2. Use restrictive modes appropriate for user data (prefer not world-readable when content may be sensitive).  
3. **MUST** remove staging artifacts via `trap` on success and failure after deposit attempt completes (or fails closed with path logged).  
4. Durable deposit path `/var/backup/...` is **not** the storage resolver’s job (privilege + domain law).

### 2.8 Implementation Notes (this project)

| Item | Live value |
|------|------------|
| **Product / binary** | `folder-backup` |
| **Cache resolver** | `util_resolve_storage` in `src/folder-backup` |
| **Preferred cache** | `/dev/shm/cache/cache-folder-backup` (`util_preferred_cache_dir`) |
| **Fallback cache** | `${XDG_CACHE_HOME}/cache-folder-backup` (`util_fallback_cache_dir`) |
| **Live chosen cache** | `EFFECTIVE_STORAGE_DIR` (JSON `effective_storage`) |
| **Persistence helper** | `util_resolve_persistent_storage` |
| **Persistence path** | `${HOME}/.local/folder-backup/` (JSON `persistent_storage`) |
| **Call sites** | `app_main`, `app_about`; cache used by domain staging |
| **Sudoers drafts / generate JSON** | Still under `${HOME}/.config/folder-backup/` (privilege/domain dest law). Persistence folder is created and shown; those dests are **not** silently re-homed in 1.2.0 |
| **Not used for** | Install binary; ram-drive project trees; `/var/backup` deposit |

### 2.9 Why This Requirement Exists (CIAO)

- **Caution:** Cache vs persistence vs install bin vs host deposit must not collapse.  
- **Intentional:** Storage means both families.  
- **Anti-fragile:** Missing `/dev/shm` still works for cache; persistence is under HOME.  
- **Principle 11 – Temps:** Cache cleanup, not museum copies of staging.  
- **Principle 12 – Backup/durability:** Persistence is the keep-files folder.

---

## 3. Design Principles (CIAO / CIAO-Lite)

- Volatile first, user cache last for **scratch**.  
- Persistence is a different folder from cache.  
- Isolation before convenience.  
- Create fail-closed in each resolver.  
- About labels name **Cache folder** and **Persistence storage**, not “storage effective.”

---

## 4. Protection Rule (Sacred)

**Future AI assistants, Grok, or maintainers MUST NOT**:

1. Remove `${APP_NAME}` from cache leaf names or the persistence leaf.  
2. Replace the cache fallback chain with a shared world-writable dump.  
3. Scatter hard-coded `/tmp/folder-backup` roots outside the cache resolver.  
4. Leave either resolver dead with no `app_main` / about wire while claiming storage product law.  
5. Echo a cache or persistence path without creating it.  
6. Stage durable deposits only in world-writable shared paths by design.  
7. Use predictable `$$` scratch names instead of `util_mktemp` / `mktemp` XXXXXX.  
8. Use `/dev/shm/folder-backup` or `/dev/shm/folder-backup-${USERNAME}` as the preferred cache.  
9. Label about cache lines **Storage (effective)** / **Storage (fallback)** instead of **Cache folder (preferred)** / **Cache folder (fallback)**.  
10. Treat storage as cache-only and omit persistence `${HOME}/.local/${APP_NAME}/`.  
11. Use `${HOME}/.local/bin` as persistence storage.  
12. Put backup staging in persistence by default.

**Violating this rule is a critical storage isolation regression.**

---

## 5. Acceptance criteria

| ID | Criterion |
|----|-----------|
| AC-1 | Exactly one authoritative cache resolver creates and returns the cache root |
| AC-2 | Cache priority matches §2.2 |
| AC-3 | `app_main` sets `EFFECTIVE_STORAGE_DIR` / `TMPDIR` and `PERSISTENT_STORAGE_DIR` early |
| AC-4 | Backup staging uses the cache resolver root and cleans up |
| AC-5 | Scratch files use `util_mktemp` / `mktemp` XXXXXX; no `$$` names |
| AC-6 | Human about prints **Cache folder (preferred)** `/dev/shm/cache/cache-folder-backup` and **Cache folder (fallback)**; JSON includes `cache_preferred` and `cache_fallback` |
| AC-7 | Persistence is `${HOME}/.local/folder-backup/`; helper creates it; about human **Persistence storage** and JSON `persistent_storage` |

---

## 6. Related requirements (peer keys only)

| Key | Relationship |
|-----|--------------|
| `requirement-project-folder` | Path classes |
| `requirement-domain-folder-backup` | Staging use; host deposit |
| `requirement-shell-cli-interface` | About fields |
| `requirement-shell-local-self-management` | Install binary `${HOME}/.local/bin` — not persistence |
| `docs/requirements/index.md` | Registry |

---

## Design-time verification

| TP family / ID | Suite | Status |
|----------------|-------|--------|
| **TP-CLI-06** | `tests/test_cli.sh` | have — JSON cache + `persistent_storage`; human Cache folder + Persistence storage |
| **TP-CLI-12** | same | have — live cache dir + persistence `${HOME}/.local/folder-backup` exists |

**Matrix:** `reviews/requirement-test-matrix.md`  
**Map:** `reviews/test-plan.md`

---

## 7. Status history

| Date | Status | Note |
|------|--------|------|
| 2026-08-03 | Active 1.0.0 | Storage resolve for folder-backup staging |
| 2026-08-15 | Active 1.0.0 | `util_mktemp` sample; forbid `$$` scratch names |
| 2026-08-30 | Active 1.1.0 | Preferred `/dev/shm/cache/cache-folder-backup`; about Cache folder (preferred)/(fallback) |
| 2026-08-30 | Active 1.2.0 | Storage = cache folder **and** persistence `${HOME}/.local/folder-backup/` |

---

**Last Updated**: 2026-08-30  
**Owner**: project maintainers  
**Alignment**: Registry `docs/requirements/index.md`; **CIAO** (https://github.com/cloudgen/ciao); CIAO-Lite (https://github.com/cloudgen/ciao-lite).
