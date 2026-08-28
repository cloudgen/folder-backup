**file**: docs/requirements/requirement-shell-cli-zero-arguments.md  
**Status**: Withdrawn (Version 1.1.0 — superseded)  
**Area**: shell  
**Key**: `requirement-shell-cli-zero-arguments`  
**Philosophy**: CIAO **v2.10.2** / CIAO-Lite (Caution • Intentional • Anti-fragile • Over-engineered / Over-protect)

## 1. Purpose

This file is **withdrawn**. It no longer owns empty-argv dispatcher behavior.

From 2026-08-03 through 2026-08-28 this file declared **Type N**: a bare `folder-backup` run always printed **help** (local-only; never install-ensure). That always-help rule is **superseded**. Empty argv is now owned by `requirement-shell-cli-default-interaction` (**case 2**): on a real terminal a bare run opens the numbered work list; in a script or pipe it still prints help.

This file remains on disk so agents do **not** recreate an Active Type N always-help owner that would steal the numbered list back off empty argv (**case 3**).

### 1.1 Human-facing

**In one sentence:** This page is retired. Typing only `folder-backup` at a real terminal now opens the numbered work list; a pipe or script still gets help.

| Box | Meaning | Example |
|-----|---------|---------|
| You / this login | Bare run at a prompt is the work list | `folder-backup` then `1` |
| The other role | Scripts must not hang | `folder-backup </dev/null` → help |
| Not this file | Numbered list membership and TTY rules | `requirement-shell-cli-default-interaction` |

| Includes | Excludes |
|----------|----------|
| Historical Type N always-help (retired) | Live empty-argv meaning |
| Fence: empty argv **MUST NOT** install-ensure | Binding empty argv to help on a real terminal |
| Registry row **Withdrawn** | Recreating this key as Active case-3 owner |

| Surface | What you open | What for |
|---------|---------------|----------|
| `docs/requirements/index.md` | registry | Status **Withdrawn** |
| `requirement-shell-cli-default-interaction` | live law | Bare run / `menu` / `main` |
| `folder-backup help` | command | Full catalog still listed |

| You do… | What it means | What you type |
|---------|---------------|---------------|
| Open the work list | No command word. Real terminal. | `folder-backup` |
| See the full catalog | Help is still a named command. | `folder-backup help` |
| Run in CI | No prompt. Help, not the list. | `folder-backup </dev/null` |

---

## 2. Core Rules (Mandatory)

### 2.1 Withdrawal

1. Status **MUST** stay **Withdrawn** while `requirement-shell-cli-default-interaction` is Active **case 2**.  
2. This key **MUST NOT** be treated as a live zero-argument owner. Live empty-argv law is `requirement-shell-cli-default-interaction`.  
3. Agents **MUST NOT** reactivate Type N always-help as empty-argv meaning without an explicit user order to leave **case 2**.

### 2.2 Residual fence (still true)

1. The product remains **local-only**. Empty argv **MUST NOT** become install-ensure (historical Type O).  
2. Explicit `folder-backup install` remains the only first-time local install path.  
3. Script entry **MUST** always call `app_main "$@"` (no basename product-name gate).

### 2.3 Implementation Notes (this project)

| Item | Value |
|------|--------|
| **Product** | `folder-backup` |
| **Status** | **Withdrawn** |
| **Successor** | `requirement-shell-cli-default-interaction` (case 2) |
| **Retired meaning** | Type N: empty argv always help |
| **Kept fence** | Empty argv is never install-ensure |

### 2.4 Why This Requirement Exists (CIAO)

- **Principle 2 – Intentional**: Withdrawal is explicit so case 2 can own a bare run.  
- **Principle 1 – Caution**: Type O surprise-install stays forbidden.  
- **Principle 16 – Interactive**: Off-TTY still must not hang.

---

## 3. Design Principles (CIAO / CIAO-Lite)

- **Caution**: Do not revive always-help as a second empty-argv owner.  
- **Intentional**: Withdrawn, not deleted, so the basename is not re-invented as case 3.  
- **Anti-fragile**: Help and install remain named commands.  
- **Over-protect**: Type O empty-argv install-ensure stays absent.

---

## 4. Protection Rule (Sacred)

**Future AI assistants, Grok, or maintainers MUST NOT**:

1. Reactivate this file as an Active Type N always-help owner while case 2 is claimed.  
2. Change empty argv to install-ensure while the product remains local-only.  
3. Delete this file solely to hide the withdrawal (registry row **MUST** stay **Withdrawn**).  
4. Treat this basename as live dispatcher law.

**Violating this rule is a critical dispatcher / case-split regression.**

---

## 5. Acceptance criteria

| ID | Criterion |
|----|-----------|
| AC-1 | Registry status is **Withdrawn** |
| AC-2 | Live empty-argv behavior is defined on `requirement-shell-cli-default-interaction` |
| AC-3 | Empty argv does not install-ensure (TP-CLI-07 off-TTY still not install) |

---

## 6. Related requirements (peer keys only)

| Key | Relationship |
|-----|--------------|
| `requirement-shell-cli-default-interaction` | **Successor** — case 2 empty argv |
| `requirement-shell-cli-interface` | Dispatcher table; flags-only still help |
| `requirement-shell-local-self-management` | Explicit `install` |
| `requirement-bootstrap-chain` | Type O empty argv remains absent |
| `docs/requirements/index.md` | Registry |

---

## Design-time verification

| TP family / ID | Suite | Status |
|----------------|-------|--------|
| **TP-CLI-07** | `tests/test_cli.sh` | have — off-TTY empty argv is help, not install (now owned with default-interaction) |

**Matrix:** `reviews/requirement-test-matrix.md`  
**Map:** `reviews/test-plan.md`

## 7. Status history

| Date | Status | Note |
|------|--------|------|
| 2026-08-03 | Active 1.0.0 | Type N for local-only folder-backup |
| 2026-08-28 | **Withdrawn** 1.1.0 | Superseded by default-interaction **case 2** (TTY empty argv = numbered list) |

---

**Last Updated**: 2026-08-28  
**Owner**: project maintainers  
**Alignment**: Registry `docs/requirements/index.md`; **CIAO** (https://github.com/cloudgen/ciao); CIAO-Lite (https://github.com/cloudgen/ciao-lite).
