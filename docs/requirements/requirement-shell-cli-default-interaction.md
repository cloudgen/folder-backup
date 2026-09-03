**file**: docs/requirements/requirement-shell-cli-default-interaction.md  
**Status**: Active (Version 1.4.0)  
**Area**: shell  
**Key**: `requirement-shell-cli-default-interaction`  
**Optional RQ-ID**: `RQ-SHELL-CLI-DEFAULT-INTERACTION`  
**Philosophy**: CIAO **v2.10.2** / CIAO-Lite (Caution • Intentional • Anti-fragile • Over-engineered / Over-protect)

## 1. Purpose

This requirement is the **product Single Source of Truth** for folder-backup’s **claimed default interactive main menu** and for **empty-argv** dispatcher meaning.

The product is **not** online-installable. There is **no** Active specialized zero-argument requirement. **Case 2** applies: on a real terminal, a bare `folder-backup` run opens the numbered work list; off-TTY that same bare run prints help. Named commands **`menu`** and **`main`** open the same list.

### 1.1 Human-facing

**In one sentence:** At a real terminal, type `folder-backup` with no extra words to see a numbered list of live work commands; in a pipe or script that same empty run prints help.

| Box | Meaning | Example |
|-----|---------|---------|
| You / this login | Open the list or pick a number | `folder-backup` then `1` |
| The other role | Scripts and CI must not hang on that list | `folder-backup </dev/null` → help |
| Not this file | Full command catalog, flags, unknown tokens | `requirement-shell-cli-interface` |

| Includes | Excludes |
|----------|----------|
| Numbered live work commands | `help` as a row |
| Line `command: what it does` | `install`, `uninstall`, `where-is-me`, `version`, `about` |
| Exit as **9** (four command rows) | `setup`, `menu`/`main` as a choice |
| Bare run on a real terminal | Install-ensure on empty argv |
| `menu` / `main` as the same list | Test-purpose: `print-sudoers`, `print-sudoers-install-script`, `generate-sudoer-request` |

| Surface | What you open | What for |
|---------|---------------|----------|
| `./src/folder-backup` | ship unit | live dispatch (empty argv, `menu`, `main`) |
| `folder-backup help` | command | listed verbs including lifecycle |
| `folder-backup` | empty argv | numbered list on a real terminal |

| You do… | What it means | What you type |
|---------|---------------|---------------|
| Open the list at a prompt | The program prints **folder-backup**(*live version*) then numbers 1–4 then 9 Exit. On a real terminal the name is bold, the version italic, and each “what it does” line after the colon is italic and light gray. `--json` on `menu`/`main` is ignored on a real terminal. | `folder-backup` or `folder-backup menu` |
| Pack a folder from the list | Choose backup, then give the folder (one field at a time) or read Next. | `1` then a source folder path |
| Run a bare invocation in CI | No prompt. Human help, or JSON help with `--json` and no command. | `folder-backup </dev/null` |

---

## 2. Core Rules / Requirements (Mandatory)

### 2.1 Claim and case

1. This product **claims** a default interactive main menu.  
2. **Case 2** applies: **no** Active specialized zero-argument requirement **and** the product is **not** online-installable.  
3. This file **owns empty argv**.  
4. Interactive empty argv (`TTY=1`, `$# -eq 0` at `app_main`) **MUST** open the numbered list (`app_main_menu`).  
5. Non-interactive empty argv (`TTY=0`, `$# -eq 0`) **MUST** be **help** (`app_help`). **MUST NOT** prompt. **MUST NOT** install-ensure.  
6. `--json` with **no command token** **MUST** be JSON help (flags-only; `--json` is non-interactive).  
7. Routed-verbs **`menu`** and **`main` MUST** call the same handler. They remain valid ways to open the list.  
8. `app_main` **MUST** route `menu` / `main` to `app_main_menu`.  
9. `requirement-shell-cli-zero-arguments` **MUST** stay **Withdrawn** while this case 2 claim is Active.

### 2.2 Mode check

Measure interactive capability **outside functions** (`TTY=1` only when stdin and stdout are terminals). Helpers consume `TTY` (`requirement-shell-interactive-vs-noninteractive`).

| Invocation | Mode | `--json` | MUST | MUST NOT |
|------------|------|----------|------|----------|
| `folder-backup` (no args) | Interactive (`TTY=1`) | *(none on empty argv)* | Draw the numbered list | Help; hang; install |
| `folder-backup` (no args) | Non-interactive (`TTY=0`) | *(none)* | **Help** (human) | Draw the menu; hang; install |
| Flags only, no command (e.g. `--json`) | Any | **Follow** | Help: human when JSON=0; JSON help when JSON=1 | Draw the menu |
| `folder-backup menu` or `main` | Interactive (`TTY=1`) | **Ignore** | Draw the numbered list | Treat as JSON help; hang |
| same | Non-interactive (`TTY=0`) | **Follow** | **Help**: human when JSON=0; JSON help when JSON=1 | Draw the menu; hang; silent return |

`--quiet` off-TTY is still the help path (do not swallow that help). Reuse `app_help` — **MUST NOT** invent a second JSON help catalog.

### 2.3 Numbered list (when the case says menu)

0. **Look (mandatory):** the numbered list **MUST** use the default CLI main menu style. Header **MUST** print live `folder-backup(VERSION)` (no space; same Config scalars as `version`) then the board title. On a TTY the name is **bold** and the version *italic*. Each numbered `explain` **MUST** be *italic* and light gray on a TTY (SGR 3 + 37). Number and command name stay unstyled. Off-TTY / JSON: **plain** — **MUST NOT** emit CSI. Typical helpers: `util_app_ident` then `out_menu_choice`. **MUST NOT** a bare `folder-backup` on that header. **MUST NOT** print `explain` unstyled on a TTY.  
1. Print a **numbered list** at the start of the interactive menu path.  
2. Each command row is one live **operational** command that is **not** excluded below, numbered **1 … N** in kept-list order.  
3. Printed line **MUST** be the kept-list **human-readable** value: **`{{short-descript}}: {{explain}}`** (short-descript = the command token).  
4. **MUST NOT** list `help`, `menu`, `main`, gap/forbidden names, **diagnostics** (`version`, `about`), **self-managed / install-setup** tokens (`install`, `uninstall`, `where-is-me`, `setup`), or **test-purpose** verbs. On this product the test-purpose verbs **MUST** be `print-sudoers`, `print-sudoers-install-script`, and `generate-sudoer-request`. Those stay on `help`, listed apart from operational work.  
5. Accept a **number** or the **verb token**. Extra operands: prompt **one field at a time** on TTY, or print `Next: folder-backup <verb> …` and return.  
5b. **Do not capture `read`:** the choice **MUST** be read in the **current shell**. Typical: `prompt_line "Choice"` then `_pick="${_prompt_line}"`. **MUST NOT** `_pick=$(prompt_line …)` / `_pick=$(prompt_ask …)` / `$()` / backticks of **any** function whose body contains `read` (do-not-capture-read / **PP-A-22**). stderr+$() is **not** a license.  
6. Last extra row is **Exit** (not a command). For this product **N = 4**, so Exit **MUST** be **9**. Unused integers 5–8 are omitted. Exit row is not a command explain; gray-italic is not required on `Exit`.  
7. Exit number, `exit`, or `quit` returns 0 with no further prompt.  
8. Typical handler: `app_main_menu`.

### 2.4 Implementation Notes (this product)

| Item | Value |
|------|--------|
| **Product** | `folder-backup` |
| **Claimed** | yes |
| **Case** | **2** (no Active zero-arg REQ; local-only) |
| **Empty argv owner** | **this file** (TTY menu; off-TTY help) |
| **Menu verbs** | empty argv on TTY; `menu` (preferred named); `main` alias |
| **Handler** | `app_main_menu` |
| **Ship unit** | Implemented — `app_main` empty argv and `menu` / `main` call `app_main_menu` |
| **Kept list** | `reviews/cli-routed-verb-table.md` |
| **Look** | Default CLI main menu style — header `folder-backup(VERSION)`; TTY italic + light-gray explain; `out_menu_choice` / `util_app_ident` |
| **Choice read** | Current-shell `prompt_line` → `_prompt_line` (not `$()`) |
| **N** | 4 |
| **Exit** | 9 |
| **Test-purpose (this product)** | `print-sudoers`, `print-sudoers-install-script`, `generate-sudoer-request` |
| **Withdrawn peer** | `requirement-shell-cli-zero-arguments` |

**Normative menu draft** (operational only; self-managed, diagnostics, and test-purpose omitted). README / this fence transcribe markdown emphasis; the live TTY uses SGR, never paste CSI here:

```text
[INFO] **folder-backup**(*VERSION*) — numbered list of live work commands
1. backup: Pack a named folder into a dated gzip archive under /var/backup/folder-backup
2. restore: Put an archive back onto the hard-disk projects tree
3. remove-project-sudoers: Remove the local grant draft only
4. submit-sudoer-request: Hand the JSON grant to the approval queue
9. Exit
```

**Invocation samples (CI-M1a):**

```text
folder-backup
folder-backup menu
folder-backup main
folder-backup menu --json
```

On a real terminal the first three **MUST** show the list. `folder-backup menu --json` on a real terminal **MUST** still show the list. Off-TTY, `folder-backup` and `folder-backup menu` **MUST** call help; `folder-backup --json` and `folder-backup menu --json` **MUST** call JSON help.

### 2.5 Why This Requirement Exists (CIAO)

- **Principle 2 – Intentional**: Empty argv has one owner (this file, case 2). The list also has named commands.  
- **Principle 1 – Caution**: Scripts do not hang; empty argv never install-ensure.  
- **Principle 16 – Interactive vs non-interactive**: TTY vs pipe is explicit.  
- **Principle 10 – Least privilege**: Install/uninstall, version/about, and test-purpose grant-emit verbs are not on the work list.

---

## 3. Design Principles (CIAO / CIAO-Lite)

- **Caution:** Do not hang off-TTY; do not steal empty argv for install.  
- **Intentional:** Case 2; labels from the kept list.  
- **Anti-fragile:** `menu` / `main` may open the same list as a bare TTY run; Exit 9 when N=4.  
- **Over-protect:** Self-managed, diagnostics, and test-purpose verbs stay off the list even if they are live.

---

## 4. Protection Rule (Sacred)

**Future AI assistants, Grok, or maintainers MUST NOT**:

1. Restore Type N always-help on empty argv while this case 2 claim is Active (do not reactivate `requirement-shell-cli-zero-arguments`).  
2. Attach empty argv to install-ensure (Type O).  
3. Invent menu labels instead of `command: what it does` from the kept list.  
4. Put `help`, `install`, `uninstall`, `where-is-me`, `version`, `about`, `setup`, `menu`, `main`, or a test-purpose verb (`print-sudoers`, `print-sudoers-install-script`, `generate-sudoer-request`) on the numbered list.  
5. Number Exit as 5 when N=4 (Exit **MUST** be 9).  
6. Draw the menu in non-interactive mode (including off-TTY empty argv).  
7. Treat interactive `folder-backup menu --json` as JSON help.  
8. Drop `menu`/`main` routing after attaching the list to empty argv.  
9. Auto-write `/etc` from a menu choice (print/submit stay Type 0 drafts).  
10. Claim the ship unit lacks the TTY empty-argv menu while `app_main` routes empty argv to `app_main_menu`.  
11. Capture the menu choice (or extra field) with `$()` / backticks of a `read` helper, or “fix” a freeze by stderr+$() (**PP-A-22** / do-not-capture-read).  
12. Print the main-menu header as a bare `folder-backup` without live `VERSION`, or unstyled on a TTY.  
13. Draw the numbered list off the default CLI main menu style — **MUST NOT** print numbered-choice `explain` unstyled on a TTY (it **MUST** be *italic* and light gray). **MUST NOT** emit CSI off-TTY.

**Violating this rule is a critical dispatcher / hang / honesty / look regression.**

---

## 5. Acceptance criteria

| ID | Criterion |
|----|-----------|
| AC-1 | Non-interactive empty argv is help (not install, not the numbered list) |
| AC-2 | Case 2 recorded; this file owns empty argv; `menu` / `main` named and routed |
| AC-3 | Interactive empty argv **and** interactive `menu` draw the four-row list + Exit 9 |
| AC-4 | Interactive `menu --json` still draws the list |
| AC-5 | Non-interactive `menu` is help; `--json` is JSON help |
| AC-6 | Numbered choices omit help, install, uninstall, where-is-me, version, about, setup, menu, main, and test-purpose (`print-sudoers`, `print-sudoers-install-script`, `generate-sudoer-request`) |
| AC-7 | Labels match kept-list human-readable `verb: explain` |
| AC-8 | TTY header is live `folder-backup(VERSION)` with bold name and italic version; numbered `explain` is italic and light gray; number and verb unstyled; no CSI off-TTY |
| AC-9 | Menu choice is current-shell `prompt_line` / `_prompt_line`; **MUST NOT** `$()` a `read` helper |

---

## 6. Related requirements (peer keys only)

| Key | Relationship |
|-----|--------------|
| `requirement-shell-cli-zero-arguments` | **Withdrawn** predecessor (Type N always-help) |
| `requirement-shell-cli-interface` | Dual mention: empty argv row + `menu` / `main` on the command table |
| `requirement-shell-interactive-vs-noninteractive` | `TTY`; no hang |
| `requirement-shell-output-requirements` | `out_*`; reuse `app_help` |
| `requirement-shell-local-self-management` | install/uninstall/where-is-me stay on help, not this list |
| `docs/requirements/index.md` | Registry |

---

## Design-time verification

| TP family / ID | Suite | Status |
|----------------|-------|--------|
| **TP-CLI-07** | `tests/test_cli.sh` | **have** — off-TTY empty argv is help, not install (AC-1) |
| **TP-CLI-13** | `tests/test_cli.sh` | **have** — interactive empty argv **and** `menu` print the four labels + `9. Exit` (AC-3) |
| **TP-CLI-14** | same | **have** — interactive `menu --json` still prints the list (AC-4) |
| **TP-CLI-15** | same | **have** — non-interactive empty argv and `menu` are help; `--json` JSON help (AC-5) |
| **TP-CLI-16** | same | **have** — numbered list omits help/install/uninstall/where-is-me/version/about/test-purpose/menu (AC-6) |
| **TP-CLI-18** | same | **have** — default CLI main menu style (AC-8); product alias of portable **TP-CLI-17** (this product already assigned **TP-CLI-17** to help heading split) |

**Matrix:** `reviews/requirement-test-matrix.md`  
**Map:** `reviews/test-plan.md`

## 7. Status history

| Date | Status | Note |
|------|--------|------|
| 2026-08-23 | Active 1.0.0 | Case 3 claimed; menu/main Gap; nine-row list; Exit 99 |
| 2026-08-23 | Active 1.1.0 | Colon labels; exclude version/about; test-purpose print-sudoers / generate-sudoer-request / print-sudoers-install-script; N=4 Exit 9 |
| 2026-08-23 | Active 1.2.0 | Ship unit routes `menu` / `main`; Gap closed; empty argv stayed help (case 3) |
| 2026-08-28 | Active 1.3.0 | **Case 2**: TTY empty argv = numbered list; off-TTY empty argv = help; zero-arguments Withdrawn |
| 2026-09-03 | Active 1.4.0 | Default CLI main menu style (header nametag + TTY gray italic explain); do-not-capture-read MUST; **TP-CLI-18** |

---

**Last Updated**: 2026-09-03  
**Owner**: project maintainers  
**Alignment**: Registry `docs/requirements/index.md`; **CIAO** (https://github.com/cloudgen/ciao); CIAO-Lite (https://github.com/cloudgen/ciao-lite).
