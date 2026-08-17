# Requirement ↔ test matrix — folder-backup

**Updated:** 2026-08-17 (1.8.1)  
**Product VERSION:** 1.8.1  
**Suite:** `tests/run.sh` (PASS=189 FAIL=0 SKIP=2)

| Requirement key | Area | TP families | Coverage notes |
|-----------------|------|-------------|----------------|
| requirement-class-software-dev | class | TP-CLI-01, TP-CLI-11 | Syntax + stack residual; no online package |
| requirement-bootstrap-chain | architecture | TP-CLI-04, TP-CLI-10 | Online surface absent |
| requirement-project-folder | architecture | TP-LC-01, TP-FOLDER-BACKUP-06 | src ship unit; deposit path naming |
| requirement-three-layer-privilege-model | architecture | TP-FOLDER-BACKUP-01, **01b**, 01c, 02, 05, **14**, **15**, **15b**, **19**, **20**, **21**, **21b**, **22e** | Trust tiers **S13**; submit **workflow** AC-16–19; inbound **fidelity** AC-21 |
| requirement-sudoer-json-file | architecture | TP-FOLDER-BACKUP-**22**, **22b**, **22c**, **22e**, **22f** | JSON grant is `folder-backup` backup/restore only; pretty emit + inbound body keep both verbs |
| requirement-folder-archive-backup | backup | TP-FOLDER-BACKUP-03..08, 10..13, **16** | Source/name/deposit/verify/next-N/**restore** + dest whitelist W-ETC-USER (ops SSOT) |
| requirement-folder-archive-backup-retention-total | backup | TP-FOLDER-BACKUP-17, 17b | Max **30** per basename; oldest-first prune |
| requirement-folder-archive-backup-retention-daily | backup | TP-FOLDER-BACKUP-18, 18b | Max **5** per basename per day; lowest-`N` same-day prune |
| requirement-shell-cli-interface | shell | TP-CLI-* | Commands, flags, dispatch (incl. new sudoers verbs) |
| requirement-shell-cli-zero-arguments | shell | TP-CLI-07 | Type N help |
| requirement-shell-local-self-management | shell | TP-LC-* (incl. **09/10** mode) | install/uninstall/where-is-me; **0755** multi-user; global preferred for elev |
| requirement-shell-output-requirements | shell | TP-CLI-03,05,08,09 | JSON / quiet / errors |
| requirement-shell-modular-function-design | shell | (indirect) | `fb_print_sudoers*`, `fb_remove_project_sudoers`, deposit/restore |
| requirement-shell-idempotency | shell | TP-LC-03,07 · TP-FOLDER-BACKUP-06,08 | Re-install; next-N |
| requirement-shell-interactive-vs-noninteractive | shell | TP-LC-05 · TP-FOLDER-BACKUP-15 · **15b** | Uninstall / remove-project-sudoers confirm; multi-draft non-interactive path required |
| requirement-shell-cli-storage | shell | TP-CLI-12 · domain staging | Isolation + per-user stage roots |
| requirement-domain-folder-backup | domain | TP-FOLDER-BACKUP-01,02,09,14,15,19,20,**21**,**21b** · TP-CLI-04,06 | Surface verbs/help/about; submit public inbound |

**Checklist / mold (harness, not product suite):** **S11–S12** elev tables (when claimed); **S13** trust tier — agent path `SK-CREATE-SUDOERS-FILE` / `CL-CREATE-SUDOERS-SECURITY`.

**Absent by design (no TP Core):** online-install, remote self-management, automatic channel checksum.
