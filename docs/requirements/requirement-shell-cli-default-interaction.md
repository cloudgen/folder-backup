**file**: docs/requirements/requirement-shell-cli-default-interaction.md  
**Status**: Active (Version 1.7.0)  
**Area**: shell  
**Key**: `requirement-shell-cli-default-interaction`  
**Optional RQ-ID**: `RQ-SHELL-CLI-DEFAULT-INTERACTION`  
**Philosophy**: CIAO **v2.10.2** / CIAO-Lite (Caution • Intentional • Anti-fragile • Over-engineered / Over-protect)

## 1. Purpose

This requirement is the **product Single Source of Truth** for folder-backup’s **claimed default interactive main menu** and for **empty-argv** dispatcher meaning.

Channel verbs exist and are explicit (`self-install`, `version-check`, `self-update`, `self-uninstall`, `self-management`). They do not own empty argv. There is **no** Active specialized zero-argument requirement. **Case 2** applies by owner order: on a real terminal, a bare `folder-backup` run opens the numbered boards; off-TTY that same bare run prints help. Named commands **`menu`** and **`main`** open the same boards. The sudoers **board body** (which rows, which numbers stay reserved) is owned by **`requirement-shell-cli-sudoers-submenu`**. **`sudoers` is not a live dispatcher token.**

### 1.1 Human-facing

**In one sentence:** At a real terminal, type `folder-backup` with no extra words to see numbered boards — client-side, self-management, and Exit — with server-side hidden; pick **1** then **17** for grant and drafts; in a pipe or script that same empty run prints help.

| Box | Meaning | Example |
|-----|---------|---------|
| You / this login | Open the boards or pick a number | `folder-backup` then `1` |
| The other role | Scripts and CI must not hang on those boards | `folder-backup </dev/null` → help |
| Not this file | Full command catalog, flags, unknown tokens | `requirement-shell-cli-interface` |

| Includes | Excludes |
|----------|----------|
| Front rows **1** client-side, **8** self-management, **9** Exit | A printed server-side row |
| One hide sentence before the numbers: server-side stays reserved | An empty server loop |
| Client rows **11** backup, **12** restore, **17** sudoers, **0** Back | Restarting a child board at **1** |
| Self rows **81**–**87** and **0** Back | `help`, `menu`, or `main` as a choice |
| Sudoers board body (owned next door) | A live `sudoers` CLI command |
| Bare run on a real terminal | Install-ensure on empty argv |
| `menu` / `main` as the same boards | Test-purpose grant-emit verbs on any numbered board |

| Surface | What you open | What for |
|---------|---------------|----------|
| `./src/folder-backup` | ship unit | live dispatch (empty argv, `menu`, `main`) |
| `folder-backup help` | command | listed verbs including lifecycle |
| `folder-backup` | empty argv | numbered boards on a real terminal |

| You do… | What it means | What you type |
|---------|---------------|---------------|
| Open the boards at a prompt | The program prints **folder-backup**(*live version*) then the front board. On a real terminal the name is bold, the version italic, each short name is bold, and each “what it does” line after the colon is italic and light gray. `--json` on `menu`/`main` is ignored on a real terminal. | `folder-backup` or `folder-backup menu` |
| Pack a folder | Client row **11**, then the folder (one field at a time) or read Next. A finished pick returns to the front board. | `1` then `11` then a source folder path |
| Put an archive back | Client row **12**. | `1` then `12` |
| Set up a grant or draft | Client row **17** opens the sudoers board. Operational rows only. Test commands stay typed. | `1` then `17` |
| Install, version, or update this program | Front row **8**. Local copy is **81**. Channel place is **87**. | `8` then `81` |
| Step back one board | Back on a child board | `0` |
| Leave the menu | Exit on the front board | `9` |
| Run a bare invocation in CI | No prompt. Human help, or JSON help with `--json` and no command. | `folder-backup </dev/null` |

---

## 2. Core Rules / Requirements (Mandatory)

### 2.1 Claim and case

1. This product **claims** a default interactive main menu.  
2. **Case 2** applies: **no** Active specialized zero-argument requirement. Channel verbs do not move empty argv to install-ensure.  
3. This file **owns empty argv**.  
4. Interactive empty argv (`TTY=1`, `$# -eq 0` at `app_main`) **MUST** open the numbered boards (`app_cmd_menu`).  
5. Non-interactive empty argv (`TTY=0`, `$# -eq 0`) **MUST** be **help** (`app_help`). **MUST NOT** prompt. **MUST NOT** install-ensure.  
6. `--json` with **no command token** **MUST** be JSON help (flags-only; `--json` is non-interactive).  
7. Routed-verbs **`menu`** and **`main` MUST** call the same handler. They remain valid ways to open the boards.  
8. `app_main` **MUST** route `menu` / `main` to `app_cmd_menu`. `app_main_menu` **MAY** remain a one-line alias of `app_cmd_menu`.  
9. `requirement-shell-cli-zero-arguments` **MUST** stay **Withdrawn** while this case 2 claim is Active.

### 2.2 Mode check

Measure interactive capability **outside functions** (`TTY=1` only when stdin and stdout are terminals). Helpers consume `TTY` (`requirement-shell-interactive-vs-noninteractive`).

| Invocation | Mode | `--json` | MUST | MUST NOT |
|------------|------|----------|------|----------|
| `folder-backup` (no args) | Interactive (`TTY=1`) | *(none on empty argv)* | Draw the numbered boards | Help; hang; install |
| `folder-backup` (no args) | Non-interactive (`TTY=0`) | *(none)* | **Help** (human) | Draw the menu; hang; install |
| Flags only, no command (e.g. `--json`) | Any | **Follow** | Help: human when JSON=0; JSON help when JSON=1 | Draw the menu |
| `folder-backup menu` or `main` | Interactive (`TTY=1`) | **Ignore** | Draw the numbered boards | Treat as JSON help; hang |
| same | Non-interactive (`TTY=0`) | **Follow** | **Help**: human when JSON=0; JSON help when JSON=1 | Draw the menu; hang; silent return |
| `folder-backup self-management` | Interactive (`TTY=1`) | **Ignore** while drawing | Draw the self board only (§2.3.3) | Draw the front board; hang |
| same | Non-interactive (`TTY=0`) | **Follow** | **Help** | Draw the self board; hang |

While a board is **drawing**, saved `JSON` and `QUIET` **MUST** be forced off, then restored before a leaf runs. `--quiet` off-TTY is still the help path (do not swallow that help). Reuse `app_help` — **MUST NOT** invent a second JSON help catalog.

### 2.3 Boards

0. **Look (mandatory):** every numbered board **MUST** use the default CLI main menu style. Header **MUST** print live `folder-backup(VERSION)` (no space; same Config scalars as `version`) then the board title. On a TTY the name is **bold** and the version *italic*. Each numbered short name **MUST** be **bold** (SGR **1**). Each numbered `explain` **MUST** be *italic* and light gray (SGR **3** + **37**). The number stays unstyled. **Exit** and **Back** have no explain and stay unstyled. Off-TTY / JSON: **plain** — **MUST NOT** emit CSI. Typical helpers: `util_app_ident` then `out_menu_choice`. **MUST NOT** a bare `folder-backup` on that header. **MUST NOT** print `explain` unstyled on a TTY. **MUST NOT** print the short name unstyled on a TTY.  
1. The front board title **MUST** be `numbered list`.  
2. **Parent-prefix integers.** Child boards **MUST NOT** restart at **1**. Hidden numbers **MUST NOT** be compacted.  
3. Printed command-row text **MUST** be `{{short-descript}}: {{explain}}`. Routed leaves use the kept-list human-readable explain. Category rows (`client-side`, `self-management`, family `sudoers`) use this file’s table.  
4. A choice **MUST** be read in the **current shell**. Typical: `prompt_line "Choice"` then `_pick="${_prompt_line}"`. **MUST NOT** `_pick=$(prompt_line …)` / `_pick=$(prompt_ask …)` / `$()` / backticks of **any** function whose body contains `read` (do-not-capture-read / **PP-A-22**). stderr+$() is **not** a license.  
5. An invalid choice **MUST** use `out_error` (not `out_die`, not `out_warn`) and **MUST** reprint **this** layer.  
6. **0** / `back` / `Back` / empty line / EOF on a **child** board is **Back** to the parent. `exit` / `quit` on a child board is the same Back. Child boards **MUST NOT** print Exit **9** and **MUST NOT** print Back **8**.  
7. Front Exit is **9**, **99**, `exit`, `quit`, or EOF, and returns 0. An empty line on the **front** is an invalid retry.  
8. After a valid leaf opened from the front (or from a child of the front), the front board **MUST** redisplay. **MUST NOT** exit the menu on success. **MUST NOT** stay on the launching child board. A leaf that calls `out_die` still ends the process. An empty required backup/restore field prints `Next:` and the front redisplays.  
9. Typing a **listed leaf verb** at the front pick prompt **MUST** run that leaf, then redisplay the front. Typing `sudoers` at the front **MUST** be invalid (open client **1**, then **17**). Typing a child number on the wrong layer **MUST** be invalid.  
10. **`sudoers` is not a live CLI command.** `folder-backup sudoers` **MUST** remain unknown.

#### 2.3.1 Front board

Print the hide sentence **before** the numbers, then the rows below. Row **2** is **not** printed. Typing **2** is an invalid-choice retry. **MUST NOT** add `app_cmd_menu_server`.

Hide sentence (exact): `server-side is hidden: this program does not run a host service. Number 2 stays reserved.`

| # | Token | Label |
|---|-------|-------|
| *(header)* | — | `**folder-backup**(*VERSION*) — numbered list` |
| *(hide)* | — | server-side sentence above |
| 1 | category `client-side` | `client-side: this login's folders: pack, restore, and grants` |
| 8 | category `self-management` | `self-management: this CLI install, version, update, uninstall` |
| **9** | **Exit** | leave the menu |

Front rows **MUST NOT** be `help`, `menu`, `main`, `uninstall`, `where-is-me`, `backup`, `restore`, the five grant/draft verbs, or a test-purpose verb. Channel verbs and diagnostics are board **8**, not front rows. Invalid front text: `Not a menu choice '<pick>'. Type 1, 8, or 9, or a listed name.`

#### 2.3.2 Client board (front **1**)

Header title: `client-side`.

| # | Token | Label |
|---|-------|-------|
| 11 | `backup` | `backup: Pack a named folder into a dated gzip archive under /var/backup/folder-backup` |
| 12 | `restore` | `restore: Put an archive back onto the hard-disk projects tree` |
| 17 | family `sudoers` | `sudoers: Grant and drafts` |
| **0** | **Back** | return to the front board |

Choosing **17** or typing `sudoers` **on this board** **MUST** open the sudoers board (`requirement-shell-cli-sudoers-submenu`). A sudoers **leaf** returns to the **front** board. Sudoers **Back** reprints **this** client board. A backup or restore leaf returns to the **front**. Invalid client text: `Not a menu choice '<pick>'. Type 11, 12, 17, or 0, or a listed command name.`

#### 2.3.3 Self board (front **8**, and verb `self-management`)

Header title: `self-management`. This product is **dual** place: local copy and channel place are both shown. Labels are the kept-list human-readable explains.

| # | Token | Label |
|---|-------|-------|
| 81 | `install` | `install: Copy this program into your bin or /usr/local/bin` |
| 82 | `version` | `version: Show the local version` |
| 83 | `about` | `about: Show diagnostics including sudoers trust tier` |
| 84 | `version-check` | `version-check: Compare this version with the channel` |
| 85 | `self-update` | `self-update: Replace the placed binary from the channel` |
| 86 | `self-uninstall` | `self-uninstall: Remove the channel-placed binary` |
| 87 | `self-install` | `self-install: Copy this file, or download it when the shell is a pipe` |
| **0** | **Back** | return to the caller |

`uninstall` and `where-is-me` stay **typed commands**. Their absence is **not** a hide cause (no hide sentence on this board). **81** is the local copy. **87** is the channel place. **86** is channel remove, not local `uninstall`.

Opened from the front, Back **and** a finished leaf both return to the front. Opened as the argv verb `self-management`, the self board is the whole session: a leaf runs and the verb ends; Back ends the verb; the front board is not opened. Handler: `app_cmd_menu_self`. `app_default_self_loop` **MUST** be a one-line alias of that function. Invalid self text: `Not a menu choice '<pick>'. Type 81-87, 0 back, or a listed command name.`

### 2.4 Sudoers board (owned elsewhere)

Client row **17** / `sudoers` **MUST** open the grant/draft board. Membership, reserved numbers **171** / **173** / **174**, Back **0**, the hide sentence, and the live-verb rule **MUST** follow **`requirement-shell-cli-sudoers-submenu`**. This file **MUST NOT** restate that table as a second SSOT.

### 2.5 Implementation Notes (this product)

| Item | Value |
|------|--------|
| **Product** | `folder-backup` |
| **Claimed** | yes |
| **Case** | **2** (no Active zero-arg REQ; channel verbs explicit; empty argv is still these boards) |
| **Empty argv owner** | **this file** (TTY boards; off-TTY help) |
| **Menu verbs** | empty argv on TTY; `menu` (preferred named); `main` alias |
| **Handlers** | `app_cmd_menu` · `app_cmd_menu_client` · `app_cmd_menu_self` · `app_cmd_menu_sudoers` |
| **Aliases** | `app_main_menu` → `app_cmd_menu`; `app_default_self_loop` → `app_cmd_menu_self` |
| **Family row** | client **17** `sudoers` — menu-only; **not** dispatched; body **`requirement-shell-cli-sudoers-submenu`** |
| **Ship unit** | Implemented — `app_main` empty argv and `menu` / `main` call `app_cmd_menu` |
| **Kept list** | `reviews/cli-routed-verb-table.md` |
| **Look** | Default CLI main menu style — header `folder-backup(VERSION)`; TTY bold short name; TTY italic + light-gray explain; `out_menu_choice` / `util_app_ident` |
| **Choice read** | Current-shell `prompt_line` → `_prompt_line` (not `$()`) |
| **Front** | **1** / **8** / **9**; number **2** reserved and hidden |
| **Exit** | **9** on the front only |
| **Back** | **0** on child boards |
| **Test-purpose (this product)** | `print-sudoers`, `print-sudoers-install-script`, `generate-sudoer-request` — off **every** numbered board |
| **Typed-only (not a hide cause)** | `uninstall`, `where-is-me` |
| **Withdrawn peer** | `requirement-shell-cli-zero-arguments` |
| **Honesty** | **Implemented** (suite **TP-CLI-13** · **TP-CLI-14** · **TP-CLI-16** · **TP-CLI-18** · **TP-CLI-19** · **TP-CLI-21**) |

**Normative front draft** (README / this fence use markdown emphasis; the live TTY uses SGR, never paste CSI here):

```text
[INFO] **folder-backup**(*VERSION*) — numbered list
[INFO] server-side is hidden: this program does not run a host service. Number 2 stays reserved.
1. **client-side**: *this login's folders: pack, restore, and grants*
8. **self-management**: *this CLI install, version, update, uninstall*
9. Exit
```

**Invocation samples (CI-M1a):**

```text
folder-backup
folder-backup menu
folder-backup main
folder-backup menu --json
folder-backup self-management
folder-backup generate-sudoer-request
folder-backup submit-sudoer-request
folder-backup print-sudoers
folder-backup print-sudoers-install-script
folder-backup remove-project-sudoers
```

On a real terminal the first three **MUST** show the front board. `folder-backup menu --json` on a real terminal **MUST** still show the front board. `folder-backup self-management` on a real terminal **MUST** show the self board and **MUST NOT** show the front board first. Off-TTY, `folder-backup`, `folder-backup menu`, and `folder-backup self-management` **MUST** call help; `folder-backup --json` and `folder-backup menu --json` **MUST** call JSON help. The five grant/draft names **MUST** run as live commands. `folder-backup sudoers` **MUST** fail as unknown.

### 2.6 Why This Requirement Exists (CIAO)

- **Principle 2 – Intentional**: Empty argv has one owner (this file, case 2). Daily folder work is client-side. This program’s own place and update is self-management. There is no host service, so server-side stays reserved.  
- **Principle 1 – Caution**: Scripts do not hang; empty argv never install-ensure.  
- **Principle 16 – Interactive vs non-interactive**: TTY vs pipe is explicit.  
- **Principle 10 – Least privilege**: `sudoers` is not a dispatcher token. Test-purpose grant-emit stays off every numbered board.

---

## Under command line for normal user only

When this program runs on Termux, Git Bash, Windows Command Prompt, or the same class, **admin privilege** and **dedicated system user privilege** stay unused. **This requirement:** the numbered boards may still show backup/restore; picking them **MUST** fail closed on that class rather than wrapping `sudo`.

| MUST | MUST NOT |
|------|----------|
| Numbered boards as this login | Hang; install-ensure on empty argv |
| Git Bash / Windows cmd: no Termux `pkg` | Treat WSL as this class |

Detect (typical): Termux — `PREFIX` contains `com.termux` or `TERMUX_VERSION` is set. Git Bash — `MSYSTEM` is `MINGW*` / `MSYS*`. Windows cmd — `OS=Windows_NT` and `COMSPEC` names `cmd.exe` after excluding Git Bash, Cygwin, and WSL.

**Implementation Notes:** ship unit has **no detect helper yet** (Gap).

## 3. Design Principles (CIAO / CIAO-Lite)

- **Caution:** Do not hang off-TTY; do not steal empty argv for install.  
- **Intentional:** Case 2; labels from the kept list; domain categories use parent-prefix numbers.  
- **Anti-fragile:** `menu` / `main` open the same boards as a bare TTY run; Back **0** returns to the parent; a finished leaf redisplays the front.  
- **Over-protect:** Test-purpose verbs stay off every numbered board; front Exit is **9**; child Back is **0**; number **2** stays reserved.

---

## 4. Protection Rule (Sacred)

**Future AI assistants, Grok, or maintainers MUST NOT**:

1. Restore Type N always-help on empty argv while this case 2 claim is Active (do not reactivate `requirement-shell-cli-zero-arguments`).  
2. Attach empty argv to install-ensure (Type O).  
3. Invent menu labels instead of `command: what it does` from the kept list (category rows use this file’s table).  
4. Put `help`, `menu`, `main`, `uninstall`, `where-is-me`, or a test-purpose verb (`print-sudoers`, `print-sudoers-install-script`, `generate-sudoer-request`) on any numbered board.  
4b. Put `backup`, `restore`, or the five grant/draft verbs on the **front** board.  
4c. Restate sudoers-board membership as a second SSOT here (that table lives on **`requirement-shell-cli-sudoers-submenu`**).  
4d. Print a server-side row, or add `app_cmd_menu_server`, while this program has no host service.  
5. Restart a child board at **1**, compact a hidden number, number front Exit as anything other than **9**, or print Back **8** / Exit **9** on a child board. Child Back **MUST** be **0**.  
6. Draw the menu in non-interactive mode (including off-TTY empty argv and off-TTY `self-management`).  
7. Treat interactive `folder-backup menu --json` as JSON help.  
8. Drop `menu`/`main` routing after attaching the boards to empty argv.  
9. Auto-write `/etc` from a menu choice (print/submit stay Type 0 drafts).  
10. Claim the ship unit lacks the TTY empty-argv menu while `app_main` routes empty argv to `app_cmd_menu`.  
11. Capture the menu choice (or extra field) with `$()` / backticks of a `read` helper, or “fix” a freeze by stderr+$() (**PP-A-22** / do-not-capture-read).  
12. Print a board header as a bare `folder-backup` without live `VERSION`, or unstyled on a TTY.  
13. Draw a numbered board off the default CLI main menu style — short name **MUST** be bold on a TTY; `explain` **MUST** be *italic* and light gray. **MUST NOT** emit CSI off-TTY.  
14. Wire `sudoers` as a live `app_main` command.  
15. Exit the menu after a successful leaf, or stay on the child board that launched it, when the operator entered from the front.  
16. Treat `uninstall` / `where-is-me` absence from the self board as a hide cause.

**Violating this rule is a critical dispatcher / hang / honesty / look regression.**

---

## 5. Acceptance criteria

| ID | Criterion |
|----|-----------|
| AC-1 | Non-interactive empty argv is help (not install, not the numbered boards) |
| AC-2 | Case 2 recorded; this file owns empty argv; `menu` / `main` named and routed to `app_cmd_menu` |
| AC-3 | Interactive empty argv **and** interactive `menu` draw the front board: hide sentence, **1** client-side, **8** self-management, **9** Exit; row **2** is not printed |
| AC-4 | Interactive `menu --json` still draws the front board |
| AC-5 | Non-interactive `menu` is help; `--json` is JSON help; non-interactive `self-management` is help |
| AC-6 | No numbered board lists `help`, `menu`, `main`, `uninstall`, `where-is-me`, or a test-purpose verb. The front board does not list backup, restore, or the five grant/draft verbs |
| AC-7 | Leaf labels match kept-list human-readable `verb: explain`; category explains are this file’s tables |
| AC-8 | TTY header is live `folder-backup(VERSION)` with bold name and italic version; short name is bold; numbered `explain` is italic and light gray; number, Exit, and Back stay unstyled; no CSI off-TTY |
| AC-9 | Menu choice is current-shell `prompt_line` / `_prompt_line`; **MUST NOT** `$()` a `read` helper |
| AC-10 | Client **1** lists **11** / **12** / **17** / **0** Back. Choosing **17** opens the sudoers board (**`requirement-shell-cli-sudoers-submenu`**). Self **8** lists **81**–**87** and **0** Back |
| AC-11 | `folder-backup sudoers` is unknown. Typing `sudoers` on the front is invalid. The five grant/draft names remain live CLI verbs |
| AC-12 | A finished leaf redisplays the front board. An invalid child number reprints the front board. Typing **2** is an invalid retry |

---

## 6. Related requirements (peer keys only)

| Key | Relationship |
|-----|--------------|
| `requirement-shell-cli-zero-arguments` | **Withdrawn** predecessor (Type N always-help) |
| `requirement-shell-cli-interface` | Dual mention: empty argv row + `menu` / `main` + self board on the command table |
| `requirement-shell-cli-sudoers-submenu` | Client row **17** / `sudoers`; sudoers-board membership and live-verb rule |
| `requirement-shell-interactive-vs-noninteractive` | `TTY`; no hang |
| `requirement-shell-output-requirements` | `out_*`; bold short name; reuse `app_help` |
| `requirement-shell-local-self-management` | `install` is self row **81**; `uninstall` / `where-is-me` stay typed |
| `requirement-domain-folder-backup` | Grant-emit verbs; help apart; off every numbered board |
| `docs/requirements/index.md` | Registry |

---

## Design-time verification

| TP family / ID | Suite | Status |
|----------------|-------|--------|
| **TP-CLI-07** | `tests/test_cli.sh` | **have** — off-TTY empty argv is help, not install (AC-1) |
| **TP-CLI-13** | `tests/test_cli.sh` | **have** — front board, client **11**/**12**/**17**, sudoers **172**/**175**, hide sentences, `sudoers` not dispatched (AC-3 / AC-10 / AC-11) |
| **TP-CLI-14** | same | **have** — interactive `menu --json` still prints the front board (AC-4) |
| **TP-CLI-15** | same | **have** — non-interactive empty argv and `menu` are help; `--json` JSON help (AC-5) |
| **TP-CLI-16** | same | **have** — front shows **8** self-management and omits verb rows that belong on a child board (AC-6) |
| **TP-CLI-18** | same | **have** — bold short name, italic light-gray explain, unstyled Exit, sudoers title (AC-8) |
| **TP-CLI-19** | same | **have** — child number **12** on the front is invalid and the front reprints (AC-12) |
| **TP-CLI-21** | same | **have** — typing `version` on the front runs it and the front reprints (AC-12) |

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
| 2026-09-03 | Active 1.5.0 | Family row **sudoers** + submenu (five grant/draft setup verbs remain live CLI commands; Back 8 / Exit 9); main **N = 3**; `sudoers` not dispatched |
| 2026-09-03 | Active 1.6.0 | Submenu **body** moved to **`requirement-shell-cli-sudoers-submenu`**; this file keeps the family row on the start list |
| 2026-09-30 | Active 1.6.1 | Channel verbs are live and stay off the flat list. Case 2 still owns empty argv. |
| 2026-09-30 | Active 1.7.0 | Hierarchical boards: front **1** / hidden **2** / **8** / **9**; client **11**/**12**/**17**; self **81**–**87**; child Back **0**; finished leaf redisplays the front; short name bold |

---

**Last Updated**: 2026-09-30  
**Owner**: project maintainers  
**Alignment**: Registry `docs/requirements/index.md`; **CIAO** (https://github.com/cloudgen/ciao); CIAO-Lite (https://github.com/cloudgen/ciao-lite).
