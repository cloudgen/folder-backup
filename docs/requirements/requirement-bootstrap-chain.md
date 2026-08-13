**file**: docs/requirements/requirement-bootstrap-chain.md  
**Status**: Active (Version 2.0.0)  
**Area**: architecture  
**Key**: `requirement-bootstrap-chain`  
**Philosophy**: CIAO **v2.10.2** / CIAO-Lite (Caution • Intentional • Anti-fragile • Over-engineered / Over-protect)

## 1. Purpose

Declare the **bootstrap chain** for this product: ordered lineage, direction, architecture inheritance, and the **domain extend** of folder-archive backup onto the Type 0 parent.

**Direction is sacred:** ancestor → descendant only. Never reverse-copy this product onto the bootstrap parent.

---

## 2. Core Rules (Mandatory)

### 2.1 Direction

1. Every edge **MUST** be **ancestor → descendant** only.  
2. Plans **MUST NOT** copy this product’s ship unit onto the bootstrap parent to “share fixes.”  
3. Detected reverse-copy **MUST** be treated as critical pollution (restore parent; rebuild this product).

### 2.2 Chain declaration (this product)

| Field | Value |
|-------|--------|
| **Root / hop 0 (A)** | `cli-template` — Type 0 local-only template (sibling workspace `/home/leolio/prjs/cli-template`) |
| **Leaf / hop 1 (B)** | `folder-backup` — this workspace product |
| **Immediate origin of leaf** | `cli-template` |
| **Specialize mode** | **Domain extend** (folder archive backup + sudoers-elevated deposit). Online / Type O already **absent on A** — not a second online trim. |
| **A ship unit** | Sibling: `/home/leolio/prjs/cli-template/src/cli-template` (not in this tree) |
| **B ship unit** | `src/folder-backup` |
| **A channel ownership** | **None** — local-only install |
| **B channel ownership** | **None** — local-only install (inherited) |
| **A domain** | none (Type 0 lifecycle template) |
| **B domain** | folder tar.gz backup + sudoers-elevated deposit (see `requirement-domain-folder-backup`) |
| **Retired names (not live hops)** | `selfmanaged` — historical 2026-08-03 origin declaration. **Not** the live parent. |

### 2.3 Architecture inheritance (B from A)

B **MUST** inherit A’s structural contracts:

| Layer | Inherit / extend |
|-------|------------------|
| Runtime | POSIX `/bin/sh`, `set -u`, explicit errors |
| Output SSOT | `out_*` family |
| Modular prefixes | `out_`, `inst_`, `util_`, `app_`, `path_`, `prompt_`; domain uses dedicated `fb_` prefix |
| Entry / dispatch | Single `app_main`; always call `app_main "$@"` at end |
| Global flags | `--quiet` / `--json` / `--debug` / `--force` / `--global` |
| Integrity companion | **Absent** (A has none) |
| Online lifecycle | **Absent** (`version-check`, `self-update`, `self-uninstall`, Type O, `SCRIPT_URL` UX) |
| Local lifecycle | **Keep** local `install` / `uninstall` / `where-is-me` |
| Empty argv | **Keep** Type N help |
| Domain | **Add** on B only |

### 2.4 Keep / extend matrix (normative for this product)

| Surface | Decision | Notes for folder-backup |
|---------|----------|-------------------------|
| `out_*` output SSOT | **Keep** | Surgical only |
| Modular single-file design | **Keep** | Ship unit under `src/` |
| Global flags + `app_main` | **Keep** | Same contracts; domain flags added on B |
| Storage resolve | **Keep / adapt** | Staging for tar.gz |
| Idempotency / interactive modes | **Keep / retarget** | Domain confirm paths stay fail-closed |
| Online channel (`SCRIPT_URL`, `REPO_*` as channel) | **Absent (inherited)** | Not install source; not help/about product UX |
| Type O empty argv | **Absent (inherited)** | Empty argv = Type N help |
| Remote `version-check` / `self-update` / `self-uninstall` | **Absent (inherited)** | Unknown commands |
| Companion `.sha256` product law | **Absent (inherited)** | No channel integrity package |
| Local `install` / `uninstall` / `where-is-me` | **Keep** | Local self-managed package |
| Domain backup + sudoers fragment | **Add** | Domain SSOT |
| Domain / out Protection Zones | **Keep spirit** | Do not “simplify away” defensive layers for style |

### 2.5 Identity retarget (B only)

| Concern | B value |
|---------|---------|
| `APP_NAME` | `folder-backup` |
| `VERSION` | `1.6.1` (product version SSOT in ship unit) |
| Primary install story | Local copy from running ship unit → `${USER_BIN}` (default `~/.local/bin`) |
| README one-liner | **No** `curl \| sh` channel claim |

### 2.6 Implementation Notes (this project)

| Item | Value |
|------|--------|
| **A (bootstrap)** | `cli-template` at `/home/leolio/prjs/cli-template` (do not reverse-copy) |
| **B (this product)** | folder-backup |
| **Specialize intent** | Same Type 0 local architecture as A + folder-backup domain |
| **Install mode** | **local-only** (not dual-mode) |
| **Domain after specialize** | Active `requirement-domain-folder-backup` |
| **Historical origin** | 2026-08-03 named `selfmanaged` with online trim. Retired 2026-08-13. |

### 2.7 Why This Requirement Exists (CIAO)

- **Principle 2 – Intentional**: Lineage and domain extend are explicit.  
- **Principle 1 – Caution**: No half-live channel; A already has none.  
- **Principle 18 / Over-protect**: Reverse-copy is forbidden pollution.  
- **Principle 21 – Dual policies**: Complete B law; portable cores elsewhere.

---

## 3. Design Principles (CIAO / CIAO-Lite)

- **Caution**: Matrix before delete; verify no half-live install.  
- **Intentional**: Explicit keep/extend; registry names absences.  
- **Anti-fragile**: Keep battle-tested `out_*` / modular patterns from A.  
- **Over-protect**: Never reverse-copy; no silent channel reintro.

---

## 4. Protection Rule (Sacred)

**Future AI assistants, Grok, or maintainers MUST NOT**:

1. Reverse-copy `folder-backup` onto `cli-template` or treat reverse as “cleanup.”  
2. Name `selfmanaged` as this product’s live origin without updating this file.  
3. Claim bootstrap trim of online while reintroducing Type O install-ensure or `SCRIPT_URL` as product UX.  
4. Leave dual-mode online+local install without an explicit dual-mode matrix and user order.  
5. Drop `out_*` / modular Protection Zones as “part of specialize.”  
6. Invent a second bootstrap origin that contradicts this declaration without updating this file.

**Violating this rule is a critical bootstrap-direction regression.**

---

## 5. Acceptance criteria

| ID | Criterion |
|----|-----------|
| AC-1 | Hop table names A=cli-template, B=folder-backup, direction A→B |
| AC-2 | Keep/extend matrix matches Active registry (online package absent; domain present) |
| AC-3 | B identity retarget complete (`APP_NAME`, `VERSION`, local install) |
| AC-4 | Domain SSOT present for backup surface |
| AC-5 | `selfmanaged` is retired history, not a live hop |

---

## 6. Related requirements (peer keys only)

| Key | Relationship |
|-----|--------------|
| `requirement-class-software-dev` | Class gate |
| `requirement-shell-local-self-management` | Local lifecycle inherited from A |
| `requirement-shell-cli-zero-arguments` | Type N empty argv inherited from A |
| `requirement-domain-folder-backup` | Domain extend |
| `docs/requirements/index.md` | Registry |

---

## Design-time verification

| TP family / ID | Suite | Status | Note |
|----------------|-------|--------|------|
| **TP-CLI-04,10** | `tests/test_cli.sh` | have | online verbs absent |
| **TP-CLI-07** | `tests/test_cli.sh` | have | Type N empty argv |
| **TP-FOLDER-BACKUP-*** | `tests/test_domain_folder_backup.sh` | have | domain extend |

**Matrix:** `reviews/requirement-test-matrix.md`  
**Map:** `reviews/test-plan.md`

## 7. Status history

| Date | Status | Note |
|------|--------|------|
| 2026-08-03 | Active 1.0.0 | Declared A=selfmanaged → B=folder-backup (trim online) |
| 2026-08-13 | Active 2.0.0 | Re-specialize: A=cli-template → B=folder-backup (domain extend). selfmanaged retired. |

---

**Last Updated**: 2026-08-13  
**Owner**: project maintainers  
**Alignment**: Registry `docs/requirements/index.md`; **CIAO** (https://github.com/cloudgen/ciao); CIAO-Lite (https://github.com/cloudgen/ciao-lite).
