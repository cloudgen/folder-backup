**file**: docs/requirements/requirement-shell-cli-sudoers-submenu.md  
**Status**: Active (Version 1.0.0)  
**Area**: shell  
**Key**: `requirement-shell-cli-sudoers-submenu`  
**Optional RQ-ID**: `RQ-SHELL-CLI-SUDOERS-SUBMENU`  
**Philosophy**: CIAO **v2.10.2** / CIAO-Lite (Caution • Intentional • Anti-fragile • Over-engineered / Over-protect)

## 1. Purpose

This requirement is the **product Single Source of Truth** for folder-backup’s **sudoers submenu**: the family row on the numbered start list and the second board of **grant/draft setup** verbs. The start list itself stays on `requirement-shell-cli-default-interaction`. JSON grant **body** stays on `requirement-sudoer-json-file`. Emit / submit **workflow** stays on `requirement-three-layer-privilege-model`.

This file exists so the submenu is a **named, transferable** product law — not a buried paragraph on the start-list requirement.

### 1.1 Human-facing

**In one sentence:** On a real terminal, pick **sudoers** on the numbered start list to open grant and draft setup; JSON grant, inbound submit, sudoers text, admin install script, and remove-draft are also ordinary commands; typing `sudoers` as a command is unknown.

| Box | Meaning | Example |
|-----|---------|---------|
| You / this login | Open the second board or type a setup verb | `folder-backup` then `3` then `1` |
| The other role | Scripts type the five names; they never hang on the second board | `folder-backup generate-sudoer-request` |
| Not this file | Daily backup/restore rows; empty argv; JSON grant body | `requirement-shell-cli-default-interaction` · `requirement-sudoer-json-file` |

| Includes | Excludes |
|----------|----------|
| Family row **sudoers** (menu-only) | `sudoers` as a typed command |
| Five live setup verbs on the second board | Those five as **main**-list rows |
| **Back 8** / **Exit 9** | Install / version / about on the submenu |
| Same nametag as the start list | A hang in a pipe |

| Surface | What you open | What for |
|---------|---------------|----------|
| `./src/folder-backup` | ship unit | live menu + live setup verbs |
| `folder-backup help` | command | listed setup verbs |

| You do… | What it means | What you type |
|---------|---------------|---------------|
| Open grant/drafts | Second board of five setup kinds | `folder-backup` then `3` then `1` |
| Write a JSON grant | Same handler as submenu **1** | `folder-backup generate-sudoer-request` |
| Queue inbound | Same handler as submenu **2** | `folder-backup submit-sudoer-request` |
| Emit sudoers text | Same handler as submenu **3** | `folder-backup print-sudoers` |
| Write admin script | Same handler as submenu **4** | `folder-backup print-sudoers-install-script` |
| Remove local draft | Same handler as submenu **5** | `folder-backup remove-project-sudoers` |
| Type the family name | Unknown — not a command | `folder-backup sudoers` |

---

## 2. Core Rules / Requirements (Mandatory)

### 2.1 Claim

1. This product **claims** a sudoers submenu.  
2. A claimed start list is required (`requirement-shell-cli-default-interaction`).  
3. **MUST NOT** hang off-TTY (submenu exists only on the interactive menu path).

### 2.2 Family row

1. Main-list family token **MUST** be `sudoers`. Explain **MUST** be `Grant and drafts`. Main-list number **MUST** be **3**.  
2. **`sudoers` is not a live CLI command.** Choosing **3** or typing `sudoers` at the pick prompt **MUST** open the submenu. `folder-backup sudoers` **MUST** remain unknown.  
3. **MUST NOT** list the five setup verbs on the **main** list.

### 2.3 Submenu — types of sudoer-file setup

Choosing main **3** / `sudoers` **MUST** print a second numbered list. Submenu header **MUST** use the same `folder-backup(VERSION)` nametag. Typical title: `sudoers (grant and drafts)`. Explain text **MUST** follow the same default CLI main menu style as the main list.

The five grouped verbs are the **different types of sudoer-file setup**. Each **MUST** remain a live CLI verb. The submenu is a picker, not a second dispatcher.

| # | Command | Label | Setup kind |
|---|---------|-------|------------|
| 1 | `generate-sudoer-request` | `generate-sudoer-request: Write a local JSON grant you can read without sudo` | JSON grant |
| 2 | `submit-sudoer-request` | `submit-sudoer-request: Hand the JSON grant to the approval queue` | Inbound queue |
| 3 | `print-sudoers` | `print-sudoers: Write a grant file an admin can install` | sudoers text |
| 4 | `print-sudoers-install-script` | `print-sudoers-install-script: Write an admin script to install or remove the grant` | Admin script |
| 5 | `remove-project-sudoers` | `remove-project-sudoers: Remove the local grant draft only` | Remove draft |
| **8** | **Back** | return to the main list (not a command) | — |
| **9** | **Exit** | leave the menu | — |

Submenu command rows **N = 5**. Exit **MUST** be **9**. **Back MUST** be **8**. Unused **6** and **7** are omitted.

- **8** / `back` / `Back` returns to the main list (does not run a handler).  
- **9** / `exit` / `quit` returns 0 from `menu` (same as main Exit).  
- A listed number or verb runs that handler, then returns 0 from `menu` (one command, then done).  
- All five grouped verbs **MUST** appear here. **MUST NOT** put install/version/about/`help`/`menu`/`main` on this list.  
- Typing a submenu verb at the **main** pick prompt **MAY** run that handler (shortcut).  
- Choice **MUST** be read in the **current shell**. **MUST NOT** `$()` a `read` helper.

### 2.4 Implementation Notes (this product)

| Item | Value |
|------|--------|
| **Product** | `folder-backup` |
| **Claimed** | yes |
| **Family token** | `sudoers` |
| **Family number** | **3** |
| **Handler family** | `app_main_menu_print_sudoers` · `app_main_menu_run_sudoers_pick` · `app_main_menu_sudoers_loop` |
| **Members** | `generate-sudoer-request` · `submit-sudoer-request` · `print-sudoers` · `print-sudoers-install-script` · `remove-project-sudoers` |
| **Choice read** | Current-shell `prompt_line` → `_prompt_line` |
| **Honesty** | **Implemented** |

**Invocation samples:**

```text
folder-backup generate-sudoer-request
folder-backup submit-sudoer-request
folder-backup print-sudoers
folder-backup print-sudoers-install-script
folder-backup remove-project-sudoers
```

### 2.5 Why This Requirement Exists (CIAO)

- **Principle 2 – Intentional**: The second board is a named law, not a comment on the start list.  
- **Principle 1 – Caution**: `sudoers` is never dispatched; scripts do not hang.  
- **Principle 21 – Dual policies**: Portable five-kind catalog; this product fills numbers and labels.  
- **Principle 10 – Least privilege**: Print/submit stay Type 0 drafts; the menu never writes `/etc`.

---

## 3. Design Principles (CIAO / CIAO-Lite)

- **Caution:** Family token unknown; no hang off-TTY.  
- **Intentional:** Five setup kinds are explicit.  
- **Anti-fragile:** Typed member verbs still run without the menu.  
- **Over-protect:** Members stay off the main list; Exit is **9**, Back is **8**.

---

## 4. Protection Rule (Sacred)

**Future AI assistants, Grok, or maintainers MUST NOT**:

1. Wire `sudoers` as a live `app_main` command.  
2. Drop a grouped setup verb from the submenu while it remains live.  
3. Put the five setup verbs on the **main** list.  
4. Number submenu Exit as 6 or Back as anything other than **8**.  
5. Hang off-TTY on the submenu path.  
6. Capture the submenu choice with `$()` of a `read` helper.  
7. Auto-write `/etc` from a submenu choice.  
8. Collapse this file back into `requirement-shell-cli-default-interaction` as the only owner.

**Violating this rule is a critical dispatcher / hang / transferability regression.**

---

## 5. Acceptance criteria

| ID | Criterion |
|----|-----------|
| AC-1 | Main list family row is **3** `sudoers: Grant and drafts` |
| AC-2 | Choosing **3** / `sudoers` at the pick prompt opens the submenu with the five setup verbs, Back 8, Exit 9 |
| AC-3 | `folder-backup sudoers` is unknown |
| AC-4 | The five names remain live CLI verbs |
| AC-5 | The five names are **not** main-list rows |
| AC-6 | Submenu nametag is live `folder-backup(VERSION)` |
| AC-7 | Submenu choice is current-shell `prompt_line` / `_prompt_line` |

---

## 6. Related requirements (peer keys only)

| Key | Relationship |
|-----|--------------|
| `requirement-shell-cli-default-interaction` | Start list; **points** here for the family row |
| `requirement-shell-cli-interface` | Dual mention: five setup verbs remain routed |
| `requirement-domain-folder-backup` | Domain catalog of those verbs; help apart |
| `requirement-sudoer-json-file` | JSON grant **body** |
| `requirement-three-layer-privilege-model` | Emit / submit **workflow** |
| `requirement-shell-interactive-vs-noninteractive` | `TTY`; no hang |
| `requirement-shell-output-requirements` | `out_menu_choice` |
| `docs/requirements/index.md` | Registry |

---

## Design-time verification

| TP family / ID | Suite | Status |
|----------------|-------|--------|
| **TP-CLI-13** | `tests/test_cli.sh` | **have** — family row, submenu five verbs, Back 8 / Exit 9, `sudoers` unknown, members live (AC-1–AC-4) |
| **TP-CLI-16** | same | **have** — members off the **main** list (AC-5) |
| **TP-CLI-18** | same | **have** — submenu nametag (AC-6); product alias of portable **TP-CLI-17** |

**Matrix:** `reviews/requirement-test-matrix.md`  
**Map:** `reviews/test-plan.md`

## 7. Status history

| Date | Status | Note |
|------|--------|------|
| 2026-09-03 | Active 1.0.0 | Dedicated sudoers-submenu SSOT (extracted from default-interaction 1.5.0); five setup kinds stay live CLI verbs |

---

**Last Updated**: 2026-09-03  
**Owner**: project maintainers  
**Alignment**: Registry `docs/requirements/index.md`; **CIAO** (https://github.com/cloudgen/ciao); CIAO-Lite (https://github.com/cloudgen/ciao-lite).
