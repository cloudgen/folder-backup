# What to review — folder-backup

**Living checklist** (review plan). Product: **folder-backup** local self-managed CLI + domain backup/restore + narrow sudo deposit.  
**Class:** software-development · domain SSOT present · **local-only** install channel (online package intentionally absent).  
**Always load first:** `reviews/lessons.md`

**Last plan update:** 2026-08-17  
**Ship unit VERSION:** 1.8.2  
**Suite baseline:** PASS=209 FAIL=0 SKIP=2 (see `reviews/test-plan.md`)

---

## Pre-flight

| # | Check | Notes |
|---|--------|--------|
| P1 | Read `docs/requirements/index.md` | Class + architecture + shell + domain + three-layer |
| P2 | Confirm ship unit `src/folder-backup` | `APP_NAME` / `VERSION` hard-assign (**1.8.2+**) |
| P3 | Load `reviews/lessons.md` and re-check every open L-* | Mandatory (esp. **L-SUDOERS-01/02** · **L-SUDOERS-06** · **L-TEST-REVIEW-01**) |
| P4 | Run `./tests/run.sh` | Record PASS/FAIL/SKIP in report; **must include TP-22e/22f** (not emit-only 22) |
| P5 | Confirm install **channel** still local-only | No SCRIPT_URL product UX |
| P6 | Privilege law version | `requirement-three-layer-privilege-model` **≥1.8.0** (S13 + inbound **AC-21** + host-probe **AC-22**) · `requirement-sudoer-json-file` **≥1.1.0** §2.7a |
| P7 | Host elev posture (if reviewing runtime) | Global vs local binary; trust tier; `/etc/sudoers.d/` status |
| P8 | **JSON re-encode / inbound fidelity** | Complete section below. **Revise/Block** if skipped when submit or JSON grant is in scope. |
| P9 | **Host fragment → submit update** | This user’s `/etc/sudoers.d` dest present → default **update**; TP-**23/23b**. **Revise/Block** if skipped when submit is in scope. |

---

## Product law surfaces

| Surface | Path | Review focus |
|---------|------|--------------|
| Class | `requirement-class-software-dev.md` | posix-sh, local-only residual |
| Bootstrap chain | `requirement-bootstrap-chain.md` | A=cli-template → B domain extend |
| Project folder | `requirement-project-folder.md` | `src/`, bins, `/var/backup` |
| **Privilege / sudoers** | `requirement-three-layer-privilege-model.md` | Type 0/1; **trust tiers S13**; print-sudoers; **install-script**; **remove-project-sudoers**; **submit-sudoer-request** public inbound; **inbound fidelity AC-21**; **host-probe add/update AC-22**; no ALL ALL |
| **JSON sudoer file** | `requirement-sudoer-json-file.md` | `folder-backup` backup/**and** restore; §2.7a re-encode; pretty JSON legal |
| CLI interface | `requirement-shell-cli-interface.md` | Commands, flags, dispatch |
| Empty argv Type N | `requirement-shell-cli-zero-arguments.md` | Empty = help |
| Local self-management | `requirement-shell-local-self-management.md` | install/uninstall; global preferred for elev |
| Output SSOT | `requirement-shell-output-requirements.md` | `out_*`; JSON errors |
| Modular design | `requirement-shell-modular-function-design.md` | `fb_*` domain prefix |
| Idempotency | `requirement-shell-idempotency.md` | Re-install; next-N archives |
| Interactive modes | `requirement-shell-interactive-vs-noninteractive.md` | Uninstall / remove-project-sudoers confirm |
| CLI storage | `requirement-shell-cli-storage.md` | Per-user staging isolation |
| Domain | `requirement-domain-folder-backup.md` | Four pillars; sudoers verbs |
| Ops backup | `requirement-folder-archive-backup.md` | backup/restore/verify |

**Harness (not product source law — load when elev/agent create in scope):**

| Surface | Path | Review focus |
|---------|------|--------------|
| Skill create sudoers | `docs/skills/skill-create-sudoers-file.md` | **S11–S13** + **S14** emit + **S15** convert/inbound |
| Checklist | `docs/templates/checklists/checklist-create-sudoers-security.md` | Pre-emit **S14** and submit/convert **S15** |
| Mold | `docs/templates/requirements/template-three-layer-privilege-model.md` | §2.3.1a, §2.3.3a/b |
| Terms | `project-sudoers-file` · `sudoers-fragment` | Draft vs installed vs admin script |

**Intentionally absent (do not “restore” without owner order):** online-install, remote self-management, companion channel checksum.

---

## High-risk paths (ship unit)

| Path / symbol | Risk | Lesson / TP |
|--------------|------|-------------|
| Empty argv branch | Type O install leak from parent | L-TYPE-N-01 · TP-CLI-07 |
| Online command names | Half-live channel | L-ONLINE-01 · TP-CLI-04/10 |
| `inst_local_uninstall` | Fake success without force | L-UNIN-01 · TP-LC-05 |
| `inst_local_install` | Mode `0711` / `chmod +x` only (non-owners cannot run shell unit) | L-INST-MODE-01 · TP-LC-09/10 |
| `fb_deposit_archive` | Silent success without sudo | L-DEPOSIT-01 · TP-FOLDER-BACKUP-05 |
| `fb_print_sudoers` | Broad sudoers; Type 0 `/etc` write; local=production | L-SUDOERS-01/02 · TP-01/01b/02 |
| `fb_print_sudoers_install_script` | Silent `/etc` install; wrong user in fragment; shared installed basename overwrites other users | TP-FOLDER-BACKUP-14 · user=`id -un` · L-SUDOERS-04 |
| `fb_remove_project_sudoers` | Deletes `/etc` as Type 0; silent without force; multi-draft wrong pick | TP-FOLDER-BACKUP-15 · **15b** |
| `fb_next_archive_name` | Overwrite archives | L-OVERWRITE-01 · TP-08 |
| `util_resolve_storage` | Isolation break / stage mismatch vs sudoers | L-STOR-01 · TP-CLI-12 · TP-02 |
| `fb_detect_sudoer_inbound` | Home-only `sudoer-approving` or Type 0 inbound `mkdir` | L-INBOUND-01 · TP-FOLDER-BACKUP-21/21b |
| `fb_submit_sudoer_request` / sibling decode | Pretty JSON inbound restore-only; `[OK]` / S14 / stub `cp` treated as fidelity | L-SUDOERS-06 · L-TEST-REVIEW-01 · TP-22e/22f · S15 |
| `fb_submit_sudoer_request` / host dest probe | Default **add** while `/etc/sudoers.d/{{APP_NAME}}-<user>` exists; other users’ fragments flipping action | TP-23/23b · AC-22 |
| Config `HOME` under `set -u` | nounset crash | L-SETU-01 · TP-CLI-11 |

---

## Type 1 elevation + project-sudoers-file — review plan gate

Product claims **narrow Type 1** (allowlisted OS tools only), **not** full host package Type 1.  
**User lines** in fragments are generated from **`id -un`** (invoking login); stage roots are per-user.

| Gate | Requirement | TP |
|------|-------------|-----|
| Negative fail-closed deposit | Without working allowlisted sudo → non-zero + hint | TP-FOLDER-BACKUP-05 |
| No Type 0 `/etc` auto-write | print-sudoers / install-script only emit draft+script | TP-01, 14 |
| Fragment narrowness | No `NOPASSWD: ALL` | TP-02 |
| Trust tier test_local | Refuse emit without `--allow-test-local`; TEST MODE banner | TP-01, **01b** |
| Admin install-script | Draft refresh + `/dev/shm` script; install needs root | TP-**14** |
| Remove draft only | Confirm/`--force`; refuse `/etc`; warn host fragment; multi-draft list/choose | TP-**15** · **15b** |
| Per-user host path | Installed `/etc/sudoers.d/{{APP_NAME}}-<user>` (no multi-user overwrite) | TP-**14** · L-SUDOERS-04 |
| Positive full deposit | Host sudoers or root | TP-07/08 |
| Installed fragment ≠ draft only | Host may still elevate after draft removed | Honesty in remove-project-sudoers |
| **JSON re-encode / inbound verbs** | Pretty emit through real sibling still has `backup` **and** `restore`; `[OK]` / purpose / S14 / stub `cp` are not enough | **§ JSON re-encode** · TP-**22e/22f** · S15 · AC-21 |
| **Host fragment → submit update** | This user’s `/etc/sudoers.d/{{APP_NAME}}-<user>` (or legacy) exists → default **update**; else **add**; `--add`/`--update` override; other users ignored | TP-**23/23b** · AC-22 |
| Full interactive password-sudo package ladder | **n/a** | deposit uses `sudo -n` after admin fragment |
| TTY package Type 1 traps | **n/a** | no package elev claimed |

**CL-SHELL-TTY-PRIVILEGE-TRAPS:** N/A for package elevation.  
**CL-CREATE-SUDOERS-SECURITY:** Required for agent-authored fragment create (**S11–S15**). **S15** required when `submit-sudoer-request` or sibling convert is in scope.

## JSON re-encode / inbound fidelity — review plan gate

**In scope when:** `print-sudoers`, `submit-sudoer-request`, JSON sudoer file, or sibling `sudoer-cli` convert/submit is reviewed.  
**Incomplete review (Revise/Block, not Pass)** if this section is skipped while in scope.  
**Law:** `requirement-sudoer-json-file` §2.7a · three-layer AC-21 · **S15** · INC-20260817-001 · L-SUDOERS-06 · L-TEST-REVIEW-01.

Reviewer **MUST** do the checks (suite green on emit-only TPs is **not** this gate):

| ID | Check | Pass | Fail |
|----|--------|------|------|
| **JR-1** | Emit dual lists **both** verbs | `print-sudoers` text + `.json` have `backup` and `restore` | Only one verb, or purpose lists both while `commands` has one |
| **JR-2** | Pretty JSON is a fixture | Grant used for convert/submit is indented / `}, {` (not only compact `},{`) | Only minified encoder output was reviewed |
| **JR-3** | Sibling **convert** | `sudoer-cli json-to-sudoers --file <pretty-dual>` has **two** Cmnd lines (`… backup` and `… restore`) | Last-`args` only (typical: restore-only) |
| **JR-4** | Sibling **submit** (real binary, test `--queue-root` / env inbound) | Queued inbound `commands[].args` still contains both verbs | Inbound is compact restore-only; purpose unchanged |
| **JR-5** | Readable inbound verify | Product submit fail-closed if inbound is readable and a verb is missing | `[OK] submitted` with a collapsed body |
| **JR-6** | Suite | **TP-FOLDER-BACKUP-22e** and **22f** **have** and ran this review | Only 22/22b/22c/20 file-count; stub `cp` treated as fidelity |
| **JR-7** | Checklist | **S15** Pass or N/A with reason; **S14 is not S15** | S14 Pass cited as inbound proof |
| **JR-8** | Host queue (if a live request exists) | `sudo cat` inbound; count `commands`; do not approve a collapsed grant | Approve because purpose says “backup and restore” |

**Must not confuse:**

| Not this gate | Why |
|---------------|-----|
| TP-22 substring `"backup"` on the **draft** `.json` | Emit, not queued body |
| TP-20 stub `sudoer-cli` that `cp`s the file | Never runs `sr_json_decode_to_fields` |
| Purpose string / request_id / `[OK]` | Not `commands[]` |
| Compact-only sibling suite (TP-SR-03/07) | Hides `},{` splitter |

**Non-finding if:** JR-3/JR-4 pass on pretty emit **and** TP-22e/22f are have **and** no live inbound is restore-only while purpose lists both.

### Trust tier (S13) — must re-check

| Tier | Binary | Emit print-sudoers | Production claim |
|------|--------|--------------------|------------------|
| production | Global managed present | Without allow flag | Allowed after review |
| test_local | Local only | Needs `--allow-test-local` + warnings | **Forbidden** |
| unmanaged | Neither | Needs allow + warnings | **Forbidden** |

---

## Tests surface

| Check | Path |
|-------|------|
| Suite entry | `tests/run.sh` |
| CLI | `tests/test_cli.sh` |
| Local lifecycle | `tests/test_local_lifecycle.sh` |
| Domain + privilege | `tests/test_domain_folder_backup.sh` |
| TP map | `reviews/test-plan.md` |
| REQ ↔ TP matrix | `reviews/requirement-test-matrix.md` |

---

## Product user docs

| Check | Path |
|-------|------|
| README install + sudoers handoff honesty | `README.md` |
| SECURITY trust tiers + install-script | `SECURITY.md` |
| CHANGELOG current VERSION | `CHANGELOG.md` |

---

## Explicit non-goals for default review

- Online install / curl\|sh channel  
- Companion `.sha256` channel integrity  
- Full root package Type 1 elevation suite  
- Cloud upload domain  
- Auto-writing `/etc/sudoers.d` from Type 0 or agents  
