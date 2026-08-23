# Requirement ↔ test matrix — folder-backup

**Updated:** 2026-08-23 (1.11.0)  
**Product VERSION:** 1.11.0  
**Suite:** `tests/run.sh` (PASS=242 FAIL=0 SKIP=2)

| Requirement key | Area | TP families | Coverage notes |
|-----------------|------|-------------|----------------|
| requirement-class-software-dev | class | TP-CLI-01, TP-CLI-11 | Syntax + stack residual; no online package |
| requirement-bootstrap-chain | architecture | TP-CLI-04, TP-CLI-10 | Online surface absent |
| requirement-project-folder | architecture | TP-LC-01, TP-FOLDER-BACKUP-06 | src ship unit; deposit path naming |
| requirement-three-layer-privilege-model | architecture | TP-FOLDER-BACKUP-01, **01b**, 01c, 02, 05, **14**, **15**, **15b**, **19**, **20**, **21**, **21b**, **22e**, **23**, **23b**, **23c**, **24**, **24b**, **24c**, **24d**, **26**, **26b** | Trust tiers **S13**; submit workflow AC-16–22; independent generate AC-23/24; inbound fidelity; host-probe add/update; other-user dest ignored; **backup \*** emit AC-25 |
| requirement-sudoer-json-file | architecture | TP-FOLDER-BACKUP-**22**, **22b**, **22c**, **22d**, **22e**, **22f**, **24**, **24c**, **24d**, **26**, **26b** | JSON grant is `folder-backup` backup/restore plus `*`; pretty emit + inbound body keep both verbs; independent generate dest readable; OS-tool submit refuse |
| requirement-folder-archive-backup | backup | TP-FOLDER-BACKUP-03..08, 10..13, **16** | Source/name/deposit/verify/next-N/**restore** + dest whitelist W-ETC-USER (ops SSOT) |
| requirement-folder-archive-backup-retention-total | backup | TP-FOLDER-BACKUP-17, 17b, **17c** | Max **30** per basename; oldest-first prune; failed backup does not prune |
| requirement-folder-archive-backup-retention-daily | backup | TP-FOLDER-BACKUP-18, 18b, **18c** | Max **5** per basename per day; lowest-`N` same-day prune; failed backup does not prune |
| requirement-shell-cli-interface | shell | TP-CLI-* | Commands, flags, dispatch (incl. new sudoers verbs); **menu/main** TP-CLI-13..16; test-purpose grant-emit apart |
| requirement-shell-cli-zero-arguments | shell | TP-CLI-07 | Type N help |
| requirement-shell-cli-default-interaction | shell | TP-CLI-07, **13**, **14**, **15**, **16** | Case 3 `menu`/`main`; colon labels; empty argv stays help; version/about/self-managed/test-purpose omitted |
| requirement-shell-local-self-management | shell | TP-LC-* (incl. **09/10** mode) | install/uninstall/where-is-me; **0755** multi-user; global preferred for elev |
| requirement-shell-output-requirements | shell | TP-CLI-03,05,08,09 | JSON / quiet / errors |
| requirement-operator-readable-error | shell | TP-FOLDER-BACKUP-**25**, **25b**, **25c** | Operator-facing `[ERROR]` wording (what happened / next step / no jargon-only) |
| requirement-shell-modular-function-design | shell | (indirect) | `fb_print_sudoers*`, `fb_remove_project_sudoers`, deposit/restore |
| requirement-shell-idempotency | shell | TP-LC-03,07 · TP-FOLDER-BACKUP-06,08 | Re-install; next-N |
| requirement-shell-interactive-vs-noninteractive | shell | TP-LC-05 · TP-FOLDER-BACKUP-15 · **15b** | Uninstall / remove-project-sudoers confirm; multi-draft non-interactive path required |
| requirement-shell-cli-storage | shell | TP-CLI-12 · domain staging | Isolation + per-user stage roots |
| requirement-domain-folder-backup | domain | TP-FOLDER-BACKUP-01,02,09,14,15,19,20,**21**,**21b**,**23**,**23b**,**24** · TP-CLI-04,06 | Surface verbs/help/about; submit public inbound; generate-sudoer-request; host-probe add/update |

**Checklist / mold (harness, not product suite):** **S11–S12** elev tables (when claimed); **S13** trust tier; **S14** emit; **S15** convert/inbound; **S16** independent generate dest — agent path `SK-CREATE-SUDOERS-FILE` / `CL-CREATE-SUDOERS-SECURITY`. Operator errors: `SK-OPERATOR-READABLE-ERROR` / `CL-OPERATOR-READABLE-ERROR`.

**Absent by design (no TP Core):** online-install, remote self-management, automatic channel checksum.
